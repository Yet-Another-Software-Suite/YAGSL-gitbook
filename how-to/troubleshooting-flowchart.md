# Troubleshooting flowchart

Most swerve problems come from something basic, so check the basics first. This chart checks a swerve drive in
stages, from the robot itself up to autonomous. Start at stage 1, and only move on when a stage passes. If a stage finds
a problem, fix it, then start that stage again.

Within each stage, the fast checks come first, such as reading a graph, and the slow ones last, such as measuring the
robot. The yellow boxes tell you how to check, and the blue question after each one asks what you saw.

* Know your symptom? [By symptom](#by-symptom) lists the checks most likely to catch it, but still work through the
  stages in order.
* Setting up a new robot? The config generator's [Quick Start Guide](https://config.yagsl.com/guide) walks the same
  checks as you go.
* Not sure what's wrong? [Find your problem](fix-a-problem.md) matches symptoms to pages.

## Swerve drive diagram

<figure><img src="../.gitbook/assets/devilbots_cropped_swerve_orientation.png" alt=""><figcaption></figcaption></figure>

{% hint style="danger" %}
Gyroscopes drift. They're sensitive to pressure, and sometimes humidity, and older ones like the Pigeon 1 and NavX 1 drift the longer they're powered on. If yours drifts, recalibrate it, and zero it at the start of your program.
{% endhint %}

## The stages

<figure><img src="../.gitbook/assets/yagsl-flowchart-stages.png" alt="A flowchart that checks a swerve drive in nine stages, from the basics up. Start at stage 1. If a check finds a problem, fix it, then start that stage again. Stage 1: Is it built right?: Compare each module with the manufacturer's guide, and with the other modules, part by part. Does every module match? If no, rebuild it by the manufacturer's guide. If yes, continue. Check each absolute encoder's magnet, by gently trying to pull it out. Does it stay put, glued or loctited in and centered? If no, glue or loctite it in. You'll record the offset again in stage 5. If yes, continue. Tug every wire and connector: encoders, motor controllers, gyro and CAN. Are they all tight? If no, fix the wiring. Loose sensor cables cause errors and motors that spin out of control. If yes, continue. Push on the gyro. Is it mounted firmly? If no, mount it so it can't shift or shake. If yes, continue. Turn each wheel, and steer each module, by hand. Do they turn freely? If no, the module binds: clean and grease it. If yes, continue. Measure every wheel's diameter with calipers, with its tread on. Are they the same size, and the tread not worn down? If no, replace worn tread. Worn tread is smaller than new. If yes, continue. Test the battery with a Battery Beak or another load tester. Does it pass? If no, charge or replace it. If yes, continue. Check how much free storage the SystemCore has left. Is it nearly full? If yes, delete old logs, then reboot the SystemCore. If no, continue. Stage 2: Do the sensors and motors work?: Start the code and watch the Driver Station console. Loop overruns just after the code starts are normal. Any errors? If yes, fix them first. See the program won't start table in Find your problem: a missing vendor library, a SPARK not found, a loose cable. If no, continue. Turn each wheel by hand while you graph its absolute encoder, Mechanisms/swerve/modules/<name>/encoder. Does the reading change smoothly? If no, the absolute encoder is disconnected: reseat or replace its cable, and check its power and status light. If yes, continue. Check each encoder's magnet reading: its status light, or the magnet health in the vendor's tool. The vendor's documentation says what each means. Does it report a good magnet? If no, the encoder is too far from the magnet, or off center: close the gap to the vendor's spec. If yes, continue. Turn each wheel exactly one turn by hand. Does its absolute encoder read one turn too? If no, the encoder or magnet is on the wrong shaft or gear: rebuild it by the manufacturer's guide. If yes, continue. Rotate the robot by hand while you graph the gyro, Mechanisms/swerve/gyro. Does the heading change? If no, the gyro isn't working: check its wiring, CAN ID and type. If yes, continue. On blocks, enable and steer and drive the modules. Graph every module's azimuth and drive stator current, ../<name>/azimuth/current/stator and ../<name>/drive/current/stator. Does one motor need much more current than the same motor on the others? If yes, it's dying, or its module binds. Swap it with another module's motor: if the problem follows, replace the motor. If no, continue. Leave the robot still for a minute, and graph the gyro. Does the heading change while the robot sits still? If yes, the gyro drifts: recalibrate it, and zero it at the start of your program. Gyros are sensitive to pressure, and sometimes humidity. Older gyros like the Pigeon 1 and NavX 1 drift the longer they're powered on. If no, continue. With a wheel pointing straight, draw a straight line across its magnet and the module frame. Drive, point it straight again, and look at the line. Does the line still line up? If no, the magnet slipped: glue or loctite it in. If yes, continue. Stage 3: Is the configuration right?: Check the project: your team number, and your config files in src/main/deploy/swerve. Are they there? If no, set the team number, and put your config from the config generator in deploy/swerve. If yes, continue. List every device in Phoenix Tuner X or the REV Hardware Client. Do the CAN IDs and device types match your JSON files? If no, fix them in the module JSON files and swervedrive.json. If yes, continue. Look for devices that share a CAN ID, such as a SparkMAX and the REV Power Distribution Hub. Is every CAN ID unique on its bus? If no, change one of the IDs. A SparkMAX with a shared ID doesn't move. If yes, continue. Check which CAN bus each device is wired to. Does each device's canbus in the JSON match? If no, fix the canbus. See Put devices on a different CAN bus. If yes, continue. On blocks, turn one wheel by hand at a time and watch which module moves in AdvantageScope's Swerve tab. Does the matching module move? If no, put the module files in swervedrive.json's modules list in order: front left, front right, back left, back right. If yes, continue. Stage 4: Are the module locations right?: Look at each module's location in its JSON file. Is it in inches, from the robot's center? If no, enter it in inches, from the robot's center to the module's center. If yes, continue. Measure from the robot's center to each module's center in inches, and compare. Do the locations match? If no, fix the locations. See Verify your module locations. If yes, continue. Zero the gyro with the robot's front away from you, and drive forward. Does it drive toward its front? If no, front is whatever the gyro reports as 0. See Verify your module locations. If yes, continue. Stage 5: Are the inversions and offsets right?: Turn each module counterclockwise, seen from above, while you graph its azimuth motor position, Mechanisms/swerve/modules/<name>/azimuth/mechanism/position, and its absolute encoder, ../<name>/encoder. Do both increase? If no, invert the one that doesn't: absoluteEncoderInverted or inverted.angle. See Determine inversion, step 1. If yes, continue. Rotate the robot counterclockwise by hand while you graph the gyro. Does the heading increase? If no, set gyroInvert. See Determine inversion, step 2. If yes, continue. Point every wheel forward with its bevel gear on the left, and graph each module's azimuth motor position with its absolute encoder. Does every motor position read 0, with its encoder on top? If no, set the offsets to 0, then record each one again with the wheel forward and its bevel gear on the left. If yes, continue. On blocks, run the spin test from Determine inversion, step 3, and watch which way each wheel drives. Does any module push against the others? If yes, that module runs backwards: flip its drive inversion, or record its offset again if it's 180° off. If no, continue. In the spin test, hold the right stick left. Does the robot spin counterclockwise? If no, a diagonal module swap, inverted drive motors, or offsets recorded with the bevel gears facing right. See Determine inversion, step 3. If yes, continue. On the ground, drive forward and watch odometry, Mechanisms/swerve/pose. Does odometry move forward too? If no, add 180 to every absoluteEncoderOffset. If yes, continue. Stage 6: Are the gear ratios and wheel size right?: Look up your module's angle and drive gear ratios for its configuration in Standard Conversion Factors or the config generator. Does your config match? If no, fix them, such as with the config generator's Module Type and Module Configuration. If yes, continue. Look for a gearing override in each module's JSON. Does any module have its own gearing? If yes, remove it, unless that module really is geared differently. If no, continue. Spin each module five turns by hand while you graph its azimuth motor position with its absolute encoder, ../<name>/azimuth/mechanism/position and ../<name>/encoder. Do the lines stay on top of each other? If no, the angle gear ratio doesn't match the absolute encoder: fix it. If yes, continue. Measure a wheel's diameter with calipers, with its tread on as it is now. Does the diameter match your config? If no, update the wheel diameter, or replace the tread. If yes, continue. Mark the floor, push the robot a measured distance, such as 3 m, and compare odometry, Mechanisms/swerve/pose. Does odometry agree? If no, check the drive gear ratio and wheel diameter again. If yes, continue. Stage 7: Does the code drive it right?: Read your drive code for scaling, such as withScaleTranslation or multiplying the controller axis. Is the input scaled down? If yes, remove the scaling, or make it a slow mode on purpose. If no, continue. Read your code for a way to zero the gyro. Can the driver zero it? If no, add the button combo from Write the swerve code. If yes, continue. Move each stick while you watch the desired module states in AdvantageScope's Swerve tab. Do they follow the sticks? If no, the controller input is wrong: check the port, and negate the axes. If yes, continue. At full stick, graph the desired module states, Mechanisms/swerve/states/desired. Do they reach your robot's top speed? If no, check the maximum speed: the drive motor type, gear ratio and wheel diameter it comes from, and any withMaximumChassisSpeed. If yes, continue. In field-relative driving, drive each direction at 0°, 45° and 125° headings. In robot-relative driving the direction changes as the robot turns, which is normal. Does it go where you push the stick at every heading? If no, work through the 8 steps. If yes, continue. Stage 8: Are the PID, feedforward and current limits right?: Compare pidfproperties.json with the starting points in Tune PIDF gains. Are your gains at least the starting points? If no, start from them. Old SparkMAX values like 0.0020645 are far too small. If yes, continue. In simulation, graph the desired and current module states, Mechanisms/swerve/states/desired and Mechanisms/swerve/states/current. Does current follow desired? If no, tune the sim gains in pidfproperties_sim.json. See Practice in simulation. If yes, continue. On the robot, graph the same states while you steer and drive. Does each module reach its angle? If no, tune the azimuth PID and feedforward. If yes, continue. Keep graphing while you drive. Does each module reach its speed? If no, tune the drive PID and feedforward. If yes, continue. Drive on the ground. Do gears grind on the ground but not on blocks? If yes, the gains need tuning, not inverting. If no, continue. Open the DataLog from a match or a practice (YAGSL records one by default), and graph the states while the robot is pushed or turning hard. Do the modules fall behind their angle, or feel weak, but not on blocks? If yes, raise statorCurrentLimit in physicalproperties.json: angle (default 20 A) or drive (default 40 A). If no, continue. Stage 9: Is the code right for odometry and autonomous?: Drive straight while rotating, then drive straight without rotating. Does it drift only when driving and rotating at once? If yes, discretization, which YAGSL normally handles: if you set withDiscretizationTime, check it, then tune the gyro angular velocity scale factor. If no, continue. If you use vision, graph the pose with and without it. Does the pose jump when vision is added? If yes, tune the vision standard deviations, and filter out far-away poses. If no, continue. Look at your autos in PathPlanner. Does each auto set a starting pose and reset odometry? If no, set a starting pose, and reset odometry at the start. If yes, continue. Run a straight path, and graph the pose against the path. Does it follow the path? If no, tune the AutoBuilder PIDs, and lower the maximum acceleration if the wheels slip. Check the maximum module velocity. If yes, continue. Then it drives right. Still stuck? Check the rest of Fix swerve drive drift, such as the center of gravity. Then ask on Discord with your swerve config folder and a DataLog."><figcaption><p>Every stage, from the basics up. Open the image to zoom in.</p></figcaption></figure>

Each stage has a section below, with links to the pages its boxes mention.

1. [Is it built right?](#stage-1-is-it-built-right)
2. [Do the sensors and motors work?](#stage-2-do-the-sensors-and-motors-work)
3. [Is the configuration right?](#stage-3-is-the-configuration-right)
4. [Are the module locations right?](#stage-4-are-the-module-locations-right)
5. [Are the inversions and offsets right?](#stage-5-are-the-inversions-and-offsets-right)
6. [Are the gear ratios and wheel size right?](#stage-6-are-the-gear-ratios-and-wheel-size-right)
7. [Does the code drive it right?](#stage-7-does-the-code-drive-it-right)
8. [Are the PID, feedforward and current limits right?](#stage-8-are-the-pid-feedforward-and-current-limits-right)
9. [Is the code right for odometry and autonomous?](#stage-9-is-the-code-right-for-odometry-and-autonomous)

## Stage 1: Is it built right?

Check the robot itself before anything electrical or in code. Most of these need nothing but your hands and a tape measure.

* Wiring, magnets, the gyro and worn parts: [Fix swerve drive drift](../reference/swerve-drift-causes.md#hardware-causes)
* Wheel diameters: [Standard Conversion Factors](../reference/standard-conversion-factors.md)

## Stage 2: Do the sensors and motors work?

With the code running, check every sensor reads, and every motor moves without straining.

* Errors at startup: [Find your problem](fix-a-problem.md#the-robot-program-wont-start-or-a-device-does-nothing)
* What the magnet status light or reading means: your encoder's page under [Absolute Encoders](../reference/hardware/absolute-encoders.md), and the vendor's documentation
* Gyros: [Gyroscopes](../reference/hardware/gyroscopes.md)
* Graphing telemetry: [Set up AdvantageScope](set-up-advantagescope.md)

## Stage 3: Is the configuration right?

Check the config files describe the robot you built: CAN IDs, buses, device types and module order.

* Generating the config: [Generate your configuration](../tutorial/03-generate-your-configuration.md)
* Device type strings: [Device Type Strings](../reference/json-schema/device-types.md)
* CAN buses: [Put devices on a different CAN bus](choose-a-can-bus.md)
* SPARK problems: [Fix common SparkMAX/SparkFlex problems](fix-sparkmax-common-problems.md)

## Stage 4: Are the module locations right?

Check where the code thinks each module is.

* Locations and which side is front: [Verify your module locations](verify-module-locations.md)

## Stage 5: Are the inversions and offsets right?

Check every sensor and motor counts the right way, and the absolute encoder offsets.

* Every inversion and the spin test: [Determine inversion](determine-inversion.md)
* Recording offsets: [Check and calibrate your hardware](../tutorial/04-verify-and-calibrate-hardware.md)

## Stage 6: Are the gear ratios and wheel size right?

Check the numbers that turn motor rotations into angles and meters.

* Gear ratios, wheel diameters and inversions per module: [Standard Conversion Factors](../reference/standard-conversion-factors.md)
* Gearing overrides: [Module JSON](../reference/json-schema/module-json.md)

## Stage 7: Does the code drive it right?

Check the code passes the driver's sticks to the drive the way you mean it to.

* The drive code and gyro zeroing: [Write the swerve code](../tutorial/write-the-swerve-code.md)
* Scaling and the maximum speed: [Chassis Control](../explanation/chassis-control.md#max-speed)
* Wrong at some headings: [Forward is not forward: the 8 steps](#forward-is-not-forward-the-8-steps)

## Stage 8: Are the PID, feedforward and current limits right?

Check each module reaches what it's told, in simulation and on the robot.

* Starting gains: [Tune PIDF gains](tune-pidf-gains.md#starting-points)
* Tuning step by step: [Practice in simulation](../tutorial/practice-in-simulation.md)
* Reading a DataLog: [View your DataLog in AdvantageScope](view-your-datalog-in-advantagescope.md)
* Current limits: [physicalproperties.json](../reference/json-schema/physicalproperties-json.md)

## Stage 9: Is the code right for odometry and autonomous?

Check the code that tracks where the robot is and follows paths.

* Discretization, vision and everything else: [Fix swerve drive drift](../reference/swerve-drift-causes.md#tuning-order)
* Autos: [Set up PathPlanner](setup-pathplanner.md)

## Forward is not forward: the 8 steps

Stage 7 sends you here when field-relative driving goes the wrong way at some headings.

When forward in real life isn't forward in odometry, or field-relative driving changes as the robot turns, the 8 steps
try every combination of gyro inversion, drive inversion and module order that can cause it. Somewhere along the way it
gets fixed. [Fix steering that changes as the robot turns](the-8-steps.md) has the drive code to test with, and how to
flip the modules.

Test in field-relative driving. In robot-relative driving the direction is supposed to change as the robot turns, so
it can't show this problem.

{% hint style="danger" %}
Most of the steps make the robot spin out of control. Keep it somewhere safe and be ready to disable it.
{% endhint %}

<figure><img src="../.gitbook/assets/yagsl-flowchart-eight.png" alt="A flowchart of the 8 steps. Forward in real life isn't forward in odometry, or field-relative driving changes as the robot turns. After every step, test in field-relative driving: drive forward, turn 90 degrees, then drive forward again, and check it goes where you push the stick at every heading. If it does, it's fixed. If not, go to the next step. Step 1: set gyroInvert to false and every inverted.drive to false. Step 2: set gyroInvert to true. Step 3: set every inverted.drive to true. Step 4: set gyroInvert to false. Step 5: flip the modules by swapping the module files diagonally. Step 6: set every inverted.drive to false. Step 7: set gyroInvert to true. Step 8: set every inverted.drive to true. If step 8 fails too, something is wired or set up wrong: check the wiring, CAN IDs and absolute encoders."><figcaption><p>The 8 steps as a flowchart.</p></figcaption></figure>


## By symptom

Work through the stages in order. These are the checks most likely to catch each symptom, so you know what to watch
for.

### Set up a robot with the config guide

The [Quick Start Guide](https://config.yagsl.com/guide) takes a robot from a downloaded config to driving. Its steps
line up with the stages:

* Set up the project, with your team number and config in `src/main/deploy/swerve`: [stage 3](#stage-3-is-the-configuration-right).
* Connect to the simulator and tune in simulation, until current follows desired: [stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right).
* Enter each module's location: [stage 4](#stage-4-are-the-module-locations-right).
* Align the modules and record each offset, and check every absolute encoder reads counterclockwise positive: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* Tune the real robot, check the gyro and spin each module 5 turns: [stage 5](#stage-5-are-the-inversions-and-offsets-right), [stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right) and [stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right).
* Hold the right stick left and check the robot spins counterclockwise: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* Drive each direction at 0°, 45° and 125° headings: [stage 7](#stage-7-does-the-code-drive-it-right).
* Maintenance, such as greasing the modules and re-measuring the wheels: [stage 1](#stage-1-is-it-built-right).

### Robot drives slower than it should

* A module running backwards in the spin test: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* A gear ratio or wheel diameter that's off, so the robot thinks it's faster than it is: [stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right).
* Scaling in the drive code, or a maximum speed set too low: [stage 7](#stage-7-does-the-code-drive-it-right).
* Untuned drive gains, or a drive current limit set too low: [stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right).
* A weak battery, or a SystemCore that's nearly out of storage: [stage 1](#stage-1-is-it-built-right).

### Autonomous is consistently off

It ends short or long by about the same amount every time.

* Worn tread: [stage 1](#stage-1-is-it-built-right).
* The drive gear ratio, wheel diameter or a per-module gearing override, and whether odometry matches a measured
  distance: [stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right).
* The path following, the starting pose and wheel slip: [stage 9](#stage-9-is-the-code-right-for-odometry-and-autonomous).

### Robot is drifting

* **Slides off its line** (translational drift): the absolute encoder offsets ([stage 5](#stage-5-are-the-inversions-and-offsets-right)), the angle gear ratio ([stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right)),
  field-relative driving at every heading ([stage 7](#stage-7-does-the-code-drive-it-right)), and whether each module reaches its angle ([stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right)).
* **Heading turns on its own** (rotational drift): a dying drive motor or a drifting gyro ([stage 2](#stage-2-do-the-sensors-and-motors-work)), the offsets ([stage 5](#stage-5-are-the-inversions-and-offsets-right)),
  wheel sizes and gearing overrides ([stage 1](#stage-1-is-it-built-right) and [stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right)), and whether each module reaches its speed ([stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right)).
* **Only while driving and rotating at once**: discretization, which YAGSL normally handles ([stage 9](#stage-9-is-the-code-right-for-odometry-and-autonomous)).

### Wheels not pointing straight

The wheels don't all point straight when you drive straight.

* CAN IDs, device types and module order: [stage 3](#stage-3-is-the-configuration-right).
* The absolute encoder offsets: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* Module locations, in inches: [stage 4](#stage-4-are-the-module-locations-right).
* The azimuth gains, and an angle current limit too low to hold the modules while driving hard: [stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right).

### One module doesn't start in the right position

One module's azimuth acts up, or points somewhere different each time the robot starts.

* The module built incorrectly, or binding: [stage 1](#stage-1-is-it-built-right).
* A disconnected encoder, an encoder too far from its magnet, a slipping magnet, or a dying motor: [stage 2](#stage-2-do-the-sensors-and-motors-work).
* The angle gear ratio: [stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right).

### Swerve modules spinning "out of control"

* Loose sensor cables: [stage 1](#stage-1-is-it-built-right).
* The absolute encoder and angle motor readings increasing when you turn the module counterclockwise: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* The conversion factors, checked against Standard Conversion Factors: [stage 6](#stage-6-are-the-gear-ratios-and-wheel-size-right).
* The PID gains: [stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right).

### Swerve module does not spin correctly

* The magnet glued in: [stage 1](#stage-1-is-it-built-right).
* A good reading on the magnet, usually a green status light: [stage 2](#stage-2-do-the-sensors-and-motors-work).
* CAN IDs, motor types and absolute encoder types: [stage 3](#stage-3-is-the-configuration-right).
* The offsets, recorded with the bevel gear facing left and the wheel pointing straight, and the inversions: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* Module locations: [stage 4](#stage-4-are-the-module-locations-right).
* The PID gains: [stage 8](#stage-8-are-the-pid-feedforward-and-current-limits-right).

### Swerve drive "spins out of control"

* The gyro reading increasing when the robot turns counterclockwise, the offsets and the inversions: [stage 5](#stage-5-are-the-inversions-and-offsets-right).
* Module locations, in inches from the robot's center: [stage 4](#stage-4-are-the-module-locations-right).
* The controller input, checked against the desired states in the Swerve tab: [stage 7](#stage-7-does-the-code-drive-it-right).
