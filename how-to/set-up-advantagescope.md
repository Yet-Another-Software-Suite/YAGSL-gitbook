---
description: >-
  AdvantageScope is a data visualization tool, courtesy of team 6328 Mechanical
  Advantage, which can visualize the swerve drive to give you feedback for
  debugging.
---

# Set up AdvantageScope

## Opening

Since the 2024 season, [AdvantageScope](https://github.com/Mechanical-Advantage/AdvantageScope) has been included with the WPILib installation. There is no external download required, but with every WPILib update you should re-download the [WPILib installer](https://docs.wpilib.org/en/stable/docs/zero-to-robot/step-2/wpilib-setup.html) to get the latest version of WPILib tools.

## Configuring AdvantageScope

1. Connect your laptop to the robot.
2. Open `AdvantageScope (WPILib)`, or in VS Code open the command palette and type `WPILib: Start Tool`, then click `AdvantageScope`.
3. Click `Help`, then `Show Preferences`.

<figure><img src="../.gitbook/assets/AdvantageScope-Preferences.png" alt="AdvantageScope Help menu."><figcaption><p>AdvantageScope's help menu</p></figcaption></figure>

4. In the **Robot Address** field, enter the SystemCore's IP address based on your team number: `10.TE.AM.2`.

<figure><img src="../.gitbook/assets/AdvantageScope-Preferences-IP.png" alt="Robot Address field in AdvantageScope preferences."><figcaption><p>Robot Address field highlighted</p></figcaption></figure>

5. Connect to the robot (or the simulator).

<figure><img src="../.gitbook/assets/AdvantageScope-Connect.png" alt="Connect to Robot menu in AdvantageScope."><figcaption><p>Connect to Robot menu</p></figcaption></figure>

6. On the right side of the window, click `+` to add a new tab.

<figure><img src="../.gitbook/assets/AdvantageScope-Add.png" alt="Add new tab button in AdvantageScope."><figcaption><p>Add new tab</p></figcaption></figure>

7. Add a new 🦀 Swerve tab.

<figure><img src="../.gitbook/assets/AdvantageScope-Swerve.png" alt="Swerve tab in AdvantageScope."><figcaption><p>Swerve tab</p></figcaption></figure>

8. In the Sources field, drag in each `Mechanisms/swerve` field:
   * `Mechanisms/swerve/states/current`
   * `Mechanisms/swerve/states/desired`
   * `Mechanisms/swerve/chassis/current`
   * `Mechanisms/swerve/chassis/desired`

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
You may need to enable your robot before AdvantageScope shows values for these fields.
{% endhint %}

{% hint style="info" %}
`swerve` is the telemetry name you gave the drive with `SwerveDriveConfig.withTelemetry("swerve", ...)`; if you named it something else, look under `Mechanisms/<that name>`. The `current` fields need `TelemetryVerbosity.MID` or higher and the `desired` fields need `TelemetryVerbosity.HIGH`.
{% endhint %}

To see the robot's pose on a 2D/3D field, add a **2D Field** or **3D Field** tab instead and drag in `Mechanisms/swerve/pose`.

## Reading the display

{% hint style="info" %}
The **RED** lines are the measured velocity and position of each swerve module.

The **BLUE** lines are the velocity and position each module was commanded to.
{% endhint %}

Large, persistent gaps between the red and blue lines usually point to a tuning issue. See [how to tune PIDF gains](tune-pidf-gains.md) and [swerve drive drift causes](../reference/swerve-drift-causes.md).
