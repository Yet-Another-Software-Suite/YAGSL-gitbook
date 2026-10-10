# modules/pidfproperties.json (and pidfproperties_sim.json)

Default closed-loop PID + feedforward gains shared by every module. Parsed into
`swervelib.core.parser.json.PIDFPropertiesJson` (each of `drive`/`angle` is a
`swervelib.core.parser.PIDFConfig`).

```json
{
  "drive": {
    "p": 0.5,
    "i": 0.0,
    "d": 0.01
  },
  "angle": {
    "p": 2.0,
    "i": 0.0,
    "d": 0.1
  }
}
```

| Field | Type   | Required | Description                                                          |
| ----- | ------ | -------- | ------------------------------------------------------------------------ |
| `p`   | number | yes      | Proportional gain.                                                    |
| `i`   | number | yes      | Integral gain.                                                        |
| `d`   | number | yes      | Derivative gain.                                                      |
| `s`   | number | no       | `kS` — static friction feedforward (`SimpleMotorFeedforward`).         |
| `v`   | number | no       | `kV` — velocity feedforward. If left as `0` for the **drive** motor, the parser auto-derives it from the motor's free speed. |
| `a`   | number | no       | `kA` — acceleration feedforward.                                       |

Both `drive` and `angle` are required top-level keys.

## `pidfproperties_sim.json`

Optional. If present, `SwerveParser` uses it instead of `pidfproperties.json` whenever
`RobotBase.isSimulation()` is `true`. It has the same shape as above. Use it to tune simulation gains
separately from your real robot's gains, without needing to swap files by hand.

{% hint style="warning" %}
There is no more `f` (feedforward) or `iz` (integral zone) field, and no more `output` min/max clamp
object — gains are now expressed as plain PID plus a `SimpleMotorFeedforward`-style `s`/`v`/`a`. See
[Schema Changes](../schema-changes.md).
{% endhint %}

See [How to Tune PIDF Gains](../../how-to/tune-pidf-gains.md) for a tuning procedure and starting-point values.
