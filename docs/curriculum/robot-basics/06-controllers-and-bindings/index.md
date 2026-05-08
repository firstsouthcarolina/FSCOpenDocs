# Controllers and Bindings

`RobotContainer` is the wiring hub of the robot. It instantiates every subsystem, creates every controller, and connects buttons to commands. This module walks through the KitBot's `RobotContainer.java` from top to bottom.

---

## CommandXboxController

The KitBot uses `CommandXboxController` — WPILib's wrapper around a standard Xbox controller that exposes buttons and axes as `Trigger` objects for binding commands.

```java
import edu.wpi.first.wpilibj2.command.button.CommandXboxController;

CommandXboxController driverController   = new CommandXboxController(0);
CommandXboxController operatorController = new CommandXboxController(1);
```

The port number (`0`, `1`) matches the controller's position in the Driver Station application. Port 0 is always the driver; port 1 is always the operator. These come from `OperatorConstants.DRIVER_CONTROLLER_PORT` and `OPERATOR_CONTROLLER_PORT`.

---

## Reading Axes

Axis values are read directly in the `Drive` command, not in `RobotContainer`. `RobotContainer` only passes the controller reference:

```java
// In Drive.java execute():
driveSubsystem.driveArcade(
    -controller.getLeftY()  * DRIVE_SCALING,
     controller.getRightX() * ROTATION_SCALING
);
```

| Method | Axis | Range | KitBot use |
|--------|------|-------|-----------|
| `getLeftY()` | Left stick vertical | -1.0 to 1.0 (up = negative) | Forward/back drive |
| `getRightX()` | Right stick horizontal | -1.0 to 1.0 | Rotation |
| `getLeftTriggerAxis()` | Left trigger | 0.0 to 1.0 | Not used directly — used as Trigger |
| `getRightTriggerAxis()` | Right trigger | 0.0 to 1.0 | Not used directly — used as Trigger |

`getLeftY()` is negated in `Drive` because Xbox controllers report up (pushing forward) as a negative Y value. Without negation, the robot drives backward when you push the stick forward.

---

## Triggers

`CommandXboxController` exposes every button and trigger as a `Trigger` object. A `Trigger` wraps a condition (`BooleanSupplier`) and lets you attach commands to state changes.

```java
// Returns a Trigger that is active when the A button is pressed
operatorController.a()

// Returns a Trigger that is active when left trigger axis exceeds 0.5
operatorController.leftTrigger(0.5)

// Returns a Trigger that is active when the POV is pressed left (270°)
operatorController.povLeft()
```

---

## Binding Methods

| Method | When command runs | When command ends |
|--------|------------------|------------------|
| `onTrue(command)` | Once, when trigger becomes active | When `isFinished()` returns true, or on interrupt |
| `whileTrue(command)` | While trigger is active | When trigger becomes inactive, or `isFinished()` |
| `toggleOnTrue(command)` | Toggles on/off each time trigger becomes active | When toggled off |

The KitBot uses `onTrue` for button presses (run once and stop) and `whileTrue` for held triggers (run while held).

---

## RobotContainer.java — Full File

