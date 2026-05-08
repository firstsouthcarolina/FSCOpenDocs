# WPILib Setup

Before writing any robot code, you need WPILib — the official software suite for FRC programming. This module walks through installation, project creation, and the tools you will use every session.

---

## What WPILib Installs

The WPILib offline installer bundles everything you need in one package:

| Component | Purpose |
|-----------|---------|
| VS Code (WPILib edition) | The editor, pre-configured for FRC development |
| WPILib extension | Robot project templates, deploy tools, and IntelliSense for WPILib APIs |
| Java 17 JDK | The Java version required by WPILib 2026 |
| GradleRIO | The build system that compiles and deploys robot code |
| Shuffleboard / SmartDashboard | Driver station data visualization |
| Robot Simulation | Run and test robot code without physical hardware |

!!! tip "Always use the WPILib VS Code"
    The WPILib installer creates its own copy of VS Code. Always open it from the **WPILib icon on your desktop** (not a regular VS Code shortcut). It has the correct Java version and extensions pre-configured.

---

## Step 1 — Download and Install WPILib

1. Go to the [WPILib GitHub releases page](https://github.com/wpilibsuite/allwpilib/releases)
2. Download the latest **2026** offline installer for your operating system (`WPILib_Windows-2026.x.x.iso` for Windows)
3. Mount the ISO (double-click on Windows 11) and run `WPILibInstaller.exe`
4. Select **Everything** when prompted for installation options
5. Click **Install for this User** (does not require admin rights)

Installation takes a few minutes. When it finishes, a WPILib icon will appear on your desktop.

---

## Step 2 — Create a New Robot Project

1. Open **WPILib VS Code** (the desktop icon)
2. Press `Ctrl+Shift+P` to open the Command Palette
3. Type `WPILib: Create a new project` and select it
4. Fill in the project wizard:

| Field | KitBot value | What it means |
|-------|-------------|---------------|
| Project type | Template | Start from a template, not a blank file |
| Language | Java | |
| Template | **Command Robot** | Uses the command-based framework |
| Base folder | Your documents folder | Where the project folder will be created |
| Project name | `KitBot` | |
| Team number | Your team number | Used during deploy to find the RoboRIO |
| Enable desktop support | ✓ checked | Allows simulation without a RoboRIO |

5. Click **Generate Project**. VS Code opens the new project automatically.

---

## Project Structure

```
KitBot/
├── src/main/java/frc/robot/
│   ├── Main.java           ← entry point (never edit this)
│   ├── Robot.java          ← robot lifecycle
│   ├── RobotContainer.java ← subsystems + button bindings
│   ├── Constants.java      ← all constants
│   ├── subsystems/         ← one class per mechanism
│   └── commands/           ← one class per action
├── vendordeps/             ← third-party library configs
├── build.gradle            ← GradleRIO build config
└── .wpilib/
    └── wpilib_preferences.json  ← team number
```

The `frc.robot` package is where all your code lives. The template generates empty placeholder files — you will replace their contents as you build the KitBot.

---

## Step 3 — Add REVLib (SparkMax Support)

The KitBot uses REV Robotics SparkMax motor controllers. WPILib does not include their library by default — you add it as a vendor dependency.

1. Press `Ctrl+Shift+P` → `WPILib: Manage Vendor Libraries`
2. Select **Install new libraries (online)**
3. Paste the REVLib URL from the [REV documentation](https://docs.revrobotics.com/brushless/spark-max/revlib)
4. Press Enter — VS Code downloads and adds `REVLib.json` to your `vendordeps/` folder
5. Click **Yes** when prompted to run a Gradle build

After the build completes, `import com.revrobotics.*;` will resolve correctly in your code.

---

## Step 4 — Build the Project

Press `Ctrl+Shift+P` → `WPILib: Build Robot Code` (or press `F5` to simulate, which builds first).

You can also build from the terminal:

```
./gradlew build
```

A successful build prints `BUILD SUCCESSFUL`. Errors appear in the **Problems** panel.

---

## Step 5 — Deploy to a RoboRIO

When you are connected to the robot (over USB or radio):

1. Press `Ctrl+Shift+P` → `WPILib: Deploy Robot Code`

Or from the terminal:

```
./gradlew deploy
```

GradleRIO compiles the code, connects to the RoboRIO over the network, and installs the JAR file. The robot restarts and runs your new code immediately.

!!! note "Team number matters"
    The deploy tool finds the RoboRIO using your team number. If deploy fails with a connection error, verify the team number in `.wpilib/wpilib_preferences.json` matches your RoboRIO configuration.

---

## Step 6 — Simulate the Robot

Desktop simulation lets you test code without physical hardware. Press `F5` or run `WPILib: Simulate Robot Code`. A simulation GUI opens alongside VS Code showing:

- Robot state (enabled/disabled, teleop/auto)
- Joystick inputs (connect a real controller or use the keyboard)
- SmartDashboard values

Simulation catches most logic bugs before you ever touch real hardware.

---

## The GradleRIO Build System

WPILib projects use **GradleRIO** — a Gradle plugin that knows how to compile Java into a fat JAR and deploy it to a RoboRIO.

The key section of `build.gradle`:

```groovy
plugins {
    id "edu.wpi.first.GradleRIO" version "2026.1.1"
}

deploy {
    targets {
        roborio(getTargetTypeClass('RoboRIO')) {
            team = thr_frc.getTeamOrDefault(team)
        }
    }
    artifacts {
        frcJavaArtifact('frcJava') {
            targets << "roborio"
            debug = frc.getDebugOrDefault(false)
        }
    }
}
```

You will almost never need to modify `build.gradle` directly. The WPILib tools handle vendordep management through the Command Palette.

---

## Useful WPILib VS Code Commands

| Command Palette entry | What it does |
|----------------------|-------------|
| `WPILib: Build Robot Code` | Compile without deploying |
| `WPILib: Deploy Robot Code` | Build and deploy to RoboRIO |
| `WPILib: Simulate Robot Code` | Build and run in simulation |
| `WPILib: Manage Vendor Libraries` | Add/update/remove third-party libraries |
| `WPILib: Create a new class` | Generate a new subsystem or command from a template |
| `WPILib: Open SmartDashboard` | Launch the SmartDashboard window |

All WPILib commands start with `WPILib:` — typing that in the Command Palette filters the list.

---

## Exercise

1. Install WPILib and create a new Command Robot project named `KitBot` with your team number.
2. Add REVLib using the vendor library manager.
3. Build the project and confirm `BUILD SUCCESSFUL`.
4. Run the simulation (`F5`) and enable the robot in teleop mode. Observe that nothing happens yet — the template is empty. In the next module you will start adding code.
