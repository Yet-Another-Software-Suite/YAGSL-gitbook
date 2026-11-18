# Troubleshooting flowchart

Pick the chart that matches what your robot is doing, and follow it from the top. Each chart checks the quickest
causes first. The links under each chart go to the pages its boxes mention.

* [Set up a robot with the config guide](#set-up-a-robot-with-the-config-guide)
* [Robot drives slower than it should](#robot-drives-slower-than-it-should)
* [Autonomous is consistently off](#autonomous-is-consistently-off)
* [Robot is drifting](#robot-is-drifting)
* [Wheels not pointing straight](#wheels-not-pointing-straight)
* [One module doesn't start in the right position](#one-module-doesnt-start-in-the-right-position)
* [Swerve modules spinning "out of control"](#swerve-modules-spinning-out-of-control)
* [Swerve module does not spin correctly](#swerve-module-does-not-spin-correctly)
* [Swerve drive "spins out of control"](#swerve-drive-spins-out-of-control)
* [Forward is not forward: the 8 steps](#forward-is-not-forward-the-8-steps)

Not sure which one? [Find your problem](fix-a-problem.md) matches symptoms to pages.

## Swerve drive diagram

<figure><img src="../.gitbook/assets/devilbots_cropped_swerve_orientation.png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
Older gyroscopes like the Pigeon 1 and NavX 1 drift the longer they're powered on. Consider rebooting or zeroing your gyroscope at the start of your program.
{% endhint %}

## Set up a robot with the config guide

The config generator's [Quick Start Guide](https://config.yagsl.com/guide) takes a robot from a downloaded config to
driving, in nine steps. This is the same path, with the checks that send you to another chart.

<figure><img src="../.gitbook/assets/yagsl-flowchart-guide.png" alt="A flowchart of the config generator's Quick Start Guide. Start: fill in config.yagsl.com and press Download Config. 1, set up the project: the example project, your team number and your config in src/main/deploy/swerve. 2, connect to the simulator: start the simulator, open AdvantageScope and add a Swerve tab. 3, tune in simulation. Does current follow desired in simulation? If not, keep tuning the sim gains and check again. 4, connect to the robot. 5, enter each module's location in its JSON file. 6, align the modules: every wheel forward with its bevel gear on the left, and record each absolute encoder offset. Does every absolute encoder read counterclockwise positive? If not, invert that module's absolute encoder. 7, tune the real robot: check the gyro, tune the gains and spin each module 5 turns. Hold the right stick left: does the robot spin counterclockwise? If not, see the spins out of control flowchart. 8, verify the field: drive each direction at 0, 45 and 125 degree headings. Does it go where you push the stick at every heading? If not, work through the 8 steps. 9, maintenance: grease the modules, re-measure the wheels and watch for drift. Then drive."><figcaption><p>The Quick Start Guide as a flowchart.</p></figcaption></figure>

* Tuning in simulation: [Practice in simulation](../tutorial/practice-in-simulation.md)
* Inverting an absolute encoder: [Determine inversion](determine-inversion.md)
* Spinning the wrong way: [Swerve drive "spins out of control"](#swerve-drive-spins-out-of-control)
* Wrong at some headings: [Forward is not forward: the 8 steps](#forward-is-not-forward-the-8-steps)

## Robot drives slower than it should

<figure><img src="../.gitbook/assets/yagsl-flowchart-slow.png" alt="A flowchart for a robot that drives slower than it should. Is the SystemCore nearly out of storage space? If yes, free up space, such as old logs, then reboot the SystemCore. If no: in the spin test, does any module push against the others? If yes, that module runs backwards: its drive inversion is wrong, or its absolute encoder offset is 180 degrees off. If no: does your code scale the joystick down? If yes, remove the scaling, such as withScaleTranslation or a multiplier on the axes, or make it the slow mode it was meant to be. If no: at full stick, is the commanded module speed your robot's top speed? If no, check the maximum speed: the drive motor type, gear ratio and wheel diameter it comes from, and any withMaximumChassisSpeed. If yes: does each module's measured speed reach its commanded speed? If no, tune the drive PID and feedforward. If yes, the robot thinks it's fast but isn't: check the gear ratio and measure the wheel diameter."><figcaption><p>Robot drives slower than it should.</p></figcaption></figure>

* The spin test, drive inversion and offsets: [Determine inversion](determine-inversion.md)
* Scaling the joystick and the maximum speed: [Chassis Control](../explanation/chassis-control.md#max-speed)
* Tuning the drive: [Practice in simulation](../tutorial/practice-in-simulation.md) and
  [Tune PIDF gains](tune-pidf-gains.md)
* Gear ratios and wheel diameters: [Standard Conversion Factors](../reference/standard-conversion-factors.md)

## Autonomous is consistently off

<figure><img src="../.gitbook/assets/yagsl-flowchart-auto.png" alt="A flowchart for autonomous that ends short or long by about the same amount every time. Push the robot a measured distance, such as 3 meters. Does odometry agree? If yes, odometry is right, so the path following is off: see Fix swerve drive drift. If no: is the drive gear ratio right for your module and its configuration? If no, fix the gear ratio, such as with the config generator's module type and configuration. If yes, measure the wheel diameter as the tread is now, since worn tread is smaller than new: update it, or replace the tread."><figcaption><p>Autonomous ends short or long by about the same amount every time.</p></figcaption></figure>

* Gear ratios and wheel diameters: [Standard Conversion Factors](../reference/standard-conversion-factors.md)
* Path following: [Fix swerve drive drift](../reference/swerve-drift-causes.md)

## Robot is drifting

<figure><img src="../.gitbook/assets/yagsl-flowchart-drift.png" alt="A flowchart for a robot that drifts sideways while driving or rotating. Are there errors in the Driver Station console, or loop overruns? If yes, fix them first: check the wiring of the encoder, motor controller and gyro, and speed up slow code. If no: driving straight, do all the wheels point straight? If no, see the Wheels not pointing straight flowchart. If yes: does the drive direction change as the robot turns? If yes, work through the 8 steps. If no: do the absolute encoder offsets still hold after driving and a reboot? If no, secure the magnet and encoder, loctiting the magnet, then record the offsets again. If yes: does each module track its speed and angle setpoints on the graph? If no, tune the drive and azimuth PID and feedforward, and if one module lags the others, grease its gears. If yes: do all modules share the right gear ratio and wheel diameter, with no per-module gearing? If no, fix them in physicalproperties.json and remove per-module gearing overrides. If yes: does it drift only when driving and rotating at once? If yes, tune withDiscretizationTime, then the gyro angular velocity scale factor. If no, check the rest of Fix swerve drive drift: vision standard deviations, current limits, the center of gravity and PathPlanner."><figcaption><p>Robot drifts sideways while driving or rotating.</p></figcaption></figure>

* Wiring and loop overruns: [Fix swerve drive drift](../reference/swerve-drift-causes.md#hardware-causes)
* Wheels not straight: [Wheels not pointing straight](#wheels-not-pointing-straight)
* Direction changes as the robot turns: [Forward is not forward: the 8 steps](#forward-is-not-forward-the-8-steps)
* Recording offsets: [Check and calibrate your hardware](../tutorial/04-verify-and-calibrate-hardware.md)
* Tuning the modules: [Practice in simulation](../tutorial/practice-in-simulation.md) and
  [Tune PIDF gains](tune-pidf-gains.md)
* Gear ratios and wheel diameters: [Standard Conversion Factors](../reference/standard-conversion-factors.md)
* Discretization, the gyro scale factor and everything else:
  [Fix swerve drive drift](../reference/swerve-drift-causes.md#tuning-order)

## Wheels not pointing straight

<figure><img src="../.gitbook/assets/yagsl-flowchart-align.png" alt="A flowchart for wheels that don't all point straight when you drive straight. Are the CAN IDs and device type strings right for every module? If no, fix them in each module's JSON file. If yes: are the module files in swervedrive.json's modules list in the right order? If no, put them in order: front left, front right, back left, back right. If yes: are the module locations and absolute encoder offsets right? If no, verify the module locations, and record each offset with every wheel forward and its bevel gear on the left. If yes: does each module reach its angle setpoint on the graph? If no, tune the azimuth PID and feedforward. If yes, check the module for a loose encoder magnet, slipping gears or wiring."><figcaption><p>Wheels don't all point straight when you drive straight.</p></figcaption></figure>

* Device type strings: [Device Type Strings](../reference/json-schema/device-types.md)
* Module locations: [Verify your module locations](verify-module-locations.md)
* Recording offsets: [Check and calibrate your hardware](../tutorial/04-verify-and-calibrate-hardware.md)
* Tuning the azimuth: [Practice in simulation](../tutorial/practice-in-simulation.md)
* Only one module acts up: [One module doesn't start in the right position](#one-module-doesnt-start-in-the-right-position)

## One module doesn't start in the right position

When one module's azimuth acts up, or points somewhere different each time the robot starts, the problem is almost
always in that module's hardware or its gearing.

<figure><img src="../.gitbook/assets/yagsl-flowchart-azimuth.png" alt="A flowchart for one module whose azimuth acts up, and doesn't start in the right position every time. Is its absolute encoder connected, with no errors, and its reading changing when you turn the wheel by hand? If no, the absolute encoder is disconnected: reseat or replace its cable, and check its CAN ID, power and status light. If yes: is the magnet glued in, centered and tight, so it can't slip on its shaft? If no, glue or loctite the magnet in, then record the absolute encoder offset again. If yes: turn the wheel exactly one turn by hand. Does the absolute encoder read one turn too? If no, the module is built incorrectly: the encoder or magnet is on the wrong shaft or gear, so rebuild it by the manufacturer's guide. If yes: graph the motor position with the absolute encoder. Do the lines stay on top of each other? If no, the gearing is wrong: set the angle gear ratio for your module and its configuration. If yes: does the azimuth turn freely by hand, and draw about the same current as the other modules with the same gains? If no, the motor is dying or the module binds: clean and grease the module, then replace the motor if it still struggles. If yes, swap this module's motor and encoder with another module's: if the problem moves, replace that part."><figcaption><p>One module's azimuth acts up, or doesn't start in the right position every time.</p></figcaption></figure>

* Reading the absolute encoder and recording offsets: [Check and calibrate your hardware](../tutorial/04-verify-and-calibrate-hardware.md)
* Graphing the motor position with the absolute encoder: [Practice in simulation](../tutorial/practice-in-simulation.md)
* Angle gear ratios: [Standard Conversion Factors](../reference/standard-conversion-factors.md)
* A dying motor or a loose magnet: [Fix swerve drive drift](../reference/swerve-drift-causes.md#physical-components)

## Swerve modules spinning "out of control"

<figure><img src="../.gitbook/assets/flowchart2.png" alt=""><figcaption></figcaption></figure>

## Swerve module does not spin correctly

1. Are the CAN IDs, motor types, and absolute encoder types correct? See the [device reference](../reference/hardware/) for supported type strings.
2. Are the module `location`s correct?
3. Do you use an absolute magnetic encoder?
   1. Did you loctite the magnet in?
   2. Do you have a good (usually green LED) reading on the magnet?
4. Did you set `absoluteEncoderOffset` while the wheels had the bevel facing left and the wheel pointed straight front-to-back?
5. Did you check [how to determine inversion](determine-inversion.md) for inversion states?
6. Did you [tune your PID](tune-pidf-gains.md)?

If none of the above resolves it, move on to [the 8 steps](#forward-is-not-forward-the-8-steps).

## Swerve drive "spins out of control"

<figure><img src="../.gitbook/assets/flowchart4.png" alt=""><figcaption></figcaption></figure>

See [how to determine inversion](determine-inversion.md) and [the 8 steps](#forward-is-not-forward-the-8-steps).

## Forward is not forward: the 8 steps

When forward in real life isn't forward in odometry, or field-relative driving changes as the robot turns, the 8 steps
try every combination of gyro inversion, drive inversion and module order that can cause it. Somewhere along the way it
gets fixed. [Fix steering that changes as the robot turns](the-8-steps.md) has the drive code to test with, and how to
flip the modules.

{% hint style="danger" %}
Most of the steps make the robot spin out of control. Keep it somewhere safe and be ready to disable it.
{% endhint %}

<figure><img src="../.gitbook/assets/yagsl-flowchart-eight.png" alt="A flowchart of the 8 steps. Forward in real life isn't forward in odometry, or field-relative driving changes as the robot turns. After every step, test: drive forward, turn 90 degrees, then drive forward again, and check it goes where you push the stick at every heading. If it does, it's fixed. If not, go to the next step. Step 1: set gyroInvert to false and every inverted.drive to false. Step 2: set gyroInvert to true. Step 3: set every inverted.drive to true. Step 4: set gyroInvert to false. Step 5: flip the modules by swapping the module files diagonally. Step 6: set every inverted.drive to false. Step 7: set gyroInvert to true. Step 8: set every inverted.drive to true. If step 8 fails too, something is wired or set up wrong: check the wiring, CAN IDs and absolute encoders."><figcaption><p>The 8 steps as a flowchart.</p></figcaption></figure>
