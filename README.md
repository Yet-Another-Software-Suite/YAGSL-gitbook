---
description: Brought to you by Yet Another Generic Swerve Library (YAGSL)
---

# Welcome to YAGSL

<figure><img src=".gitbook/assets/YAGSL.png" alt=""><figcaption></figcaption></figure>

YAGSL is a swerve library developed by current and former BroncBotz mentors for all FRC teams. It
reads a JSON description of your swerve drive and builds a [YAMS](https://yams.yamgen.com/)
`SwerveDrive` from it: describe your hardware once at [**config.yagsl.com**](https://config.yagsl.com),
and drive. See [What is YAGSL?](explanation/what-is-yagsl.md) for how the pieces fit together.

{% hint style="success" %}
YAGSL 2027 is built on WPILib 2027 (`2027_alpha7`, Java 25) and runs on the SystemCore. It supports both WPILib Commands v2 and Commands v3, each with its own vendordep. See the [Changelog](explanation/changelog.md) for what changed in the 2027 port.
{% endhint %}

## Where to start

| If you... | Go to |
| --- | --- |
| are setting up YAGSL for the first time | [**Your first swerve robot**](tutorial/README.md), seven steps from a bare WPILib project to a driving robot |
| have a robot that isn't driving right | [**Find your problem**](how-to/fix-a-problem.md), which matches what your robot is doing to the page that fixes it |
| have a driving robot and want to do more with it | [**Tune and extend**](how-to/how-to.md): PIDF tuning, drive to a pose, PathPlanner and more |
| are upgrading a 2026 project or a config from before 2026.8.05 | [**Upgrade to YAGSL 2027**](reference/schema-changes.md), before you do anything else |
| need an exact JSON field, device type or API | [**Reference**](reference/reference.md) |

{% embed url="https://www.youtube.com/watch?v=4Tcyn_oj_G0&list=PLdhNPDifsCMLLe5pZoyHGXrMMVxNpJOxP" %}

## Install

Install **exactly one** of these vendordeps, matching the WPILib command framework your robot uses (they conflict with each other):

| Command framework | Vendordep URL                                       |
| ----------------- | --------------------------------------------------- |
| Commands v2       | `https://cdn.yassrobotics.com/yagsl_commands2.json` |
| Commands v3       | `https://cdn.yassrobotics.com/yagsl_commands3.json` |

[Install YAGSL](tutorial/02-install-yagsl.md) covers the YAMS vendordep each one requires and the vendor libraries for your hardware.

{% hint style="warning" %}
The old `https://yet-another-software-suite.github.io/YAGSL/...` vendordep and maven URLs are stale. Remove the old `yagsl.json` from your `vendordeps` folder and install one of the URLs above.
{% endhint %}

{% embed url="https://datawrapper.dwcdn.net/ZVxvE/13/" %}

## Our philosophy

Your program does not revolve around your swerve drive. Your constants file doesn't have to take 10 minutes to find the right option. Different robots should be able to work with the same code: swap the `swerve/` config directory and the same subsystem code drives a different robot.
