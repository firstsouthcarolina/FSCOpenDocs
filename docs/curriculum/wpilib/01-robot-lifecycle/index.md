---
icon: lucide/activity
---

# Robot Lifecycle

The robot program runs inside modes controlled by the Driver Station and field. WPILib calls lifecycle methods when the robot starts, changes mode, or runs periodic updates.

## Key Modes

- **Disabled**: The robot program runs, but outputs should not move mechanisms.
- **Autonomous**: The robot runs without driver input.
- **Teleop**: Drivers control the robot.
- **Test**: Teams can run diagnostics and tuning code.

## Common Methods

```java
public class Robot extends TimedRobot {
    @Override
    public void robotInit() {
        // Runs once when the robot program starts.
    }

    @Override
    public void robotPeriodic() {
        CommandScheduler.getInstance().run();
    }

    @Override
    public void autonomousInit() {
        // Runs once when autonomous starts.
    }

    @Override
    public void teleopInit() {
        // Runs once when teleop starts.
    }
}
```

`robotPeriodic()` runs repeatedly. In a command-based project, it must call the command scheduler so commands and subsystems update.

## Init vs Periodic

Use `Init` methods for one-time setup when a mode starts. Use `Periodic` methods for repeated checks.

```java
@Override
public void teleopInit() {
    drivetrain.stop();
}

@Override
public void teleopPeriodic() {
    // Usually empty in command-based projects.
}
```

## Practice

Add temporary print statements to each lifecycle method in a test project. Run simulation and watch which methods are called as the robot changes modes.
