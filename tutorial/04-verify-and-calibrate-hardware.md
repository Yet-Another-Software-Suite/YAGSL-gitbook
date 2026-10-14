# Verify and calibrate your hardware

Before your first drive, confirm every sensor and motor reports direction consistently, capture each module's absolute encoder offset, and tune the modules. Skipping this step is the single most common cause of a swerve drive that "spins out" or drives in the wrong direction.

Work through the steps in order. Steps 2 and 10 are the same live tuning, first in simulation to learn it and then on the real robot, so the animations in step 2 apply to step 10 too.

{% hint style="info" %}
The [config generator's Quick Start Guide](https://config.yagsl.com/guide) walks through this same process as interactive, printable checklists.
{% endhint %}

{% stepper %}
{% step %}
### Label every device and check the CAN IDs

Label each motor controller, encoder and the gyroscope with its type, CAN ID (or channel) and bus before you start. This is the easiest step to get wrong and the hardest mistake to spot afterward.

<figure><img src="../.gitbook/assets/yagsl-label-devices.png" alt="A robot from above with four modules, each tagged with its drive, angle and encoder IDs, and the gyro in the middle. Beside it, the front left module's three labels: FL drive, sparkflex_vortex, ID 2, canbus 1; FL angle, sparkmax_neo550, ID 1, canbus 1; FL absolute encoder, cancoder_can, ID 10, empty canbus. Every CAN ID must be unique on its bus and not shared with other devices such as the power hub."><figcaption><p>Label every device with its type, ID and bus.</p></figcaption></figure>

{% hint style="danger" %}
Make sure every CAN ID is unique across your entire robot, and that each device's CAN bus in your config matches the SystemCore bus it is actually wired to (an empty `canbus` means `can_s0`). A known failure mode: if a SparkMAX shares a CAN ID with your REV Power Distribution Hub, the SparkMAX simply won't move, with no obvious error.
{% endhint %}
{% endstep %}

{% step %}
### Practice live tuning in simulation

Run the robot program in simulation and go through live tuning once before you touch the real robot. Simulation is practice, not tuning: the gains it finds can't be trusted on the real modules, but it teaches you the dashboard and catches config mistakes while nothing can break. Live tuning needs the swerve subsystem from [step 5](05-deploy-and-drive.md) with `TelemetryVerbosity.HIGH` and both drive-to-pose controllers; [Tune PIDF gains](../how-to/tune-pidf-gains.md#live-tuning) shows the setup.

**Start live tuning.** Enable Utility mode, start the tuning command under `Mechanisms/swerve/tuning`, and turn on one tuning mode. `Tuning/swerve/` values only take effect while the command runs.

<figure><img src="../.gitbook/assets/yagsl-live-tuning-start.gif" alt="An animation of a dashboard. First the robot mode is set to Utility. Then Mechanisms/swerve/tuning is set to running: true. Then Tuning/swerve/modules/azimuth/enabled is set to true. Notes beside it explain each step: Utility mode with nothing else driving, starting the tuning command, and picking one tuning mode at a time."><figcaption><p>Utility mode, the tuning command, then one tuning mode.</p></figcaption></figure>

**Tune the azimuth first**, so the wheels point where they are told while you tune the drive. Set `Tuning/swerve/modules/azimuth/enabled` to `true` and switch `modules/azimuth/angle` between `0` and `90`. Raise `feedback/p` until the modules snap to the angle; if they overshoot and shake, back it off and add a little `feedback/d`.

<figure><img src="../.gitbook/assets/yagsl-tune-azimuth.gif" alt="An animation of every module turning from 0 to 90 degrees beside a graph of the desired and current angle. With p at 0.002 the modules turn slowly and are still short of 90 degrees. With p at 0.05 they overshoot and ring. With p at 0.03 and d at 0.0005 they turn quickly and settle. The values are only an example."><figcaption><p>Too slow, ringing, then quick and settled. The values are only an example.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-azimuth-swerve.png" alt="AdvantageScope connected to the YAGSL example in simulation. The sidebar shows Mechanisms/swerve/tuning with running on, and Tuning/swerve/modules/azimuth with angle 90, enabled on and the feedback gains. The Swerve tab shows every module turned to 90 degrees, desired in red and current in blue."><figcaption><p>The real thing: azimuth tuning in AdvantageScope, every module at 90°.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-azimuth-graph.png" alt="An AdvantageScope line graph of Tuning/swerve/modules/azimuth/angle stepping between 0 and 90 degrees and Mechanisms/swerve/modules/frontleft/encoder following it, settling a little short of each step."><figcaption><p>Graph Tuning/swerve/modules/azimuth/angle against a module's encoder to judge the gains.</p></figcaption></figure>

**Then tune the drive.** Turn azimuth tuning off and set `modules/drive/enabled` to `true` with `modules/drive/velocity` at `2`. Zero `feedback/p`, raise `feedforward/v` until the velocity settles on 2 m/s, then add `feedback/p` until it gets there quickly and holds.

<figure><img src="../.gitbook/assets/yagsl-tune-drive.gif" alt="An animation of the drive velocity against a 2 meters per second setpoint. With feedforward v at 2 and p at 0 it settles at 1.6 meters per second, below the setpoint. With v at 2.5 and p at 0 it settles on 2 meters per second. With p at 4 added it reaches 2 meters per second quickly and holds. The values are only an example."><figcaption><p>Feedforward first, then feedback. The values are only an example.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-drive-graph.png" alt="An AdvantageScope line graph of Tuning/swerve/modules/drive/velocity stepping between 0 and 1.5 meters per second and Mechanisms/swerve/modules/frontleft/drive/measurement/velocity following it, overshooting to about 1.58 meters per second."><figcaption><p>Graph Tuning/swerve/modules/drive/velocity against a module's drive/measurement/velocity.</p></figcaption></figure>

**Save what you find.** Values typed on the dashboard are gone the next time the robot program starts. In simulation, copy them into `pidfproperties_sim.json`, which YAGSL reads only in simulation.

<figure><img src="../.gitbook/assets/yagsl-live-tuning-save.png" alt="The tuning values under Tuning/swerve/modules map to the JSON: azimuth/feedback/p to angle.p, azimuth/feedback/d to angle.d, drive/feedforward/v to drive.v and drive/feedback/p to drive.p. In simulation they go into pidfproperties_sim.json, which is read only in simulation. On the real robot they go into pidfproperties.json."><figcaption><p>Where the live values belong.</p></figcaption></figure>

[YAMS' live tuning page](https://yams.yassrobotics.com/documentation/understanding/live-tuning#swerve-drive) covers every swerve tuning key, `feedforward/s`, and auto-align tuning.
{% endstep %}

{% step %}
### Deploy, stay disabled, and open AdvantageScope

Deploy your code with the config from step 3 and **keep the robot disabled**. Open AdvantageScope's `Mechanisms/swerve` NetworkTables tree. The swerve subsystem from [step 5](05-deploy-and-drive.md) publishes these values, so if your project doesn't have one yet, add it first and come back. The `swerve` in the path is the name that code passes to `SwerveDriveConfig.withTelemetry("swerve", ...)`.

<figure><img src="../.gitbook/assets/yagsl-telemetry.png" alt="The Mechanisms/swerve tree in AdvantageScope, with each module's drive and azimuth telemetry and its raw absolute encoder."><figcaption><p>The Mechanisms/swerve tree in AdvantageScope. Each module's drive/azimuth telemetry and raw absolute encoder are visible per-module.</p></figcaption></figure>

{% hint style="warning" %}
If you're using a hardware vendor client to poke at motors/encoders directly, your robot program must not be commanding those devices at the same time. Keep the robot disabled while you use the client, and power-cycle the robot when you're done so the SystemCore and your robot program start from a clean state.
{% endhint %}
{% endstep %}

{% step %}
### Pick a front and check the gyroscope

**Front is whatever the gyroscope reports as `0`.** Pick a front, and expect that you may need to change it later if it turns out to be inconvenient. Then lift the robot and turn it counterclockwise (seen from above): `Mechanisms/swerve/gyro` must go **up**. If it goes down, set `gyroInvert` to `true` in `swervedrive.json`.

<figure><img src="../.gitbook/assets/yagsl-gyro-ccw.gif" alt="An animation of a robot seen from above turning 90 degrees counterclockwise, twice. On the left the gyro reading goes up to 90 degrees, which is correct, so gyroInvert stays false. On the right it goes down to minus 90 degrees, so gyroInvert must be set to true."><figcaption><p>Turn the robot counterclockwise: the gyro should go up.</p></figcaption></figure>

If you're using a NavX, recalibrate it once it arrives at your shop because NavX units ship calibrated to the humidity of their factory, not your build space. Recalibrate again periodically, especially at competition.
{% endstep %}

{% step %}
### Check each drive motor

Point the module forward, then roll its wheel forward by hand. That module's `modules/<name>/drive/measurement/position` (the distance the wheel has rolled, in meters) must go **up**. If it goes down, set `"inverted": { "drive": true }` in that module's JSON.

<figure><img src="../.gitbook/assets/yagsl-check-drive.gif" alt="An animation of a wheel seen from the side being rolled forward by hand. On the left drive/measurement/position goes up to 0.244 meters, which is correct. On the right it goes down to minus 0.244 meters, so inverted.drive must be set to true."><figcaption><p>Roll the wheel forward: the drive position should go up.</p></figcaption></figure>
{% endstep %}

{% step %}
### Check each angle motor and absolute encoder

Turn the module counterclockwise (seen from above) by hand. Both `modules/<name>/azimuth/mechanism/position` (relative, in rotations) and `modules/<name>/encoder` (absolute, in degrees) must go **up**.

* If the **encoder** goes down, set `"absoluteEncoderInverted": true`.
* If the **azimuth** position goes down, set `"inverted": { "angle": true }`.

<figure><img src="../.gitbook/assets/yagsl-check-angle.gif" alt="An animation of three modules seen from above, each turned 90 degrees counterclockwise by hand. In the first both readings go up: azimuth position to 0.25 rotations and encoder to 90 degrees, which is correct. In the second the encoder goes down to minus 90 degrees, so absoluteEncoderInverted must be set to true. In the third the azimuth position goes down to minus 0.25 rotations, so inverted.angle must be set to true."><figcaption><p>Turn the module counterclockwise: both readings should go up.</p></figcaption></figure>

See [Determine Inversion](../how-to/determine-inversion.md) for the full decision procedure if any of these don't behave as expected.
{% endstep %}

{% step %}
### Align every wheel

Turn all four wheels by hand so they point forward, with their bevel gears facing the robot's left, and so rolling them forward makes the drive position go up.

<figure><img src="../.gitbook/assets/yagsl-align-wheels.gif" alt="An animation of a robot seen from above. The four modules start at different angles and turn until every wheel points forward with its bevel gear on the robot's left side."><figcaption><p>Every wheel forward, bevel gears on the left.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/devilbots_cropped_swerve_orientation.png" alt="Swerve modules with their bevel gears facing left."><figcaption><p>The left and right are physical left and right.</p></figcaption></figure>
{% endstep %}

{% step %}
### Record each module's offset

With the wheels still aligned and the robot still disabled, read each module's `Mechanisms/swerve/modules/<name>/encoder` and enter it as that module's absolute encoder offset. Use the [config generator](https://config.yagsl.com) (recommended: upload your existing config, update the four offset fields, and re-download) or `absoluteEncoderOffset` in the module's JSON file.

<figure><img src="../.gitbook/assets/yagsl-record-offsets.gif" alt="An animation of each module's encoder reading in AdvantageScope being copied into its JSON, one module at a time: frontleft 132.363 degrees, frontright minus 50.977, backleft 271.143 and backright 12.656, each into that module's absoluteEncoderOffset."><figcaption><p>Each encoder reading becomes that module's offset.</p></figcaption></figure>

The absolute encoder offset is what lets your swerve module remember wheel orientation across power cycles. Redeploy once all four are in.

{% hint style="warning" %}
**Special note for MAXSwerve teams:** after aligning the wheels, you may need to add or subtract `90` from the measured offset to get the module pointing truly forward, and set `absoluteEncoderInverted` to `true`. This is a quirk of how MAXSwerve mounts its absolute encoder, not a bug in your measurement.
{% endhint %}
{% endstep %}

{% step %}
### Run the spin test

With the robot lifted, command pure counterclockwise rotation and watch the modules in AdvantageScope's Swerve view. It should spin cleanly counterclockwise.

<figure><img src="../.gitbook/assets/yagsl-spin-test.gif" alt="An animation of three robots seen from above, commanded to spin counterclockwise. The first spins cleanly counterclockwise: correct. The second has one wheel pushing against the others: invert that module's drive motor. The third spins clockwise: check for a diagonal module swap first."><figcaption><p>What the spin test can look like.</p></figcaption></figure>

You can also run it from the dashboard: with drive tuning on, set `Tuning/swerve/modules/drive/inplace` to `true` and give `modules/drive/velocity` a small positive speed. Every module turns tangent to the robot and it should spin counterclockwise.

<figure><img src="../.gitbook/assets/yagsl-ascope-spin-swerve.png" alt="The AdvantageScope Swerve tab during drive tuning with modules/drive/inplace on: every module points tangent to the robot at plus or minus 45 degrees and drives at 1 meter per second, desired in red and current in blue, so the robot spins counterclockwise in place."><figcaption><p>The spin test from the dashboard: modules/drive/inplace on, every module tangent to the robot.</p></figcaption></figure>

[Determine Inversion](../how-to/determine-inversion.md) lists every symptom and its cause.
{% endstep %}

{% step %}
### Live tune on the real robot

Now do step 2 again for real, with the same dashboard and the same order: start live tuning, tune the azimuth, then the drive. The animations in step 2 show what to look for.

* **Lift the robot on blocks**, with every wheel clear of the blocks and of the ground. Tuning moves the modules.
* **Start from the gains in `pidfproperties.json`**, not the ones you found in simulation.
* **Copy what you settle on into `pidfproperties.json`**, the file the real robot reads.

<figure><img src="../.gitbook/assets/yagsl-live-tuning-save.png" alt="The tuning values under Tuning/swerve/modules map to the JSON: azimuth/feedback/p to angle.p, azimuth/feedback/d to angle.d, drive/feedforward/v to drive.v and drive/feedback/p to drive.p. In simulation they go into pidfproperties_sim.json, which is read only in simulation. On the real robot they go into pidfproperties.json."><figcaption><p>On the real robot, the gains go into pidfproperties.json.</p></figcaption></figure>
{% endstep %}
{% endstepper %}

Once every module reports correctly, has a captured offset and is tuned, move on to [deploying and driving](05-deploy-and-drive.md).
