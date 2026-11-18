---
description: A swerve drive can be built wrong in more places than you'd think...
---

# Common build issues

Swerve modules have a lot of small parts, and a mistake in any one of them can look like a software problem. This is a lookup of common build issues, grouped by where they happen on the robot. Check these before you change your configuration or your code.

It's important to keep in mind that SEVERAL of these issues could be present on your robot at once.

Prefer to work through it step by step? See [Stage 1: Is it built right?](../how-to/troubleshooting-flowchart.md#stage-1-is-it-built-right) in the troubleshooting flowcharts.

## Swerve module

### Drive

* [ ] The wrong pinion is installed on the drive motor. Some kits come with more than one, such as 12T and 14T, and they're easy to mix up; the robot then drives faster or slower than the gear ratio you configured.
* [ ] One module has a different gear ratio than the others (e.g. a module swapped in from another robot or kit).
* [ ] The wheel tread is worn, or was replaced with a different tread; the wheel diameter is no longer what you configured.
* [ ] The wheel tread is installed loosely or unevenly, so the wheel isn't round.
* [ ] The bevel gears are missing grease, or have debris in them; the module needs more current to move than the others.

### Azimuth (angle/steering)

* [ ] Gears are binding or too tight; the module doesn't turn freely by hand.
* [ ] A bearing is missing, cracked, or seated crooked.
* [ ] A set screw, shaft collar, or retaining clip is loose; the wheel or a gear can slip on its shaft.
* [ ] The module was assembled differently from the manufacturer's guide (e.g. a gear flipped, a spacer left out, or a part from a different version of the module).

### Absolute encoder

* [ ] Magnet is not secured to the swerve module and slips while in motion; absolute encoder offsets would need constant readjustment until the magnet is secured (loctite/glue it in).
* [ ] Magnet is not centered over the encoder.
* [ ] The encoder is too far from the magnet to read it reliably. The vendor's documentation says how far is too far.
* [ ] Magnetic encoder itself is not secured to the module.
* [ ] The encoder is mounted on the wrong shaft or gear, so it doesn't turn exactly once for each turn of the wheel's azimuth.

### Motor

* [ ] Motor doesn't have a firm connection to the module shaft; it will "twitch" while running if a current limit is set.
* [ ] Motor mounting screws are loose, so the pinion doesn't mesh fully with its gear.
* [ ] Motor is dying; it needs more stator current to move than the same motor on the other modules.

## Wiring

Incorrect, worn, or loose wiring produces different symptoms depending on which connector is affected.

**CAN**

* [ ] A CAN connector is loose or a wire is broken; every device after it on the bus drops out.
* [ ] The CAN bus isn't terminated at the end of the chain.
* [ ] CAN high and CAN low are swapped at a connector.
* [ ] A device is wired to a different CAN bus than the one it's configured on.

**Encoder**

* [ ] The encoder cable is loose, pinched, or not latched into its port.
* [ ] The encoder is plugged into the wrong motor controller or the wrong SmartIO channel.

**Power**

* [ ] A motor controller's power leads are loose, or the crimps are poor.
* [ ] A motor controller is on a breaker that's too small for it, and trips under load.
* [ ] The main breaker or battery connector is loose; the robot browns out when it accelerates.

## Gyroscope

* [ ] The gyro isn't mounted firmly; it moves or shakes while the robot drives.
* [ ] The gyro isn't mounted flat, or is mounted on its side without `gyroAxis` set to match.
* [ ] The gyro is mounted near a motor or another source of vibration.

## Robot

* [ ] The battery is old or worn; it fails a load test even when fully charged.
* [ ] The frame isn't square, so the modules aren't where the configured locations say they are.
* [ ] Robot center of gravity is not ideal.
* [ ] The SystemCore is nearly out of storage.
