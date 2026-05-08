---
icon: lucide/play
---

# Commands

A command describes an action the robot can run. Commands use subsystems and are scheduled by WPILib.

## Instant Command

Use an instant command for a one-time action.

```java
new InstantCommand(() -> intake.stop(), intake);
```

## Run Command

Use a run command for behavior that should continue while scheduled.

```java
new RunCommand(() -> intake.run(0.8), intake);
```

## Start-End Command

Use start-end behavior when setup and cleanup are both important.

```java
new StartEndCommand(
    () -> intake.run(0.8),
    () -> intake.stop(),
    intake
);
```

## Command Requirements

The last argument in many command factories is the required subsystem.

```java
Commands.run(() -> drivetrain.arcadeDrive(speed, rotation), drivetrain);
```

Requirements prevent command conflicts. If a new command requiring the same subsystem starts, the old one is interrupted.

## Default Commands

A default command runs whenever nothing else is using that subsystem.

```java
drivetrain.setDefaultCommand(
    new RunCommand(
        () -> drivetrain.arcadeDrive(
            -driverController.getLeftY(),
            -driverController.getRightX()
        ),
        drivetrain
    )
);
```

## Practice

Write one command that runs an intake while held and stops the intake when released.
