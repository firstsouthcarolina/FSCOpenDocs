---
icon: lucide/download
---

# Installation and Notes

Java programs need a Java Development Kit, an editor, and a terminal. FRC students usually get all three from the WPILib installer because it includes a known-good Java toolchain and a WPILib-flavored VS Code.

## What to Install

- **WPILib VS Code** for robot programming and local Java practice.
- **Git** for source control.
- **A terminal** such as PowerShell, Terminal, or the VS Code integrated terminal.

If your team already has a programming laptop set up for WPILib, use that environment for these lessons.

## Your First Workspace

Create a folder outside of Downloads for practice code. Good names are boring and searchable:

```text
frc-java-practice/
```

Inside it, keep one folder per lesson or exercise. Do not put every file on the desktop.

## Compile and Run

A single-file Java program can be compiled and run from a terminal:

```bash
javac Main.java
java Main
```

`javac` turns source code into bytecode. `java` runs that bytecode on the Java Virtual Machine.

## Style Rules

- Class names use `PascalCase`: `RobotState`, `DriveConfig`.
- Variable and method names use `camelCase`: `leftSpeed`, `calculateRPM`.
- Constants use `UPPER_SNAKE_CASE`: `MAX_SPEED`.
- File names match public class names: `Main.java` contains `public class Main`.

!!! tip "Practice habit"
    Keep examples small. A student who can write and run five tiny programs will learn faster than a student who stares at one large broken file.
