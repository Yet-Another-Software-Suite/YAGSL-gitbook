---
description: >-
  Pull the .wpilog off your robot and look at what actually happened during a
  match or test run.
---

# View your DataLog in AdvantageScope

{% hint style="danger" %}
**This is not "Log Replay."** [AdvantageKit's Log Replay](https://docs.advantagekit.org/getting-started/what-is-advantagekit) re-runs your robot code against a recorded log to deterministically reproduce a match in simulation, and requires the AdvantageKit IO-interface pattern. What this page covers is simpler and works with **every** YAGSL robot, AdvantageKit or not: opening the `.wpilog` file your robot already wrote and looking at the recorded values in AdvantageScope. Nothing re-executes; you're just viewing a file.
{% endhint %}

YAGSL's swerve telemetry can be written to a WPILib `DataLog` as well as published to NetworkTables. See [Telemetry, Simulation & Vision](../explanation/telemetry-and-vision.md) for the live NetworkTables tree; this page is the practical walkthrough for looking at a _recorded_ run afterward.

## Step 1: Make sure a DataLog is actually being written

`DataLogManager` owns the actual file on disk. Start it once, early, in your `Robot` constructor (WPILib 2027 has no `robotInit()`):

```java title="Robot.java"
import org.wpilib.driverstation.DriverStation;
import org.wpilib.system.DataLogManager;

public Robot() {
  DataLogManager.start();
  DriverStation.startDataLog(DataLogManager.getLog()); // optional: also logs joystick/DS data
}
```

{% hint style="info" %}
If you never call `DataLogManager.start()` yourself, it still starts lazily the first time YAMS creates a DataLog entry. Calling it explicitly, before you build the swerve drive, guarantees the log begins at power-on and lets you fold in DS/joystick data with `DriverStation.startDataLog(...)`.
{% endhint %}

On the real robot, `.wpilog` files are written to a `logs` folder on a USB drive if one is plugged in (`/u/logs`), otherwise to `/home/systemcore/logs` on the SystemCore's internal storage. In simulation, they land in your project's `logs/` folder next to `build.gradle`. WPILib initially names the file `WPILIB_TBD_<random>.wpilog` and renames it to a UTC timestamp (e.g. `WPILIB_20261009_010558.wpilog`) once the Driver Station connects, or to `WPILIB_<timestamp>_<event>_<match>.wpilog` when the FMS provides a match number.

{% hint style="warning" %}
`WPILIB_TBD_*` files left over from a previous boot are deleted on startup, and the oldest logs are deleted when the storage has less than 50 MB free. Pull the logs you care about off the robot.
{% endhint %}

## Step 2: Give the swerve drive a DataLog name

By default `DataLogManager` records every NetworkTables value change, so everything under `Mechanisms/swerve` is already in the file (as `NT:/Mechanisms/swerve/...`) without any setup. To also get YAMS' dedicated DataLog entries, which keep recording when you turn NetworkTables publishing off for competition, name them on the telemetry config you pass to `SwerveDriveConfig`:

```java title="SwerveDriveSubsystem.java"
import yams.core.telemetry.SwerveDriveTelemetryConfig;
import yams.core.telemetry.enums.TelemetryVerbosity;

var cfg = new SwerveDriveConfig()
    .withSubsystem(this) // withMechanism(this) on Commands v3
    .withTelemetry("swerve", new SwerveDriveTelemetryConfig(TelemetryVerbosity.HIGH)
        .withDataLogName("Swerve"));
```

`SwerveParser` then derives a DataLog name for every module and motor from that one name:

* `Swerve`: the drive itself (pose, gyro, chassis speeds, module states).
* `Swerve/modules/<name>`: that module's absolute encoder and state.
* `Swerve/modules/<name>/drive`: that module's drive motor.
* `Swerve/modules/<name>/azimuth`: that module's angle/steering motor.

Numeric entries have their unit inserted after that prefix, for example `Swerve/degrees/gyro`, `Swerve/modules/frontleft/degrees/encoder`, or `Swerve/modules/frontleft/azimuth/rotations/mechanism/position`. Struct entries such as `Swerve/pose` and `Swerve/states/current` do not. Tunable (live tuning) fields are never written to the DataLog.

{% hint style="info" %}
If you use the shorter `withTelemetry("swerve", TelemetryVerbosity.HIGH)` overload instead, `SwerveParser` names the drive's DataLog `Swerve` for you, but only the drive-level entries are created, not the per-module or per-motor ones. Pass a `SwerveDriveTelemetryConfig` with `withDataLogName(...)` as above to get all of them. A `SwerveDriveTelemetryConfig` without a DataLog name creates no dedicated entries at all (the `NT:` mirror is still recorded).
{% endhint %}

{% hint style="warning" %}
Note the DataLog tree uses a top-level `Swerve` (the name you chose, no `Mechanisms` prefix), a _different_ root than the live NetworkTables tree covered in [Telemetry, Simulation & Vision](../explanation/telemetry-and-vision.md) (`Mechanisms/swerve`, from the telemetry name). Both describe the same data; AdvantageScope shows them as two separate branches in the field tree (`Swerve` for the DataLog-only entries, `NT` for the mirrored NetworkTables ones). See the sidebar in the screenshots below.
{% endhint %}

Which fields exist depends on the `TelemetryVerbosity`: `LOW` logs the pose and gyro, `MID` adds the measured chassis speeds and module states, and `HIGH` adds the desired ones.

## Step 3: Run it and record

Deploy or simulate the robot, enable it, and drive around for a bit. Every field above writes to both NetworkTables and the DataLog on each `drive.updateTelemetry()` call.

## Step 4: Pull the file and open it in AdvantageScope

1. Grab the `.wpilog` off the SystemCore (the USB drive, or `scp systemcore@10.TE.AM.2:/home/systemcore/logs/*.wpilog .`), or straight from your sim project's `logs/` folder.
2. Open **AdvantageScope**, then **File → Open File...** (or drag the `.wpilog` onto the window). Do **not** use "Connect to Robot/Simulator". That connects live over NetworkTables, which shows _current_ values, not the recorded run.

## Step 5: Explore the data

Add a tab to visualize the data:

* **2D Field** tab: drag `Swerve/pose` in to see the robot's recorded path.

<figure><img src="../.gitbook/assets/datalog-1-2d-field.gif" alt=""><figcaption><p>Scrubbing a recorded run's path on the 2D Field tab, fed from Swerve/pose.</p></figcaption></figure>

* **Swerve** tab: AdvantageScope's built-in module visualizer. Drag `Swerve/states/current` (and optionally `Swerve/states/desired` and `Swerve/chassis/current`/`desired`) into the **Sources** list to see wheel vectors animate over the timeline.

<figure><img src="../.gitbook/assets/datalog-2-swerve-tab.gif" alt=""><figcaption><p>The Swerve tab replaying recorded module states: translating, then spinning in place.</p></figcaption></figure>

* **Line Graph** tab: drag any numeric field to plot it over time. A useful pairing while debugging: each module's `Swerve/modules/<name>/azimuth/rotations/mechanism/position` (the angle motor's own position, in rotations) against `Swerve/modules/<name>/degrees/encoder` (the absolute encoder, in degrees; also in the file as `NT:/Mechanisms/swerve/modules/<name>/encoder`). If the two drift apart over a run, once you account for the units, the angle motor's encoder was seeded from the wrong absolute offset.

<figure><img src="../.gitbook/assets/datalog-3-line-graph.gif" alt=""><figcaption><p>Dragging each module's azimuth position and absolute encoder fields into the Line Graph tab.</p></figcaption></figure>

Scrub the timeline at the top of the window to step through the run, or hit play to watch it back at normal speed.

{% hint style="success" %}
Because this is just a file, you can open the same `.wpilog` in as many tabs/windows as you want, compare two different matches side by side, or hand the file to a teammate to look at independently. None of that requires the robot, the simulator, or AdvantageKit replay to be running.
{% endhint %}

## Related pages

* [Telemetry, Simulation & Vision](../explanation/telemetry-and-vision.md)
* [How to set up AdvantageScope](set-up-advantagescope.md)
* [Schema Changes](../reference/schema-changes.md)
