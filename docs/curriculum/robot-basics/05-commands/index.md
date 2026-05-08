# Commands

A command represents a single action the robot takes. Driving, intaking, launching — each is a command. The `CommandScheduler` manages which commands run, and subsystems enforce that no two commands fight over the same hardware.

Commands follow the same abstract class pattern from [Java Basics Module 23](../../java-fundamentals/23-polymorphism-abstract/index.md): you extend `Command`, override four lifecycle methods, and the framework calls them at the right time.

---

## The Command Lifecycle

Every command goes through the same four steps:

```
schedule()
    │
    ▼
initialize()   ← called once when the command starts
    │
    ▼
execute()      ← called every 20 ms while the command runs
    │
    ▼
isFinished()   ← checked every 20 ms; return true to end
    │
    ▼
end(boolean interrupted)  ← called once when the command stops
                             interrupted=true if cancelled by another command
```

Override only the methods you need. WPILib provides empty default implementations, so you only write what your command actually requires.

---

## Requirements

```java
addRequirements(driveSubsystem);
```

A requirement declares which subsystems a command uses. The `CommandScheduler` enforces that only one command can require a given subsystem at a time. If a new command requires a subsystem that is already in use, the scheduler cancels the old command (calls `end(true)`) and starts the new one.

Always call `addRequirements` in the constructor. If you forget, two commands can accidentally run on the same hardware simultaneously — motors get conflicting commands and the behavior is undefined.

---

## Default Commands

A **default command** runs on a subsystem whenever no other command requires it. The `Drive` command is the default for `CANDriveSubsystem` — it runs continuously during teleop whenever nothing else is driving.

```java
// In RobotContainer:
driveSubsystem.setDefaultCommand(new Drive(driveSubsystem, driverController));
```

Default commands are never "finished" — `isFinished()` always returns `false`. If a default command returns `true`, the subsystem would have no active command and the hardware would sit idle.

---

## Drive.java — Annotated

```java
package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.Command;
import edu.wpi.first.wpilibj2.command.button.CommandXboxController;
import frc.robot.subsystems.CANDriveSubsystem;
import static frc.robot.Constants.OperatorConstants.*;

public class Drive extends Command {
    private final CANDriveSubsystem driveSubsystem;
    private final CommandXboxController controller;

    public Drive(CANDriveSubsystem driveSubsystem, CommandXboxController controller) {
        this.driveSubsystem = driveSubsystem;
        this.controller = controller;
        addRequirements(driveSubsystem);   // ← claim the drive subsystem
    }

    @Override
    public void execute() {
        driveSubsystem.driveArcade(
            -controller.getLeftY()  * DRIVE_SCALING,     // forward/back (inverted: push forward = positive)
             controller.getRightX() * ROTATION_SCALING   // turn
        );
    }

    @Override
    public boolean isFinished() {
        return false;   // runs forever — this is a default command
    }
}
```

`getLeftY()` is negated because the Xbox controller reports up (push forward) as negative Y — the opposite of the robot's "forward is positive" convention. Multiplying by `DRIVE_SCALING` (0.7) caps the maximum speed at 70%, making the robot easier to control.

`initialize()` and `end()` are not overridden because Drive has no startup or cleanup logic.

---

## Intake.java — Annotated

```java
public class Intake extends Command {
    private final CANFuelSubsystem fuelSubsystem;

    public Intake(CANFuelSubsystem fuelSubsystem) {
        this.fuelSubsystem = fuelSubsystem;
        addRequirements(fuelSubsystem);
    }

    @Override
    public void execute() {
        fuelSubsystem.setIntakeLauncherRoller(
            SmartDashboard.getNumber("Intaking Launcher V", FuelConstants.INTAKING_LAUNCHER_V));
        fuelSubsystem.setFeederRoller(
            SmartDashboard.getNumber("Intaking Feeder V", FuelConstants.INTAKING_FEEDER_V));
    }

    @Override
    public void end(boolean interrupted) {
        fuelSubsystem.stop();   // stop motors when the trigger is released
    }

    @Override
    public boolean isFinished() {
        return false;   // runs as long as the trigger is held
    }
}
```

`end(boolean interrupted)` is important here: it stops the motors when the operator releases the left trigger. If you omit it, the motors keep spinning after the command ends.

The `SmartDashboard.getNumber` reads the live tuning value from the dashboard on every `execute()` call — so the operator can adjust intake voltage during a match and see the effect immediately.

---

## Launch.java — Annotated

```java
public class Launch extends Command {
    private final CANFuelSubsystem fuelSubsystem;

    public Launch(CANFuelSubsystem fuelSubsystem) {
        this.fuelSubsystem = fuelSubsystem;
        addRequirements(fuelSubsystem);
    }

    @Override
    public void execute() {
        fuelSubsystem.setIntakeLauncherRoller(
            SmartDashboard.getNumber("Launching Launcher V", FuelConstants.LAUNCHING_LAUNCHER_V));
        fuelSubsystem.setFeederRoller(
            SmartDashboard.getNumber("Launching Feeder V", FuelConstants.LAUNCHING_FEEDER_V));
    }

    @Override
    public boolean isFinished() {
        return false;
    }
    // Note: no end() override — motors are intentionally left running
    // when this command is used inside LaunchSequence
}
```

