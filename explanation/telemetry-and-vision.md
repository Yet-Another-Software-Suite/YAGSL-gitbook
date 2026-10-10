---
description: Telemetry verbosity, simulation, and fusing vision pose estimates
---

# Telemetry, Simulation & Vision

## Telemetry

YAMS pushes swerve drive and module data to NetworkTables under `Mechanisms/<name>`, where `<name>` is the telemetry name you pass to `SwerveDriveConfig.withTelemetry(name, ...)`. This documentation and the YAGSL examples use `swerve`, so the paths below start with `Mechanisms/swerve`. This is the data most dashboards (Shuffleboard, Elastic, AdvantageScope) read to render swerve widgets. See the [AdvantageScope how-to guide](../how-to/set-up-advantagescope.md). Every one of these fields is also written to a WPILib DataLog automatically. See [How to view your DataLog in AdvantageScope](../how-to/view-your-datalog-in-advantagescope.md) to look at a recorded run after the fact.

<figure><img src="../.gitbook/assets/yagsl-telemetry.png" alt=""><figcaption><p>The Mechanisms/swerve NetworkTables tree as seen in AdvantageScope.</p></figcaption></figure>

```
Mechanisms/swerve/
├── gyro                    (degrees)
├── loopTime                (seconds)
├── pose                    (Pose2d struct)
├── chassis/
│   ├── current             (ChassisVelocities struct, robot-relative, measured)
│   ├── desired              (ChassisVelocities struct, robot-relative, commanded)
│   └── field                (ChassisVelocities struct, field-relative)
├── states/
│   ├── current              (SwerveModuleVelocity[] struct array, measured)
│   └── desired               (SwerveModuleVelocity[] struct array, commanded)
└── modules/<name>/
    ├── encoder              (degrees, the module's raw absolute encoder reading)
    ├── drive/                (that module's drive SmartMotorController telemetry)
    │   ├── current/{stator, supply}            (amps)
    │   └── measurement/{position, velocity}    (meters, meters per second)
    └── azimuth/              (that module's angle/steering SmartMotorController telemetry)
        ├── current/{stator, supply}            (amps)
        ├── mechanism/{position, velocity}      (rotations, rotations per second)
        └── externalencoder/{position, velocity} (rotations, rotations per second)
```

{% hint style="info" %}
Units follow one rule in YAMS: read-only values are published in rotations and rotations per second (meters and meters per second for linear values such as the drive wheel), while values you can edit for live tuning are in degrees and RPM (meters and meters per second for linear values).
{% endhint %}

{% hint style="warning" %}
`modules/<name>/encoder` is the raw absolute encoder reading, in degrees. This is the value you read to determine `absoluteEncoderOffset` for that module. It sits directly under the module's table, as a sibling of `drive` and `azimuth`, not nested inside either of them.
{% endhint %}

How much gets published is controlled by `yams.core.telemetry.enums.TelemetryVerbosity`:

```java
public enum TelemetryVerbosity {
  LOW,   // minimal telemetry
  MID,   // moderate telemetry
  HIGH   // full swerve drive + module data
}
```

{% hint style="info" %}
This enum replaces the old `NONE`/`LOW`/`INFO`/`POSE`/`HIGH`/`MACHINE` levels from pre-2026 YAGSL. If you're migrating an older robot project, map your old verbosity choice down to the nearest of `LOW`/`MID`/`HIGH`. See [Schema Changes](../reference/schema-changes.md).
{% endhint %}

```java
var cfg = new SwerveDriveConfig()
    .withSubsystem(this) // .withMechanism(this) for Commands v3
    .withTelemetry("swerve", new SwerveDriveTelemetryConfig(TelemetryVerbosity.HIGH));

SwerveParser.parse(directory);
drive = SwerveParser.createSwerveDrive(cfg);
```

`withTelemetry("swerve", TelemetryVerbosity.HIGH)` is the short form. Passing a `SwerveDriveTelemetryConfig` (`yams.core.telemetry.SwerveDriveTelemetryConfig`) also lets you choose the DataLog name with `.withDataLogName(...)`; with the short form, YAGSL logs under `Swerve/`.

Telemetry is only published while your code calls `drive.updateTelemetry()` every loop: from the subsystem's `periodic()` in Commands v2, or from `robotPeriodic()` in Commands v3, where a `Mechanism` has no `periodic()` of its own.

{% hint style="warning" %}
Higher telemetry verbosity can induce some lag on the robot and slow down loop cycle times. Be deliberate about what you choose, especially in competition.
{% endhint %}

### Reading module telemetry while bringing up a robot

The most useful pair of values while bringing up a new robot are `modules/<name>/encoder` (the raw absolute encoder, in degrees) and `modules/<name>/azimuth/mechanism/position` (the angle mechanism's position as the motor controller sees it, in rotations):

* If the absolute encoder decreases while the module is rotated counter-clockwise (should be CCW+), set `absoluteEncoderInverted` for that module.
* If the drive or angle motor's telemetry decreases when it should be increasing (or vice versa), invert that motor in `inverted.drive`/`inverted.angle`.

See [Determine Motor/Encoder Inversion](../how-to/determine-inversion.md) for the full procedure.

## Simulation

YAMS simulates the whole swerve drive using the same vendor simulation models as the real hardware, to varying degrees of fidelity per vendor. All you need to do is call `simIterate()` from your subsystem's `simulationPeriodic()` (in Commands v3, from your robot class's `simulationPeriodic()`):

```java
@Override
public void simulationPeriodic()
{
  drive.simIterate();
}
```

{% hint style="info" %}
[Cosine compensation](module-behaviors.md#cosine-compensation) is tuned for real hardware behavior and can look wrong in simulation. This is expected, not a bug in your config.
{% endhint %}

If `modules/pidfproperties_sim.json` exists in your config directory, YAGSL reads it in place of `modules/pidfproperties.json` while running in simulation, so simulation gains can differ from the real robot's. The config generator includes one in every download.

For more on WPILib's simulation framework generally:

{% embed url="https://docs.wpilib.org/en/stable/docs/software/wpilib-tools/robot-simulation/introduction.html" %}

## Vision Odometry

YAMS's `SwerveDrive` maintains a `SwerveDrivePoseEstimator` internally, and extends its vision-fusion API directly onto `SwerveDrive` so you don't need to construct or manage your own estimator:

```java
drive.addVisionMeasurement(visionPose, timestampSeconds);

// Or with per-measurement standard deviations:
drive.addVisionMeasurement(visionPose, timestampSeconds, visionStdDevs);

// Or set a persistent default:
drive.setVisionMeasurementStdDevs(visionStdDevs);
```

Feed this from your vision subsystem (PhotonVision, Limelight, etc.) every time a new pose estimate is available. To draw vision poses or targets on the dashboard, add them to the drive's own field with `drive.getField2d()` instead of creating a second `Field2d`. Standard deviations control how much the pose estimator trusts a given vision measurement relative to odometry: tighter (smaller) values pull the estimate toward vision more aggressively; looser (larger) values let odometry dominate. Tune these empirically; see [Swerve Drive Drift Causes](../reference/swerve-drift-causes.md) if pose estimates drift or jump unexpectedly.
