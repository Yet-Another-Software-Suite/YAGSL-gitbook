# How-to

Goal-oriented recipes for a robot that's already running YAGSL. If you're setting up your first
swerve robot, start with the [Tutorial](../tutorial/README.md) section instead. These guides
assume a working config and address a specific task or problem.

Code samples default to WPILib Commands v2 (`swervelib.commands2`, `yams.commands2`), with a
Commands v3 tab or note wherever the API differs.

- [How to tune PIDF gains](tune-pidf-gains.md), including live tuning from a dashboard
- [How to determine inversion](determine-inversion.md)
- [How to verify your module locations](verify-module-locations.md)
- [How to use a custom gyro](use-a-custom-gyro.md): for a heading source YAGSL's parser doesn't
  build, supplied as a `Rotation3d`
- [How to put devices on a different CAN bus](choose-a-can-bus.md): the `canbus` values for the
  SystemCore and Motioncore buses, and what each vendor accepts
- [How to drive to a pose](drive-to-a-pose.md): a single point-to-point PID move, no path planner
  needed
- [How to set up PathPlanner](setup-pathplanner.md): register a YAGSL-built `SwerveDrive` with
  PathPlanner's `AutoBuilder` (Commands v2)
- [How to access raw hardware devices](access-raw-hardware-devices.md): get the actual vendor
  motor controller/encoder/gyro objects `SwerveParser` built
- [The 8 steps](the-8-steps.md): the translational-axis/heading
  coupling debugging procedure
- [How to fix common SparkMAX/SparkFlex problems](fix-sparkmax-common-problems.md)
- [How to set up AdvantageScope](set-up-advantagescope.md)
- [How to view your DataLog in AdvantageScope](view-your-datalog-in-advantagescope.md): opening a
  recorded `.wpilog` after the fact, not live telemetry
- [Troubleshooting flowchart](troubleshooting-flowchart.md)
