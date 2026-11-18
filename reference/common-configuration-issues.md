---
description: One wrong number in a JSON file can make a swerve drive do almost anything...
---

# Common configuration issues

A swerve drive's configuration has a lot of numbers in it, and a wrong one usually doesn't cause an error; the robot just drives wrong. This is a lookup of common configuration issues, grouped by the file they live in. For what each field means, see the [JSON Configuration Schema](json-schema/README.md).

It's important to keep in mind that SEVERAL of these issues could be present on your robot at once.

Prefer to work through it step by step? See [Stage 3: Is the configuration right?](../how-to/troubleshooting-flowchart.md#stage-3-is-the-configuration-right) and the stages after it in the troubleshooting flowcharts.

## Project setup

* [ ] The team number in the project doesn't match the robot's, so the code never deploys to it.
* [ ] The `swerve/` config directory isn't in `src/main/deploy/`, or the code points to a different directory than the one you edited.
* [ ] A vendor library is missing; the robot program crashes at startup with a missing class error. See [Vendordeps and offline install](vendordep-installation.md#optional-vendor-libraries).

## Devices

### CAN IDs and buses

* [ ] A device's `id` doesn't match the CAN ID set on the device itself.
* [ ] Two devices share a CAN ID on the same bus (e.g. a SparkMAX and the REV Power Distribution Hub); one of them won't respond.
* [ ] A device's `canbus` doesn't match the bus it's wired to. `""` is the SystemCore bus `can_s0`, and `"1"` is `can_s1`. See [Put devices on a different CAN bus](../how-to/choose-a-can-bus.md).
* [ ] The drive and angle IDs are swapped within a module, so the drive motor steers and the angle motor drives.

### Device types

* [ ] A motor controller's `type` names the wrong motor (e.g. a NEO 550 configured as a NEO).
* [ ] The absolute encoder's `type` doesn't match how it's wired (e.g. a `_can` type for an encoder plugged into SmartIO, or the reverse).
* [ ] An absolute encoder on SmartIO has the wrong `channel`.
* [ ] The gyro's `type` is wrong, or `gyroAxis` doesn't match how the gyro is mounted.

### Modules

* [ ] Module files are listed in the wrong order in `swervedrive.json`'s `modules`. They're listed clockwise from front-left by convention.
* [ ] Two module files have the same contents, such as a copied file that was never edited.

## Module locations

* [ ] Module locations aren't defined correctly. Remember these are measured from the center of the robot to the center of the module, in inches. See [how to verify module locations](../how-to/verify-module-locations.md).
* [ ] A location has the wrong sign; `front` is positive toward the front of the robot and `left` is positive toward the left.
* [ ] The front of the robot in the configuration isn't the side the gyro reports as `0`.

## Inversions and offsets

* [ ] `inverted.angle` is wrong; the module spins "out of control" because the motor drives away from its setpoint.
* [ ] `absoluteEncoderInverted` is wrong; the encoder counts the opposite way from the angle motor.
* [ ] `inverted.drive` is wrong on one module; that wheel pushes against the others.
* [ ] `gyroInvert` is wrong; field-relative driving is wrong at every heading.
* [ ] `absoluteEncoderOffset` was recorded with the bevel gears facing right instead of left; the robot drives backward in odometry. See [Determine inversion](../how-to/determine-inversion.md).
* [ ] `absoluteEncoderOffset` is stale, from before a magnet was reglued or an encoder was replaced.
* [ ] [Translational axis changes with robot orientation](../how-to/the-8-steps.md).

## Gearing

* [ ] The gear ratio/wheel diameter is not correct. See [Standard Conversion Factors](standard-conversion-factors.md) for common modules.
  * If your swerve drive consistently travels further than expected with PathPlanner, this is a likely cause.
  * If your angle motors never align the module correctly, this can also cause it.
* [ ] The gear ratio is for a different pinion than the one installed (e.g. 14T configured with a 12T pinion).
* [ ] The gear ratio is entered upside down; it's the `X` in `X:1`, so it's greater than `1` for a reduction.
* [ ] The wheel diameter is in the wrong units; it's in inches.
* [ ] A module's `gearing` override in its module JSON disagrees with the default in `physicalproperties.json`.
  * A per-module `gearing` override is completely **OPTIONAL** and shouldn't be defined unless you have a specific reason (e.g. one swapped module with a different ratio); otherwise rely on `physicalproperties.json` to avoid drift between modules.
* [ ] `absoluteEncoderGearRatio` is set when the encoder turns 1:1 with the module, or left out when it doesn't.

## PID, feedforward and current limits

* [ ] Drive and steering/azimuth/angle motor PIDs aren't tuned well enough. See [how to tune PIDF gains](../how-to/tune-pidf-gains.md).
* [ ] Gains were copied from a different motor controller or motor; gains don't carry over between them.
* [ ] The current limit (`statorCurrentLimit`) is too low; the modules fall behind their angles when driving hard.
* [ ] The current limit is too high for the motor; it browns out the robot or overheats the motor.

## Code configuration

* [ ] Maximum physical velocity isn't set correctly. See `SwerveDriveConfig.withMaximumChassisSpeed()`/`withMaximumModuleSpeed()`.
* [ ] The controller input is scaled down, such as with `withScaleTranslation()` or by multiplying the controller axis; the robot never reaches full speed.
* [ ] The controller axes are mapped to the wrong drive directions, or have the wrong sign.
* [ ] The gyro is never zeroed, so field-relative forward is wherever the gyro's `0` happens to be.
