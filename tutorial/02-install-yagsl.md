# 2. Install YAGSL

YAGSL 2027 is built on WPILib 2027 (`2027_alpha7`) and Java 25, and runs on the SystemCore. Install the matching WPILib release first, then create or open your robot project.

## Add the YAGSL vendordep

YAGSL ships as two vendordeps, one per WPILib command framework. Install **exactly one** of them, the one that matches the command framework your robot project uses. They conflict with each other, so do not install both.

| Command framework | Vendordep name      | Vendordep URL                                       |
| ----------------- | ------------------- | --------------------------------------------------- |
| Commands v2       | YAGSL (Commands v2) | `https://cdn.yassrobotics.com/yagsl_commands2.json` |
| Commands v3       | YAGSL (Commands v3) | `https://cdn.yassrobotics.com/yagsl_commands3.json` |

In VS Code, run **WPILib: Manage Vendor Libraries → Install new libraries (online)** and paste the URL for your command framework.

<figure><img src="../.gitbook/assets/modern-yagsl.png" alt=""><figcaption><p>The WPILib Vendor Dependencies panel. If YAGSL is listed there for your WPILib version, you can install it from the panel instead of pasting a URL.</p></figcaption></figure>

{% hint style="info" %}
Not sure which one to pick? Use Commands v2 if your project uses `SubsystemBase` and PathPlanner. Use Commands v3 if your project uses `Mechanism`s, coroutines and the OpMode framework. The rest of this tutorial shows both where they differ.
{% endhint %}

{% hint style="warning" %}
Upgrading an older project? The old `https://yet-another-software-suite.github.io/YAGSL/...` vendordep and maven URLs are stale. Delete the old `yagsl.json` (and the old `yams.json`) from your project's `vendordeps` folder, then install the URLs on this page. YAGSL now resolves from `https://cdn.yassrobotics.com/`.
{% endhint %}

## Add the YAMS vendordep (required)

YAGSL builds a [YAMS](https://yams.yamgen.com/) `SwerveDrive`, so the matching YAMS vendordep is the one library the YAGSL vendordep requires. VS Code offers to install it when you add YAGSL. If it does not, install it the same way:

| Command framework | Vendordep URL                                      |
| ----------------- | -------------------------------------------------- |
| Commands v2       | `https://cdn.yassrobotics.com/yams_commands2.json` |
| Commands v3       | `https://cdn.yassrobotics.com/yams_commands3.json` |

YAMS in turn requires the matching WPILib command framework vendordep (Commands v2 or Commands v3).

## Add vendordeps for your hardware (optional)

YAGSL does not require any hardware vendor library. It only loads a vendor's classes when your JSON configuration uses one of that vendor's devices, so install a vendordep for the hardware your robot actually has and skip the rest:

| Vendor         | Needed for                                                                 |
| -------------- | -------------------------------------------------------------------------- |
| CTRE Phoenix 6 | TalonFX, TalonFXS, Pigeon 2.0, CANcoder                                    |
| REVLib         | SparkMAX, SparkFlex, REV spline encoder                                    |
| ReduxLib       | Canandgyro, Canandmag                                                      |
| ThriftyLib     | Thrifty Nova                                                               |
| StudicaLib     | NavX3, over CAN or USB                                                     |
| None           | SystemCore IMU, and encoders wired to the SystemCore's DIO or analog ports |

Use the 2027 build of each vendor library. If your configuration names a device whose vendor library is not installed, the project still builds, but the robot program fails at startup when `SwerveParser` tries to create that device.

{% hint style="warning" %}
The AndyMark library has not published a `2027_alpha7` vendordep yet. Until it does, the `andymarkhexbore_can` encoder cannot be used with YAGSL 2027. Wire the encoder to a SmartIO port or the angle motor controller instead.
{% endhint %}

[Vendordeps and offline install](../reference/vendordep-installation.md#optional-vendor-libraries) lists which vendor library each device type string needs.

{% hint style="info" %}
If a vendor isn't in the Vendor Dependencies panel yet on your WPILib version, install it the manual way instead. [Installing 3rd-party libraries](https://docs.wpilib.org/en/2027/docs/software/vscode-overview/3rd-party-libraries.html) covers pasting a URL, which you can get from the vendor's own install page.
{% endhint %}

{% hint style="warning" %}
Your vendor's hardware client (REV Hardware Client, Phoenix Tuner X) is a separate tool from the vendordep. Install it too, it's needed for firmware updates and low-level device configuration.
{% endhint %}

## No internet at the robot?

If your network blocks the download, install YAGSL and YAMS from their offline zips instead. See
[Offline install](../reference/vendordep-installation.md#offline).

## Build

Run a Gradle build to pull down the libraries before continuing. Once your build succeeds with all vendordeps installed, move on to [generating your configuration](03-generate-your-configuration.md).
