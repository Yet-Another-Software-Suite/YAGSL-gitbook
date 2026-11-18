# Table of contents

* [Welcome to YAGSL](README.md)

## Get started

* [Your first swerve robot](tutorial/README.md)
* [1. Gather your robot information](tutorial/01-gather-your-robot-information.md)
* [2. Install YAGSL](tutorial/02-install-yagsl.md)
* [3. Generate your configuration](tutorial/03-generate-your-configuration.md)
* [4. Write the swerve code](tutorial/write-the-swerve-code.md)
* [5. Practice in simulation](tutorial/practice-in-simulation.md)
* [6. Check and calibrate your hardware](tutorial/04-verify-and-calibrate-hardware.md)
* [7. Tune and drive](tutorial/05-deploy-and-drive.md)

## Fix a problem

* [Find your problem](how-to/fix-a-problem.md)
* [Troubleshooting flowchart](how-to/troubleshooting-flowchart.md)
* [Determine inversion](how-to/determine-inversion.md)
* [Verify your module locations](how-to/verify-module-locations.md)
* [Fix steering that changes as the robot turns](how-to/the-8-steps.md)
* [Fix swerve drive drift](reference/swerve-drift-causes.md)
* [Fix common SparkMAX/SparkFlex problems](how-to/fix-sparkmax-common-problems.md)

## Tune and extend

* [All guides](how-to/how-to.md)
* [Tune PIDF gains](how-to/tune-pidf-gains.md)
* [Drive to a pose](how-to/drive-to-a-pose.md)
* [Set up PathPlanner](how-to/setup-pathplanner.md)
* [Use a custom gyro](how-to/use-a-custom-gyro.md)
* [Put devices on a different CAN bus](how-to/choose-a-can-bus.md)
* [Access raw hardware devices](how-to/access-raw-hardware-devices.md)
* [Set up AdvantageScope](how-to/set-up-advantagescope.md)
* [View your DataLog in AdvantageScope](how-to/view-your-datalog-in-advantagescope.md)

## Reference

* [All reference pages](reference/reference.md)
* [JSON Configuration Schema](reference/json-schema/README.md)
  * [swervedrive.json](reference/json-schema/swervedrive-json.md)
  * [Module JSON](reference/json-schema/module-json.md)
  * [physicalproperties.json](reference/json-schema/physicalproperties-json.md)
  * [pidfproperties.json](reference/json-schema/pidfproperties-json.md)
  * [Device Type Strings](reference/json-schema/device-types.md)
* [Supported Hardware](reference/hardware/README.md)
  * [Gyroscopes](reference/hardware/gyroscopes.md)
  * [Motor Controllers](reference/hardware/motor-controllers.md)
  * [Absolute Encoders](reference/hardware/absolute-encoders.md)
* [Standard Conversion Factors](reference/standard-conversion-factors.md)
* [Vendordeps and offline install](reference/vendordep-installation.md)
* [API Reference](reference/api-reference.md)

## Upgrading

* [Upgrade to YAGSL 2027](reference/schema-changes.md)
* [Changelog](explanation/changelog.md)

## How it works

* [All explanations](explanation/explanation.md)
* [What is YAGSL?](explanation/what-is-yagsl.md)
* [Swerve Drive Kinematics](explanation/swerve-drive-kinematics.md)
* [Swerve Modules](explanation/swerve-modules.md)
* [Module Behaviors](explanation/module-behaviors.md)
* [Chassis Control](explanation/chassis-control.md)
* [Telemetry, Simulation & Vision](explanation/telemetry-and-vision.md)

## Links and community

* [Config Generator](https://config.yagsl.com)
* [Example Code](https://github.com/Yet-Another-Software-Suite/YAGSL/tree/main/examples)
* [YAMS API Reference](https://yagsl.gitbook.io/yams/api-reference/java-reference)
* [Discord](https://discord.gg/yass)
* [Turkish Translation by 9024](https://callister.gitbook.io/yagsl-turkish)
* [YASS (Yet Another Software Suite)](external-links/yass-yet-another-software-suite/README.md)
  * [YAMS](https://yams.yamgen.com/)
  * [YAMG](https://yamgen.com/)
  * [YALL](https://github.com/Yet-Another-Software-Suite/YALL)
* [Resources](readme/resources.md)
