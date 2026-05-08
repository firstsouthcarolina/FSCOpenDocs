# Autonomous

During the autonomous period, the robot operates without driver input. The KitBot's autonomous routine drives forward briefly, then launches fuel for the remainder of the 15-second window. This module covers how to build that routine and wire it into the robot lifecycle.

---

## How Autonomous Works

At match start, the driver station enables the robot in autonomous mode. WPILib calls `autonomousInit()` in `Robot.java`, which schedules an autonomous command. The `CommandScheduler` runs that command in `robotPeriodic()` until it finishes or autonomous ends.

```
Autonomous enabled
    │
    ▼
autonomousInit()
    └── autoChooser.getSelected().schedule()
              │
              ▼
         CommandScheduler runs it every 20 ms via robotPeriodic()
              │
              ▼
         Autonomous ends (15 seconds) or command finishes
```

The `RobotContainer` provides the selected command via `getAutonomousCommand()`. `Robot.java` schedules it. The scheduler runs it.

---

## SendableChooser

`SendableChooser` is a WPILib class that creates a dropdown menu on SmartDashboard. The driver can select which autonomous routine to run before the match starts.

```java
private final SendableChooser<Command> autoChooser = new SendableChooser<>();
```

Adding options:

```java
autoChooser.setDefaultOption("Example Auto", new ExampleAuto(driveSubsystem, fuelSubsystem));
autoChooser.addOption("Do Nothing", new PrintOut("Auto: doing nothing"));
```

`setDefaultOption` adds a choice and marks it as the default — what runs if the driver makes no selection. `addOption` adds additional choices.

Publishing to SmartDashboard:

```java
SmartDashboard.putData("Auto Chooser", autoChooser);
```

This sends the chooser object to the driver station. The dropdown appears in Shuffleboard or SmartDashboard under the key `"Auto Chooser"`.

---

## Getting the Selected Command

In `RobotContainer`:

```java
public Command getAutonomousCommand() {
    return autoChooser.getSelected();
}
```

In `Robot.java`:

```java
@Override
public void autonomousInit() {
    m_robotContainer.getAutonomousCommand().schedule();
}
```

`getSelected()` returns whichever `Command` the driver chose. `.schedule()` hands it to the `CommandScheduler`, which starts running it on the next `robotPeriodic()` tick.

---

## ExampleAuto.java — Full File

```java
package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.SequentialCommandGroup;
import frc.robot.subsystems.CANDriveSubsystem;
import frc.robot.subsystems.CANFuelSubsystem;

public class ExampleAuto extends SequentialCommandGroup {

    public ExampleAuto(CANDriveSubsystem driveSubsystem, CANFuelSubsystem fuelSubsystem) {
        addCommands(
            new AutoDrive(driveSubsystem, 0.5, 0).withTimeout(0.25),
            new Launch(fuelSubsystem).withTimeout(10.0)
        );
    }
}
```

**Step 1 — drive forward:**  
`AutoDrive(driveSubsystem, 0.5, 0)` drives straight at 50% speed. `.withTimeout(0.25)` stops it after 0.25 seconds. At 50% drive speed, the KitBot travels roughly one robot length.

**Step 2 — launch:**  
`Launch(fuelSubsystem).withTimeout(10.0)` runs the launcher for 10 seconds — the remainder of the 15-second autonomous window after the 0.25-second drive. The timeout prevents the command from running into teleop if autonomous ends before 10 seconds elapses.

Total: 0.25 s drive + 10 s launch = 10.25 s. With a 15 s window, the robot finishes with about 4.75 s to spare.

---

## AutoDrive.java (Review)

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
        driveSubsystem.driveArcade(0, 0);   // stop when timeout fires
    }

    @Override
    public boolean isFinished() { return false; }
}
```

`AutoDrive` is always used with `.withTimeout()`. Its `isFinished()` returns `false` indefinitely — the timeout wrapper is what actually stops it.

---

## Wiring It All Together in Robot.java

```java
@Override
public void robotInit() {
    m_robotContainer = new RobotContainer();
}

@Override
public void robotPeriodic() {
    CommandScheduler.getInstance().run();
}

@Override
public void autonomousInit() {
    Command auto = m_robotContainer.getAutonomousCommand();
    if (auto != null) {
        auto.schedule();
    }
}

@Override
public void teleopInit() {
    // Optional: explicitly cancel any still-running auto command
    // CommandScheduler.getInstance().cancelAll();
}
```

The `if (auto != null)` guard protects against `getSelected()` returning null if no option was configured. The KitBot always has a default option, so this never triggers — but it is good defensive practice.

---

## SmartDashboard Basics

SmartDashboard is the driver station's data panel. The KitBot uses it for two things:

1. **Publishing the auto chooser** — `SmartDashboard.putData("Auto Chooser", autoChooser)`
2. **Tunable voltage values** — `SmartDashboard.putNumber("Intaking Feeder V", ...)` and `SmartDashboard.getNumber(...)` in commands

```java
// Write a value to the dashboard (do once, in constructor)
SmartDashboard.putNumber("Drive Speed", 0.5);

// Read it back (do every loop, in execute)
double speed = SmartDashboard.getNumber("Drive Speed", 0.5);
```

Keys are just strings. `putNumber` establishes the key and sets an initial value. `getNumber` reads the current value, returning the default if the key does not exist yet. Always `put` before `get` so the value appears on the dashboard for the driver to see and edit.

---

## Full Autonomous Flow Diagram

```
Robot boots
    │
    ▼
robotInit() → new RobotContainer()
    ├── new CANDriveSubsystem()
    ├── new CANFuelSubsystem()
    ├── configureBindings()
    └── configureAuto()
           ├── autoChooser.setDefaultOption("Example Auto", ...)
           └── SmartDashboard.putData("Auto Chooser", autoChooser)

Driver selects "Example Auto" on dashboard

Autonomous enabled
    │
    ▼
autonomousInit() → autoChooser.getSelected().schedule()
    └── ExampleAuto scheduled

robotPeriodic() × N
    └── CommandScheduler.run()
           ├── AutoDrive executes for 0.25 s, then times out
           └── Launch executes for 10 s, then times out

Autonomous ends (or ExampleAuto finishes)
    │
    ▼
teleopInit() → driver takes control
```

---

## Exercise

1. Write `ExampleAuto.java` and wire `getAutonomousCommand()` into `RobotContainer` and `autonomousInit()` in `Robot.java`. Run the simulation, switch to autonomous mode, and enable the robot. Verify that the autonomous command runs.
2. Add a second autonomous option to `autoChooser` called `"Drive Only"` that just drives forward for 2 seconds and stops. Select it on SmartDashboard and verify it runs instead of `ExampleAuto`.
3. Modify `ExampleAuto` to drive forward for 0.5 seconds instead of 0.25 seconds. How far does the robot travel? What would you change in `Constants.java` to make the drive distance configurable without editing `ExampleAuto.java` directly?
