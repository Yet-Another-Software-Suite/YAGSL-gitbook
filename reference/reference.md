# Reference

Information-oriented lookup material: exact JSON field names/types, supported hardware, and the
YAMS API surface YAGSL hands you. Come here when you already know what you're doing and need a
precise answer. For step-by-step guidance see [Tutorial](../tutorial/README.md) or
[How-to Guides](../how-to/how-to.md).

- [JSON Configuration Schema](json-schema/README.md): every field in `swervedrive.json`, module
  files, `physicalproperties.json`, and `pidfproperties.json`.
- [Supported Hardware](hardware/README.md): gyro, motor controller, and absolute encoder type
  tables plus per-device notes.
- [Standard Conversion Factors](standard-conversion-factors.md): gear ratios/wheel diameters for
  common COTS swerve modules.
- [Vendordep Installation](vendordep-installation.md): the two YAGSL vendordeps (Commands v2 and
  Commands v3) and which vendor libraries you need for your hardware.
- [API Reference](api-reference.md): the `swervelib` packages, the `SwerveParser` entry points, and
  where to find docs for `SwerveDrive`, `SwerveModule`, and friends (they're YAMS classes).
- [Schema Changes](schema-changes.md): migrating a 2026 project to YAGSL 2027, and migrating a
  pre-2026.8.05 `swerve/` config directory.
- [Swerve Drive Drift: Causes and Tuning Order](swerve-drift-causes.md): hardware/software/config
  causes of drift, and the fixed order to tune them out.
