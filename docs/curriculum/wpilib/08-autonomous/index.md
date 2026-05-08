---
icon: lucide/route
---

# Autonomous

Autonomous code runs without driver input. A good auto routine is simple, testable, and safe when interrupted.

## Returning an Auto Command

Command-based projects usually expose autonomous through `RobotContainer`.

```java
public Command getAutonomousCommand() {
    return Commands.sequence(
        drivetrain.driveForwardCommand().withTimeout(2.0),
        new InstantCommand(() -> drivetrain.stop(), drivetrain)
    );
}
```

`Robot.java` schedules this command in `autonomousInit()`.

## Canceling Before Teleop

When teleop starts, cancel autonomous if it is still running.

```java
if (autonomousCommand != null) {
    autonomousCommand.cancel();
}
```

This prevents autonomous commands from continuing into driver control.

## Auto Choosers

Use a chooser when drivers need to select from multiple routines.

```java
private final SendableChooser<Command> autoChooser = new SendableChooser<>();

public RobotContainer() {
    autoChooser.setDefaultOption("Drive Forward", driveForwardAuto());
    autoChooser.addOption("Score And Leave", scoreAndLeaveAuto());
    SmartDashboard.putData("Auto Chooser", autoChooser);
}

public Command getAutonomousCommand() {
    return autoChooser.getSelected();
}
```

## Testing

Test autonomous with the robot on blocks before placing it on the floor. Confirm that every routine stops when disabled.

## Practice

Write two autonomous routines:

- Drive forward and stop.
- Run intake for one second, then drive backward and stop.