```java
package frc.robot;

import edu.wpi.first.wpilibj.smartdashboard.SendableChooser;
import edu.wpi.first.wpilibj.smartdashboard.SmartDashboard;
import edu.wpi.first.wpilibj2.command.Command;
import edu.wpi.first.wpilibj2.command.button.CommandXboxController;
import frc.robot.commands.*;
import frc.robot.subsystems.CANDriveSubsystem;
import frc.robot.subsystems.CANFuelSubsystem;
import static frc.robot.Constants.OperatorConstants.*;
import static frc.robot.Constants.PrintConstants.*;

public class RobotContainer {

    // ── Subsystems ────────────────────────────────────────────
    private final CANDriveSubsystem driveSubsystem = new CANDriveSubsystem();
    private final CANFuelSubsystem  fuelSubsystem  = new CANFuelSubsystem();

    // ── Controllers ───────────────────────────────────────────
    private final CommandXboxController driverController =
        new CommandXboxController(DRIVER_CONTROLLER_PORT);
    private final CommandXboxController operatorController =
        new CommandXboxController(OPERATOR_CONTROLLER_PORT);

    // ── Autonomous chooser ────────────────────────────────────
    private final SendableChooser<Command> autoChooser = new SendableChooser<>();

    public RobotContainer() {
        configureBindings();
        configureAuto();
    }

    private void configureBindings() {
        // Driver: default command — drive with left stick (forward) and right stick (turn)
        driveSubsystem.setDefaultCommand(new Drive(driveSubsystem, driverController));

        // Operator face buttons: diagnostic print commands
        operatorController.a().onTrue(new PrintOut(BUTTON_A));
        operatorController.b().onTrue(new PrintOut(BUTTON_B));
        operatorController.x().onTrue(new PrintOut(BUTTON_X));
        operatorController.y().onTrue(new PrintOut(BUTTON_Y));

        // Operator triggers: fuel mechanism
        operatorController.leftTrigger(0.5).whileTrue(new Intake(fuelSubsystem));
        operatorController.rightTrigger(0.5).whileTrue(new LaunchSequence(fuelSubsystem));
        operatorController.povLeft().whileTrue(new Eject(fuelSubsystem));
    }

    private void configureAuto() {
        autoChooser.setDefaultOption("Example Auto", new ExampleAuto(driveSubsystem, fuelSubsystem));
        SmartDashboard.putData("Auto Chooser", autoChooser);
    }

    public Command getAutonomousCommand() {
        return autoChooser.getSelected();
    }
}
```

---

## Walking Through the Bindings

### Default Command

```java
driveSubsystem.setDefaultCommand(new Drive(driveSubsystem, driverController));
```

The `Drive` command runs whenever nothing else requires `driveSubsystem`. During teleop, this means the driver always has control unless another command takes over.

### Diagnostic Buttons

```java
operatorController.a().onTrue(new PrintOut(BUTTON_A));
```

`onTrue` schedules `PrintOut` the moment the A button is pressed. `PrintOut` finishes immediately (its `isFinished()` returns `true` on the first cycle), so it completes before the button is even released.

### Intake (left trigger, held)

```java
operatorController.leftTrigger(0.5).whileTrue(new Intake(fuelSubsystem));
```

`.leftTrigger(0.5)` creates a Trigger that is active when the trigger axis exceeds 0.5. `whileTrue` schedules `Intake` when the trigger is pulled and cancels it (calls `end(true)`) when the trigger is released. The threshold prevents accidental activation from resting pressure.

### LaunchSequence (right trigger, held)

```java
operatorController.rightTrigger(0.5).whileTrue(new LaunchSequence(fuelSubsystem));
```

Same pattern as intake. `LaunchSequence` is a command group (covered in Module 07).

### Eject (POV left, held)

```java
operatorController.povLeft().whileTrue(new Eject(fuelSubsystem));
```

`.povLeft()` is active when the D-pad is pressed left. Same `whileTrue` pattern.

---

## Why Requirements Matter Here

All three fuel commands (`Intake`, `LaunchSequence`, `Eject`) require `fuelSubsystem`. Only one can run at a time. If the operator holds both the left and right triggers simultaneously, whichever was scheduled second cancels the first. This is automatic — no `if` statement needed.

The `Drive` default command requires `driveSubsystem`, which none of the fuel commands touch. So the driver can always drive while the operator works the fuel system simultaneously.

---

## Exercise

1. Write `RobotContainer.java` from scratch. Build and simulate — confirm that driving works with the left and right sticks.
2. Add a binding: when the driver presses the `B` button, print `"Driver B pressed"` to the console using `PrintOut`. Test it in simulation.
3. Change the intake binding from `whileTrue` to `onTrue`. In simulation, press and release the left trigger. What is different about how `Intake` behaves? Why?
