---
description: Starting points and a tuning procedure for your drive and angle motor gains.
---

# How to tune PIDF gains

{% hint style="warning" %}
Swerve steering/angle/azimuth motors have PID wrapping enabled at the topmost and bottommost point (e.g. "front" and "back"). When tuning angle PID you may want to test with left or right translation so the module doesn't cross the wrap point.
{% endhint %}

## The basic idea

YAGSL reads PIDF values from [`pidfproperties.json`](../reference/json-schema/pidfproperties-json.md) in your module folder. Generate or edit it with **[config.yagsl.com](https://config.yagsl.com)**.

This has been hashed and boiled down to the simplest form like this before:

* **P**: if you're not where you want to be, get there.
* **I**: if you haven't been where you want to be for a long time, get there faster.
* **D**: if you're getting close to where you want to be, slow down.
  * [Video](https://www.youtube.com/watch?v=qKy98Cbcltw) found by 78, found by [this CD post](https://www.chiefdelphi.com/t/finally-i-understand-pid/450811)

<figure><img src="../.gitbook/assets/pid_explainer.png" alt=""><figcaption></figcaption></figure>

## Tuning procedure

Another good way to describe it, [from CTRE](https://pro.docs.ctr-electronics.com/en/latest/docs/api-reference/device-specific/talonfx/closed-loop-requests.html):

Manual tuning typically follows this process:

1. Set $$P$$, $$I$$, and $$D$$ to zero.
2. Increase $$P$$ until the output **starts** to **oscillate** **around** the setpoint.
3. Increase $$D$$ as much as possible **without** introducing **jittering** to the response.

For the drive motor, also set the feedforward terms (`s`, `v`, `a`; see below) before fine-tuning `P`; a good feedforward means `P` only has to correct small errors.

## WPILib walkthrough

WPILib has lots of great documentation on PID, so it's worth reading directly:

{% embed url="https://docs.wpilib.org/en/stable/docs/software/advanced-controls/introduction/introduction-to-pid.html" %}
Basics of PID
{% endembed %}

{% embed url="https://docs.wpilib.org/en/stable/docs/software/advanced-controls/introduction/tuning-flywheel.html" %}
Tuning velocity, like the drive motors!
{% endembed %}

{% embed url="https://docs.wpilib.org/en/stable/docs/software/advanced-controls/introduction/tuning-turret.html" %}
Tuning position, like the steering/angle/azimuth motors!
{% endembed %}

## The current `pidfproperties.json` shape

{% hint style="info" %}
The schema changed in YAGSL 2026.8.05; `f` and `iz` (integral zone) and the old `output` clamp are gone. Feedforward is now expressed as `s`/`v`/`a` (`kS`/`kV`/`kA`, matching WPILib's `SimpleMotorFeedforward`). See the [schema changes reference](../reference/schema-changes.md) if you're migrating an older config by hand.
{% endhint %}

```json
{
  "drive": {
    "p": 0.02,
    "i": 0,
    "d": 0,
    "s": 0,
    "v": 0,
    "a": 0
  },
  "angle": {
    "p": 0.01,
    "i": 0,
    "d": 0,
    "s": 0,
    "v": 0,
    "a": 0
  }
}
```

If you leave drive `v` (kV) at `0`, YAGSL auto-derives it from the drive motor's free speed and your gearing, so a reasonable feedforward exists even before you've tuned anything by hand.

You can also add a `pidfproperties_sim.json` alongside it. YAGSL will load that one instead when running in simulation (`RobotBase.isSimulation()`), so you can use looser gains in sim without touching your competition robot's config.

## Starting points

The right PIDF values vary per-robot, but these are reasonable starting points to iterate from, organized by motor controller type.

{% tabs %}
{% tab title="SparkMax / SparkFlex" %}
```json
{
  "drive": {
    "p": 0.0020645,
    "i": 0,
    "d": 0,
    "s": 0,
    "v": 0,
    "a": 0
  },
  "angle": {
    "p": 0.01,
    "i": 0,
    "d": 0,
    "s": 0,
    "v": 0,
    "a": 0
  }
}
```
{% endtab %}

{% tab title="TalonFX" %}
When the drive motor `type` for every module is `talonfx_*` (or alike: `krakenx60`, `falcon500`) this is a reasonable starting point:

```json
{
  "drive": {
    "p": 1,
    "i": 0,
    "d": 0,
    "s": 0,
    "v": 0,
    "a": 0
  },
  "angle": {
    "p": 50,
    "i": 0,
    "d": 0.32,
    "s": 0,
    "v": 0,
    "a": 0
  }
}
```
{% endtab %}
{% endtabs %}

Once you have a reasonable starting point, use **[config.yagsl.com](https://config.yagsl.com)** to regenerate `pidfproperties.json` with your updated numbers, or hand-edit the file directly. Both work, since the parser only cares about the final JSON.

## Live tuning

You don't have to redeploy for every gain change. YAMS publishes the drive's gains and setpoints to NetworkTables, so you can change them from a dashboard (Elastic, AdvantageScope) while the robot runs. The **Tuning Guide** at [config.yagsl.com](https://config.yagsl.com) walks through the full process; these are the pieces it uses.

Live tuning needs `TelemetryVerbosity.HIGH` on the drive and both drive-to-pose controllers. YAMS only publishes the tuning command when both controllers are configured, even if you only want to tune the modules:

```java
var cfg = new SwerveDriveConfig()
    .withSubsystem(this) // withMechanism(this) on Commands v3
    .withTranslationController(new PIDController(4, 0, 0))
    .withRotationController(new PIDController(3, 0, 0))
    .withTelemetry("swerve", new SwerveDriveTelemetryConfig(TelemetryVerbosity.HIGH));
```

1. **Start in simulation**, or with the robot on blocks. Tuning moves the robot.
2. **Enable Utility mode** (formerly Test) and make sure nothing else is commanding the drive. The Commands v2 example holds the drive with an idle command so the default drive command can't fight the tuning command:

   ```java
   RobotModeTriggers.utility().whileTrue(Commands.idle(swerve).withName("Live Tuning"));
   ```
3. **Start the tuning command** YAMS publishes under `Mechanisms/swerve/tuning` (click it in your dashboard, or set its `running` entry to `true`). Values under `Tuning/swerve/` only take effect while it runs.
4. **Turn on one tuning mode** below and adjust its values. Only one mode drives at a time; if you enable more than one, auto-align wins over drive tuning, and drive tuning wins over azimuth tuning.
5. **Copy the gains you settle on back** into `pidfproperties.json` (module gains) or your `SwerveDriveConfig` (drive-to-pose controllers). Live values are not saved and are gone on the next code restart.

`swerve` in these paths is the telemetry name from `withTelemetry("swerve", ...)`.

| Key (under `Tuning/swerve/`)                 | What it does                                                                        | Unit you type            |
| -------------------------------------------- | ----------------------------------------------------------------------------------- | ------------------------ |
| `modules/drive/enabled`                      | Drive tuning: drive every module at `modules/drive/velocity`                        | boolean                  |
| `modules/drive/velocity`                     | Drive velocity setpoint                                                             | meters per second        |
| `modules/drive/inplace`                      | Point the modules tangent to the robot, so a positive velocity spins it in place (counter-clockwise) instead of driving straight | boolean |
| `modules/drive/feedback/p`, `/i`, `/d`       | Drive motor PID (`drive.p`/`i`/`d` in `pidfproperties.json`)                        | same as the JSON         |
| `modules/drive/feedforward/s`, `/v`, `/a`    | Drive motor feedforward (`drive.s`/`v`/`a`)                                         | same as the JSON         |
| `modules/azimuth/enabled`                    | Azimuth tuning: point every module at `modules/azimuth/angle`                       | boolean                  |
| `modules/azimuth/angle`                      | Azimuth angle setpoint                                                              | degrees                  |
| `modules/azimuth/feedback/p`, `/i`, `/d`     | Azimuth motor PID (`angle.p`/`i`/`d`)                                               | same as the JSON         |
| `modules/azimuth/feedforward/s`, `/v`, `/a`  | Azimuth motor feedforward (`angle.s`/`v`/`a`)                                       | same as the JSON         |
| `autoalign/enabled`                          | Auto-align tuning: drive to `autoalign/setpoint/*` with the drive-to-pose controllers | boolean                |
| `autoalign/setpoint/x`, `/y`, `/rot`         | Auto-align target pose (field relative, blue origin)                                | meters, meters, degrees  |
| `autoalign/translation/p`, `/i`, `/d`        | Translation controller (see [drive to a pose](drive-to-a-pose.md))                  | per meter of error       |
| `autoalign/rotation/p`, `/i`, `/d`           | Rotation controller                                                                 | per radian of error      |

{% hint style="warning" %}
**The units you type are not the units you read back.** YAMS' tunable fields use the units people think in: **degrees** and **RPM** for rotating mechanisms, **meters** and **meters per second** for linear ones (a swerve drive wheel is linear, so its velocity setpoint is in m/s). The read-only telemetry it publishes next to them, under `Mechanisms/swerve/...`, is in **rotations** and **rotations per second** (meters and m/s for linear measurements). So an azimuth setpoint of `90` (degrees) shows up as a `mechanism/position` of `0.25` (rotations). Check which table you're looking at before deciding a loop is "off by 360."
{% endhint %}

| Quantity                           | Tunable (under `Tuning/`) | Read-only telemetry (under `Mechanisms/`) |
| ---------------------------------- | ------------------------- | ----------------------------------------- |
| Angular position                   | degrees                   | rotations                                 |
| Angular velocity                   | RPM                       | rotations per second                      |
| Linear position                    | meters                    | meters                                    |
| Linear velocity                    | meters per second         | meters per second                         |
| Gyro heading, absolute encoder     | n/a                       | degrees                                   |

{% hint style="info" %}
The example README lists one known issue for this release: dashboard auto-align only drives correctly while the robot faces 0° (away from the blue driver station). Zero the gyro facing 0° before enabling `autoalign/enabled`. The `driveToPose` commands themselves are not affected.
{% endhint %}
