---
description: Match what your robot is doing to the page that fixes it.
---

# Find your problem

Find the row that matches what your robot is doing, and start with the page it points to. Several
problems can be present at once, so if the first fix doesn't settle it, work down the table.

{% hint style="info" %}
Prefer flowcharts? The [troubleshooting flowcharts](troubleshooting-flowchart.md) cover the same problems.
Most problems come from a step skipped during setup. If you haven't been through
[Check and calibrate your hardware](../tutorial/04-verify-and-calibrate-hardware.md), do that first:
it catches inversions, bad offsets and CAN ID mistakes in a few minutes.
{% endhint %}

## The robot program won't start, or a device does nothing

| What you see | Start here |
| --- | --- |
| The robot program crashes at startup with a missing class error | A vendor library is missing. See [Vendordeps and offline install](../reference/vendordep-installation.md#optional-vendor-libraries). |
| A SPARK isn't found, or `canbus` is rejected with an `IllegalArgumentException` | [Fix common SparkMAX/SparkFlex problems](fix-sparkmax-common-problems.md) |
| A SparkMAX doesn't move, with no error | Its CAN ID is shared with another device, such as the REV Power Distribution Hub. Every CAN ID must be unique on its bus. See [Put devices on a different CAN bus](choose-a-can-bus.md). |
| Status frame errors from a SparkMAX | A loose or broken sensor cable. See [Fix common SparkMAX/SparkFlex problems](fix-sparkmax-common-problems.md). |

## On blocks: the modules don't point or spin correctly

| What you see | Start here |
| --- | --- |
| One or more modules spin "out of control" | [Troubleshooting flowchart](troubleshooting-flowchart.md) |
| One module acts up, or doesn't start in the right position every time | A disconnected absolute encoder, an encoder too far from its magnet, a loose magnet, wrong gearing, a dying motor, or a module built incorrectly. See the [One module doesn't start in the right position](troubleshooting-flowchart.md#one-module-doesnt-start-in-the-right-position) flowchart. |
| A module points the wrong way, or its readings go the wrong way when turned by hand | [Determine inversion](determine-inversion.md), steps 1 and 2 |
| In the spin test, one wheel pushes against the others | That module's drive motor is inverted. See [Determine inversion](determine-inversion.md), step 3. |
| In the spin test, the robot spins clockwise instead of counterclockwise | A diagonal module swap, inverted drive motors, or offsets captured with the bevel gears facing right. See [Determine inversion](determine-inversion.md), step 3. |
| Driving straight, the modules don't all point forward | A wrong CAN ID, device type, module order, location or offset, or untuned azimuth gains. See the [Wheels not pointing straight](troubleshooting-flowchart.md#wheels-not-pointing-straight) flowchart. |
| Gears grind on the ground but not on blocks, with every wheel pointing and spinning the right way | The gains need tuning, not inverting. See [Tune PIDF gains](tune-pidf-gains.md). |

## On the ground: field-relative driving is wrong

| What you see | Start here |
| --- | --- |
| Driving is wrong at every heading, including the starting one | Check `gyroInvert`. See [Determine inversion](determine-inversion.md), step 4. |
| In field-relative driving, driving is right at the starting heading, but the directions change as the robot turns, or forward isn't forward in odometry | [Fix steering that changes as the robot turns](the-8-steps.md) (the 8 steps), also [as a flowchart](troubleshooting-flowchart.md#forward-is-not-forward-the-8-steps) |
| The robot drives forward, but odometry shows it driving backward | Add `180` to every `absoluteEncoderOffset`. See the "Odometry mismatch" note in [Determine inversion](determine-inversion.md), step 4. |
| "Forward" is the wrong side of the robot | Front is whatever the gyro reports as `0`. See [Verify your module locations](verify-module-locations.md). |
| The field-relative heading is off after a while, or the robot powered on facing the wrong way | Zero the gyro with the button combo from [Write the swerve code](../tutorial/write-the-swerve-code.md). An older gyro such as a Pigeon 1 or NavX 1 drifts the longer it's powered on. |

## It drives, but not well

| What you see | Start here |
| --- | --- |
| The robot slides off its line while it drives | The [Robot is drifting](troubleshooting-flowchart.md#robot-is-drifting) flowchart, left branch, then [Fix swerve drive drift](../reference/swerve-drift-causes.md) |
| The robot's heading turns on its own while it drives | The [Robot is drifting](troubleshooting-flowchart.md#robot-is-drifting) flowchart, right branch, then [Fix swerve drive drift](../reference/swerve-drift-causes.md) |
| The modules are slow to reach their angle, overshoot, or shake | [Tune PIDF gains](tune-pidf-gains.md) |
| Driving hard, such as in a match or practice, the modules fall behind their angles, but they're fine on blocks | The angle motor's current limit is too low. Raise `statorCurrentLimit.angle` in [physicalproperties.json](../reference/json-schema/physicalproperties-json.md). See the [Wheels not pointing straight](troubleshooting-flowchart.md#wheels-not-pointing-straight) flowchart. |
| The robot drives slower than it should | A module running backwards, scaling in the drive code, the maximum speed, the gains, the gear ratio or wheel diameter, or a full SystemCore. See the [Robot drives slower than it should](troubleshooting-flowchart.md#robot-drives-slower-than-it-should) flowchart. |
| Autonomous ends short or long by about the same amount every time | The gear ratio or wheel diameter is off, or the wheel tread has worn down. See the [Autonomous is consistently off](troubleshooting-flowchart.md#autonomous-is-consistently-off) flowchart. |
| PathPlanner paths overshoot or end in the wrong place | [Fix swerve drive drift](../reference/swerve-drift-causes.md), then [Set up PathPlanner](setup-pathplanner.md) |

## Still stuck?

Record a DataLog of the problem and open it with
[View your DataLog in AdvantageScope](view-your-datalog-in-advantagescope.md). Then ask on the
[YAGSL Discord](https://discord.gg/yass) with your `swerve/` config directory and the log attached.
