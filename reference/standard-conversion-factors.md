---
description: Known-good gearing values for common commercial-off-the-shelf swerve modules
---

# Standard Conversion Factors

These are known-good `gearRatio`/`diameter` values for common COTS swerve modules. Set them either
as the drivetrain-wide default in `modules/physicalproperties.json`, or as a per-module override in
a module's own `gearing` field (module-level `gearing` wins if both are present):

```json
"gearing": {
  "drive": { "gearRatio": 6.75, "diameter": 4 },
  "angle": { "gearRatio": 12.8 }
}
```

- `drive.gearRatio`: motor revolutions per wheel revolution, as `X` where the ratio is `X:1`.
- `drive.diameter`: wheel diameter in **inches**.
- `angle.gearRatio`: motor revolutions per full azimuth revolution, as `X:1`.

The config generator at [config.yagsl.com](https://config.yagsl.com) has these built in as presets.
You generally won't need to type these by hand, but they're listed here for reference and for
teams hand-editing JSON.

## MAX Swerve

| Pinion | `angle.gearRatio` | `drive.gearRatio` | `drive.diameter` |
|---|---|---|---|
| 12T | 46.42 | 5.50 | 3 |
| 13T | 46.42 | 5.08 | 3 |
| 14T | 46.42 | 4.71 | 3 |

## Swerve Drive Specialties (SDS)

| Module | `angle.gearRatio` | `drive.gearRatio` | `drive.diameter` |
|---|---|---|---|
| MK4 L1 | 12.8 | 8.14 | 4 |
| MK4 L2 | 12.8 | 6.75 | 4 |
| MK4 L3 | 12.8 | 6.12 | 4 |
| MK4 L4 | 12.8 | 5.14 | 4 |
| MK4i L1 | 21.4285714286 | 8.14 | 4 |
| MK4i L2 | 21.4285714286 | 6.75 | 4 |
| MK4i L3 | 21.4285714286 | 6.12 | 4 |
| MK5i R1 | 26 | 7.03 | 4 |
| MK5i R2 | 26 | 6.03 | 4 |
| MK5i R3 | 26 | 5.27 | 4 |
| MK4n L1 | 18.75 | 7.13 | 4 |
| MK4n L2 | 18.75 | 5.9 | 4 |
| MK4n L3 | 18.75 | 5.36 | 4 |
| MK5n R1 | 26.09 | 7.03 | 4 |
| MK5n R2 | 26.09 | 6.03 | 4 |
| MK5n R3 | 26.09 | 5.27 | 4 |

## Thrifty Swerve

Steering gear ratio is **25:1** for both configurations below (12T pinion, 3" wheel, NEO-driven).
Check the gear-ratio chart on your Thrifty Swerve for other pinion/output combinations.

| Output gear | `angle.gearRatio` | `drive.gearRatio` | `drive.diameter` |
|---|---|---|---|
| 18T | 25 | 15 | 3 |
| 16T | 25 | 16.9 | 3 |

## Plummer Industries

| Configuration | `angle.gearRatio` | `drive.gearRatio` | `drive.diameter` |
|---|---|---|---|
| Corner, Mid Ratio | 28 | 4 | 2.5 |
| Corner, High Ratio | 28 | 3.25 | 2.5 |

## Deriving your own

If your module isn't listed, compute the reduction from the module's datasheet: `gearRatio` is
motor revolutions per output revolution (drive → wheel, angle → azimuth), and `diameter` is the
wheel diameter in inches, measured on a slightly-worn tread (a fresh, un-worn wheel measures
larger than it will after a few matches, and using the worn number keeps odometry accurate
longer). Overestimating `diameter` makes the robot think it's traveled less than it has, so
odometry-driven autonomous routines undershoot; verify against a real drive-straight test as
described in [Verify Module Locations](../how-to/verify-module-locations.md).
