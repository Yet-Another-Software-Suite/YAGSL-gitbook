# Verify and calibrate your hardware

Before your first drive, confirm every sensor and motor reports direction consistently, and capture each module's absolute encoder offset. Skipping this step is the single most common cause of a swerve drive that "spins out" or drives in the wrong direction.

{% hint style="danger" %}
Make sure every CAN ID is unique across your entire robot, and that each device's CAN bus in your config matches the SystemCore bus it is actually wired to (an empty `canbus` means `can_s0`). A known failure mode: if a SparkMAX shares a CAN ID with your REV Power Distribution Hub, the SparkMAX simply won't move, with no obvious error.
{% endhint %}

## Physically label everything

Label each motor controller, encoder, and gyroscope with its CAN ID/channel and connection type before you start. This is the easiest step to get wrong and the hardest mistake to spot afterward.

## Check your gyroscope

* **Front is whatever the gyroscope reports as `0`.** Pick a front, and expect that you may need to change it later if it turns out to be inconvenient.
* The gyroscope must report **increasing yaw while the robot rotates counterclockwise**. If it doesn't, invert it.
* If you're using a NavX, recalibrate it once it arrives at your shop because NavX units ship calibrated to the humidity of their factory, not your build space. Recalibrate again periodically, especially at competition.

## Check your motors

Deploy your code with the config from step 3, then, **with the robot disabled**, spin each motor by hand and watch AdvantageScope's `Mechanisms/swerve` NetworkTables tree. These values are published by the swerve subsystem from [step 5](05-deploy-and-drive.md), so if your project doesn't have one yet, add it first and come back. The `swerve` in the path is the name that code passes to `SwerveDriveConfig.withTelemetry("swerve", ...)`.

<figure><img src="../.gitbook/assets/yagsl-telemetry.png" alt=""><figcaption><p>The Mechanisms/swerve tree in AdvantageScope. Each module's drive/azimuth telemetry and raw absolute encoder are visible per-module.</p></figcaption></figure>

* Rotate the **drive** wheel forward (CCW as viewed from above). That module's `modules/<name>/drive/measurement/position` (the distance the wheel has rolled, in meters) should **increase**. If it doesn't, invert the drive motor.
* Rotate the **angle** mechanism CCW (viewed from above). Both `modules/<name>/azimuth/mechanism/position` (relative, in rotations) and `modules/<name>/encoder` (absolute, in degrees) should **increase**. If either doesn't, invert that motor or encoder respectively.
* Rotate the entire **robot** CCW. `Mechanisms/swerve/gyro` should **increase**. If not, invert the gyroscope.

See [Determine Inversion](../how-to/determine-inversion.md) for the full decision procedure if any of these don't behave as expected, or work through [config.yagsl.com/guide](https://config.yagsl.com/guide), which walks through this same alignment process interactively.

{% hint style="warning" %}
If you're using a hardware vendor client to poke at motors/encoders directly, your robot program must not be commanding those devices at the same time. Keep the robot disabled while you use the client, and power-cycle the robot when you're done so the SystemCore and your robot program start from a clean state.
{% endhint %}

## Capture absolute encoder offsets

The absolute encoder offset is what lets your swerve module remember wheel orientation across power cycles. It's essential to a functioning swerve drive.

1. Manually rotate all four wheels so they point in the same direction (bevel gears facing left, as in the diagram below) and forward rotation increases the drive encoder.

<figure><img src="../.gitbook/assets/devilbots_cropped_swerve_orientation.png" alt=""><figcaption><p>The left and right are physical left and right.</p></figcaption></figure>

2. With the robot still disabled, open AdvantageScope and read each module's `Mechanisms/swerve/modules/<name>/encoder` value while it's held in that aligned position.
3. Enter each value as that module's absolute encoder offset, either back in the [config generator](https://config.yagsl.com) (recommended: upload your existing config, update the four offset fields, and re-download) or directly in `absoluteEncoderOffset` in the module's JSON file.

{% hint style="warning" %}
**Special note for MAXSwerve teams:** after aligning the wheels, you may need to add or subtract `90` from the measured offset to get the module pointing truly forward, and set `absoluteEncoderInverted` to `true`. This is a quirk of how MAXSwerve mounts its absolute encoder, not a bug in your measurement.
{% endhint %}

Once every module reports correctly and has a captured offset, move on to [deploying and driving](05-deploy-and-drive.md).
