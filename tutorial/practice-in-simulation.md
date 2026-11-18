# 5. Practice in simulation

Run the robot program in simulation and go through live tuning once before you touch the real
robot. Simulation is practice, not tuning: the gains it finds can't be trusted on the real modules,
but it teaches you the dashboard and catches config mistakes while nothing can break. You'll do the
same live tuning for real in [step 7](05-deploy-and-drive.md).

Live tuning needs `TelemetryVerbosity.HIGH` and both drive-to-pose controllers, which the code from
[step 4](write-the-swerve-code.md) already sets. [Tune PIDF gains](../how-to/tune-pidf-gains.md#live-tuning)
explains each piece.

**Start live tuning.** Enable Utility mode, start the `Tuning/swerve/Live Tuning` command, and turn on one tuning mode. `Tuning/swerve/` values only take effect while the command runs.

<figure><img src="../.gitbook/assets/yagsl-sim-utility-mode.png" alt="The Robot State panel of the WPILib simulation GUI with two numbered markers: 1 on Utility and 2 on Enable. The OpMode dropdown is left at NONE."><figcaption><p>In simulation: in the sim GUI's Robot State panel, pick Utility (1), then Enable (2). Leave the OpMode at NONE, because a teleop op mode would drive the modules and fight the tuning.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-live-tuning.gif" alt="A recording of AdvantageScope connected to the YAGSL example in simulation, in Utility mode. 1: the tuning mode button beside the sidebar search is clicked, and the values under Tuning become editable. 2: running under Tuning/swerve/Live Tuning is clicked and turns green. 3: enabled under Tuning/swerve/modules/azimuth is clicked and turns green. 4: 15 is typed into angle, then an empty part of the sidebar is clicked to apply it. 5: in the Swerve tab every module turns to 15 degrees."><figcaption><p>Starting live tuning in AdvantageScope, recorded in simulation.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-enable-tuning.png" alt="AdvantageScope connected to the simulation with four numbered markers. 1 is the tuning mode button beside the sidebar search, which makes the values editable. 2 is the running switch under Tuning/swerve/Live Tuning, which starts the tuning command. 3 is the enabled switch under Tuning/swerve/modules/azimuth, which turns azimuth tuning on. 4 is the angle field under Tuning/swerve/modules/azimuth, where you type the setpoint."><figcaption><p>In AdvantageScope: turn on tuning mode (1), click <code>running</code> to start the tuning command (2), turn one tuning mode on (3), then type the setpoint (4).</p></figcaption></figure>

**Tune the azimuth first**, so the wheels point where they are told while you tune the drive. Set `Tuning/swerve/modules/azimuth/enabled` to `true` and switch `modules/azimuth/angle` between `0` and `15`. Raise `feedback/p` until the modules snap to the angle; if they overshoot and shake, back it off and add a little `feedback/d`.

<figure><img src="../.gitbook/assets/yagsl-tune-azimuth.gif" alt="An animation of every module turning from 0 to 15 degrees beside a graph of the desired and current angle. With p at 0.002 the modules turn slowly and are still short of 15 degrees. With p at 0.05 they overshoot and ring. With p at 0.03 and d at 0.0005 they turn quickly and settle. The values are only an example."><figcaption><p>Too slow, ringing, then quick and settled. The values are only an example.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-azimuth-swerve.png" alt="AdvantageScope connected to the YAGSL example in simulation. The sidebar shows Tuning/swerve/Live Tuning with running on, and Tuning/swerve/modules/azimuth with angle 15, enabled on and the feedback gains. The Swerve tab shows every module turned to 15 degrees, desired in red and current in blue."><figcaption><p>The real thing: azimuth tuning in AdvantageScope, every module at 15°.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-azimuth-graph.png" alt="An AdvantageScope line graph of Tuning/swerve/modules/azimuth/angle stepping between 0 and 15 degrees and Mechanisms/swerve/modules/frontleft/encoder following it, settling a little short of each step."><figcaption><p>Graph Tuning/swerve/modules/azimuth/angle against a module's encoder to judge the gains.</p></figcaption></figure>

**Then tune the drive.** Turn azimuth tuning off and set `modules/drive/enabled` to `true` with `modules/drive/velocity` at `2`. Zero `feedback/p`, raise `feedforward/v` until the velocity settles on 2 m/s, then add `feedback/p` until it gets there quickly and holds.

<figure><img src="../.gitbook/assets/yagsl-tune-drive.gif" alt="An animation of the drive velocity against a 2 meters per second setpoint. With feedforward v at 2 and p at 0 it settles at 1.6 meters per second, below the setpoint. With v at 2.5 and p at 0 it settles on 2 meters per second. With p at 4 added it reaches 2 meters per second quickly and holds. The values are only an example."><figcaption><p>Feedforward first, then feedback. The values are only an example.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/yagsl-ascope-drive-graph.png" alt="An AdvantageScope line graph of Tuning/swerve/modules/drive/velocity stepping between 0 and 1.5 meters per second and Mechanisms/swerve/modules/frontleft/drive/measurement/velocity following it, overshooting to about 1.58 meters per second."><figcaption><p>Graph Tuning/swerve/modules/drive/velocity against a module's drive/measurement/velocity.</p></figcaption></figure>

**Save what you find.** Values typed on the dashboard are gone the next time the robot program starts. In simulation, copy them into `pidfproperties_sim.json`, which YAGSL reads only in simulation.

<figure><img src="../.gitbook/assets/yagsl-live-tuning-save.png" alt="The tuning values under Tuning/swerve/modules map to the JSON: azimuth/feedback/p to angle.p, azimuth/feedback/d to angle.d, drive/feedforward/v to drive.v and drive/feedback/p to drive.p. In simulation they go into pidfproperties_sim.json, which is read only in simulation. On the real robot they go into pidfproperties.json."><figcaption><p>Where the live values belong.</p></figcaption></figure>

[YAMS' live tuning page](https://yams.yassrobotics.com/documentation/understanding/live-tuning#swerve-drive) covers every swerve tuning key, `feedforward/s`, and auto-align tuning.

Next: [check and calibrate your hardware](04-verify-and-calibrate-hardware.md).
