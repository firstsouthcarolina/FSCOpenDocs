---
icon: lucide/gamepad-2
---

# Controllers and Bindings

Controller bindings connect driver input to commands. Most command-based projects place bindings in `RobotContainer`.

## Controller Object

```java
private final CommandXboxController driverController =
    new CommandXboxController(OperatorConstants.DRIVER_CONTROLLER_PORT);
```

The port must match the Driver Station USB order.

## Buttons

```java
driverController.a().whileTrue(
    new RunCommand(() -> intake.run(0.8), intake)
);

driverController.b().onTrue(
    new InstantCommand(() -> intake.stop(), intake)
);
```

Common trigger methods:

| Method | Behavior |
| --- | --- |
| `onTrue` | Schedule once when pressed |
| `onFalse` | Schedule once when released |
| `whileTrue` | Schedule while held |
| `toggleOnTrue` | Toggle when pressed |

## Axes

Axes are usually read inside default commands.

```java
double forward = -driverController.getLeftY();
double turn = -driverController.getRightX();
```

Many controllers report forward stick motion as negative, so teams often negate the value.

## Deadband

Small joystick noise should not move the robot.

```java
double forward = MathUtil.applyDeadband(-driverController.getLeftY(), 0.05);
```

## Practice

Bind:

- `A` to intake in while held.
- `B` to outtake while held.
- Left stick Y and right stick X to arcade drive.
