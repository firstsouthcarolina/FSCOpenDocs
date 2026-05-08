# Robot Basics

A hands-on curriculum for writing FRC robot code from scratch. By the end, you will have rebuilt the [2026 FSC KitBot](https://github.com/FIRSTTeam102/2026KitBot) — a differential drive robot with a fuel intake and launcher — using WPILib's command-based framework.

This curriculum assumes you have completed [Java Basics](../java-fundamentals/index.md) through at least Module 23 (OOP, inheritance, polymorphism, and abstract classes). Every WPILib pattern maps directly to something you already know.

---

## Module Overview

| # | Module | Description |
|---|--------|-------------|
| 00 | [WPILib Setup](00-wpilib-setup/index.md) | Installing WPILib, creating a command-based project, project structure, and deploying to a RoboRIO |
| 01 | [Robot Lifecycle](01-robot-lifecycle/index.md) | `TimedRobot`, the 20 ms loop, init/periodic method pairs, and how `Robot.java` wires everything together |
| 02 | [Constants](02-constants/index.md) | `public static final` fields, nested static classes for grouping, and the KitBot `Constants.java` explained |
| 03 | [Subsystems](03-subsystems/index.md) | `SubsystemBase`, encapsulating hardware, `periodic()`, and writing `CANDriveSubsystem` and `CANFuelSubsystem` |
| 04 | [Motor Controllers](04-motor-controllers/index.md) | CAN bus, `SparkMax` from REVLib, current limits, inversion, follower motors, and `DifferentialDrive` |
| 05 | [Commands](05-commands/index.md) | The command lifecycle (`initialize`/`execute`/`end`/`isFinished`), requirements, default commands, and every KitBot command explained |
| 06 | [Controllers and Bindings](06-controllers-and-bindings/index.md) | `CommandXboxController`, reading axes and triggers, `onTrue`/`whileTrue`, and writing `RobotContainer` |
| 07 | [Command Groups](07-command-groups/index.md) | `SequentialCommandGroup`, `ParallelCommandGroup`, inline composition, and rebuilding `LaunchSequence` |
| 08 | [Autonomous](08-autonomous/index.md) | `SendableChooser`, `SmartDashboard`, `AutoDrive`, and the `ExampleAuto` full-autonomous routine |

---

## What You Are Building

The KitBot is a tank-drive robot with two mechanisms:

```
┌─────────────────────────────────────────┐
│              2026 KitBot                │
│                                         │
│  Drive (4× SparkMax, DifferentialDrive) │
│  ┌──────────────────────────────────┐   │
│  │  Left Leader  (ID 40)            │   │
│  │  Left Follower (ID 41) → follows │   │
│  │  Right Leader (ID 50)  inverted  │   │
│  │  Right Follower (ID 51) → follows│   │
│  └──────────────────────────────────┘   │
│                                         │
│  Fuel System (2× SparkMax)              │
│  ┌──────────────────────────────────┐   │
│  │  Feeder (ID 11)                  │   │
│  │  Intake/Launcher (ID 21)         │   │
│  └──────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

**Driver controller (port 0):** Left stick Y = forward/back, Right stick X = turn  
**Operator controller (port 1):** Left trigger = intake, Right trigger = launch, POV left = eject

---

## How This Maps to Java Basics

| Java Basics concept | Where it appears in the KitBot |
|---------------------|-------------------------------|
| Classes and objects ([Module 15](../java-fundamentals/15-simple-classes/index.md)) | Every subsystem and command is a class |
| Inheritance and `extends` ([Module 21](../java-fundamentals/21-inheritance/index.md)) | `CANDriveSubsystem extends SubsystemBase`, `Drive extends Command` |
| Abstract methods ([Module 23](../java-fundamentals/23-polymorphism-abstract/index.md)) | `initialize()`, `execute()`, `end()`, `isFinished()` are abstract in `Command` |
| `public static final` constants ([Module 02](../java-fundamentals/02-primitive-types/index.md)) | `Constants.java` — all motor IDs and tuning values |
| Method overriding `@Override` ([Module 21](../java-fundamentals/21-inheritance/index.md)) | Every `initialize()`, `execute()` etc. you write is an override |
| Constructor patterns ([Module 15](../java-fundamentals/15-simple-classes/index.md)) | Motor controllers initialized in subsystem constructors |

---

## Module Details

### Module 00 — WPILib Setup

- WPILib offline installer — what it includes (VS Code, tools, Java 17)
- Creating a project: **Command Robot** template, team number, package name
- Project structure: `src/main/java/frc/robot/`, `build.gradle`, `vendordeps/`, `.wpilib/`
- Adding a vendor library: REVLib for SparkMax motors
- Simulating the robot vs deploying to the RoboRIO
- The GradleRIO build system — `./gradlew build`, `./gradlew deploy`
- Useful WPILib VS Code shortcuts

---

### Module 01 — Robot Lifecycle

- `Robot.java extends TimedRobot` — the main entry point
- The 20 ms loop: why robot code runs in a repeating cycle
- Method pairs: `robotInit` / `robotPeriodic`, `autonomousInit` / `autonomousPeriodic`, `teleopInit` / `teleopPeriodic`, `disabledInit` / `disabledPeriodic`
- `RobotContainer` — instantiated in `robotInit`, holds all subsystems and bindings
- How `CommandScheduler.getInstance().run()` drives the command framework
- Why `autonomousInit` schedules the autonomous command and `teleopInit` cancels it

---

### Module 02 — Constants

- Why constants belong in a dedicated class — single source of truth, easy tuning
- `public static final` — value set at compile time, cannot be changed
- Nested static classes: `DriveConstants`, `FuelConstants`, `OperatorConstants`
- CAN bus IDs — what they are and why they must be unique
- SmartDashboard-backed values — reading tunable numbers at runtime

---

### Module 03 — Subsystems

- A subsystem = one physical mechanism on the robot
- `SubsystemBase` — the WPILib base class; provides `getName()`, `register()`, and scheduler integration
- Declaring motor controllers as `private final` fields
- The constructor initializes hardware; methods expose actions
- `@Override periodic()` — runs every 20 ms; use for logging and sensor reads
- Full walkthrough: `CANDriveSubsystem` and `CANFuelSubsystem`

---

### Module 04 — Motor Controllers

- The CAN bus — a single two-wire network carrying commands to all motor controllers
- `SparkMax` from REVLib — constructor takes CAN ID and motor type (`kBrushless`)
- `SmartCurrentLimit` — protects motors from stall damage
- `setInverted(true)` — reverses positive direction on a motor
- `follow(SparkMax leader)` — mirrors the leader's output exactly
- `DifferentialDrive` — takes left and right leaders, provides `arcadeDrive(xSpeed, zRotation)`
- `setVoltage(volts)` — direct voltage control vs `set(percent)` speed control

---

### Module 05 — Commands

- A command = one action the robot takes
- The four lifecycle methods: `initialize()` (once on start), `execute()` (every 20 ms), `end(boolean interrupted)` (once on stop), `isFinished()` (returns true to stop)
- `addRequirements(subsystem)` — locks the subsystem; only one command per subsystem at a time
- Default commands — run whenever no other command requires the subsystem
- Full walkthrough: `Drive`, `Intake`, `Launch`, `Eject`, `SpinUp`, `PrintOut`

---

### Module 06 — Controllers and Bindings

- `CommandXboxController` — wraps a standard Xbox controller and exposes trigger-based bindings
- Reading axes: `getLeftY()`, `getRightX()`, `getLeftTriggerAxis()`
- Button triggers: `.a()`, `.b()`, `.leftTrigger(threshold)`, `.povLeft()`
- `onTrue(command)` — run once when pressed; `whileTrue(command)` — run while held
- `RobotContainer` structure: instantiate → bind → expose autonomous
- Full walkthrough of the KitBot `RobotContainer`

---

### Module 07 — Command Groups and Composition

- `SequentialCommandGroup` — commands run one after another
- `ParallelCommandGroup` — commands run simultaneously (group ends when all finish)
- `ParallelRaceGroup` — commands run simultaneously (group ends when any finishes)
- Inline composition: `.andThen()`, `.withTimeout()`, `.alongWith()`, `.raceWith()`
- `LaunchSequence` reconstruction — `SpinUp` with timeout, then `Launch`

---

### Module 08 — Autonomous

- `SendableChooser<Command>` — dropdown on SmartDashboard for selecting auto routines
- `SmartDashboard.putData()` — publishes the chooser to the driver station
- `getSelected()` — retrieves the chosen command in `autonomousInit`
- `AutoDrive` — takes fixed speed and rotation, drives for a timed duration
- `ExampleAuto` — drive forward 0.25 s, then launch for 10 s
- Wiring autonomous into `Robot.java`

---

## Sources

- **[2026 FSC KitBot](https://github.com/FIRSTTeam102/2026KitBot)** — the reference codebase; all examples are drawn directly from this repository
- **[WPILib Documentation](https://docs.wpilib.org)** — official reference for all WPILib APIs
- **[REVLib Documentation](https://docs.revrobotics.com)** — SparkMax motor controller API