Unlike `Intake`, `Launch` does not call `stop()` in `end()`. This is intentional: when `Launch` is used as the final step of `LaunchSequence`, the motors should keep spinning until the operator releases the trigger. The trigger binding's `whileTrue` will cancel the command and the scheduler will call `end(true)` — but since there is no override, WPILib's default `end` (which does nothing) runs instead.

---

## Eject.java

```java
public class Eject extends Command {
    private final CANFuelSubsystem fuelSubsystem;

    public Eject(CANFuelSubsystem fuelSubsystem) {
        this.fuelSubsystem = fuelSubsystem;
        addRequirements(fuelSubsystem);
    }

    @Override
    public void execute() {
        // Reverse intake voltages to push game pieces back out
        fuelSubsystem.setIntakeLauncherRoller(
            -SmartDashboard.getNumber("Intaking Launcher V", FuelConstants.INTAKING_LAUNCHER_V));
        fuelSubsystem.setFeederRoller(
            -SmartDashboard.getNumber("Intaking Feeder V", FuelConstants.INTAKING_FEEDER_V));
    }

    @Override
    public void end(boolean interrupted) {
        fuelSubsystem.stop();
    }

    @Override
    public boolean isFinished() { return false; }
}
```

Negating the intake voltages reverses both motors, pushing game pieces back out through the intake.

---

## SpinUp.java

```java
public class SpinUp extends Command {
    private final CANFuelSubsystem fuelSubsystem;

    public SpinUp(CANFuelSubsystem fuelSubsystem) {
        this.fuelSubsystem = fuelSubsystem;
        addRequirements(fuelSubsystem);
    }

    @Override
    public void execute() {
        fuelSubsystem.setIntakeLauncherRoller(
            SmartDashboard.getNumber("Launching Launcher V", FuelConstants.LAUNCHING_LAUNCHER_V));
        fuelSubsystem.setFeederRoller(
            SmartDashboard.getNumber("SpinUp Feeder V", FuelConstants.SPINUP_FEEDER_V));
    }

    @Override
    public boolean isFinished() { return false; }
}
```

`SpinUp` runs the launcher at full speed but holds the feeder back (`SPINUP_FEEDER_V = -6V`) so no game pieces enter until the launcher is up to speed. It is used inside `LaunchSequence` (Module 07) with a timeout before `Launch` takes over.

---

## PrintOut.java

```java
public class PrintOut extends Command {
    private final String message;

    public PrintOut(String message) {
        this.message = message;
    }

    @Override
    public void initialize() {
        System.out.println(message);   // print once when the button is pressed
    }

    @Override
    public boolean isFinished() {
        return true;   // finishes immediately after initialize()
    }
}
```

`PrintOut` is a one-shot command: it runs `initialize()`, then `isFinished()` returns `true` on the same cycle, so `end(false)` is called immediately. It never calls `execute()` at all. No `addRequirements` call — it does not use any subsystem, so it can run in parallel with anything.

This pattern is useful for diagnostic buttons during testing and for any command that just needs to fire once.

---

## AutoDrive.java

```java
public class AutoDrive extends Command {
    private final CANDriveSubsystem driveSubsystem;
    private final double xSpeed;
    private final double zRotation;

    public AutoDrive(CANDriveSubsystem driveSubsystem, double xSpeed, double zRotation) {
        this.driveSubsystem = driveSubsystem;
        this.xSpeed     = xSpeed;
        this.zRotation  = zRotation;
        addRequirements(driveSubsystem);
    }

    @Override
    public void execute() {
        driveSubsystem.driveArcade(xSpeed, zRotation);
    }

    @Override
    public void end(boolean interrupted) {
        driveSubsystem.driveArcade(0, 0);   // stop when done
    }

    @Override
    public boolean isFinished() {
        return false;   // always run until cancelled — pair with withTimeout()
    }
}
```

`AutoDrive` drives at a fixed speed until cancelled externally (by a `.withTimeout()` wrapper in the autonomous command). `end()` stops the drive to prevent the robot from coasting after the command finishes.

---

## Command Anatomy Summary

```
public class MyCommand extends Command {

    // 1. Store references to subsystems and parameters
    private final MySubsystem subsystem;

    // 2. Constructor: store references, call addRequirements
    public MyCommand(MySubsystem subsystem) {
        this.subsystem = subsystem;
        addRequirements(subsystem);
    }

    // 3. initialize() — one-time setup (optional)
    @Override public void initialize() { ... }

    // 4. execute() — runs every 20 ms
    @Override public void execute() { ... }

    // 5. isFinished() — return true when done
    @Override public boolean isFinished() { return false; }

    // 6. end() — cleanup when stopped (optional)
    @Override public void end(boolean interrupted) { ... }
}
```

---

## Exercise

1. Create all seven command files: `Drive`, `Intake`, `Launch`, `Eject`, `SpinUp`, `PrintOut`, `AutoDrive`. Build the project after each one to catch import errors early.
2. For `Drive`, temporarily change `isFinished()` to return `true`. Run the simulation and hold the forward stick. What happens? Why? Restore it to `return false`.
3. Explain why `Launch` does not call `stop()` in `end()` but `Intake` does. What would happen to the fuel mechanism during autonomous if `Launch` called `stop()` prematurely?
