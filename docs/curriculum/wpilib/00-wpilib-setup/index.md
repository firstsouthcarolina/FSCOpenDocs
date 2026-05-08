---
icon: lucide/wrench
---

# WPILib Setup

WPILib provides the robot libraries, Gradle tooling, vendordep support, simulation tools, and WPILib VS Code environment used by FRC Java teams.

## Install WPILib

Use the official WPILib installer for your operating system. It installs a separate WPILib VS Code, a Java toolchain, GradleRIO, and common FRC tools.

!!! warning "Use WPILib VS Code"
    Robot projects should be opened in the WPILib-provided VS Code. A normal VS Code install may not have the right Java, extensions, or WPILib commands.

## Create a Java Project

In WPILib VS Code:

1. Open the command palette.
2. Run **WPILib: Create a new project**.
3. Choose **Template**.
4. Choose **Java**.
5. Choose **Command Robot** for command-based projects.
6. Enter the team number and project name.

## Build the Project

Use the WPILib command palette or the terminal:

```bash
./gradlew build
```

On Windows PowerShell:

```powershell
.\gradlew.bat build
```

## Deploy to the Robot

Connect to the robot by radio, Ethernet, or USB, then run:

```bash
./gradlew deploy
```

Deploying sends compiled code to the robot controller and restarts the robot program.

## Practice

Create a command-based Java project and build it without changing any code. Then find these files:

- `Robot.java`
- `RobotContainer.java`
- `Constants.java`
- `subsystems/`
- `commands/`
