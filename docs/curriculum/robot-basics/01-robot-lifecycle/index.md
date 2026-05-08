# Robot Lifecycle

Every WPILib robot program has the same skeleton: a class that extends `TimedRobot`, a set of lifecycle methods the framework calls automatically, and a 20-millisecond control loop that keeps everything running. This module explains how that skeleton works before you put anything inside it.

---

## The 20 ms Loop

A robot is a real-time system. Motors, sensors, and controllers must be updated continuously — not just once. WPILib calls your periodic methods every **20 milliseconds** (50 times per second). Your code runs inside that loop; the framework handles the timing.

```
─────────────────────────────────────────── time ───▶
│   20 ms  │   20 ms  │   20 ms  │   20 ms  │
│ robotPer │ robotPer │ robotPer │ robotPer │
│ teleopPer│ teleopPer│ teleopPer│ teleopPer│
```

Every method that ends in `Periodic` is called once per loop. Every method that ends in `Init` is called exactly once when the robot enters that mode.

---

## Robot.java — Line by Line

The KitBot's `Robot.java`:

```java
package frc.robot;

import edu.wpi.first.wpilibj.TimedRobot;
import edu.wpi.first.wpilibj2.command.CommandScheduler;

public class Robot extends TimedRobot {
    private RobotContainer m_robotContainer;

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
        m_robotContainer.getAutonomousCommand().schedule();
    }

    @Override
    public void teleopInit() {
        // Cancels any still-running auto command when teleop starts
    }

    @Override
    public void disabledInit() {}
    @Override
    public void disabledPeriodic() {}
    @Override
    public void autonomousPeriodic() {}
    @Override
    public void teleopPeriodic() {}
    @Override
    public void testPeriodic() {}
}
```

### Every token explained

**`extends TimedRobot`**  
`TimedRobot` is a WPILib abstract class that starts the 20 ms loop and calls the lifecycle methods. This is the same inheritance pattern you used in [Java Basics Module 23](../../java-fundamentals/23-polymorphism-abstract/index.md). All the `@Override` methods below are abstract methods defined in `TimedRobot` that you fill in.

**`private RobotContainer m_robotContainer`**  
`RobotContainer` holds all subsystems and button bindings. Declaring it here (in `Robot`) keeps a reference to it alive for the entire program. The `m_` prefix is a WPILib convention for member variables.

**`robotInit()`**  
Called once when the robot program starts. This is where you instantiate `RobotContainer`. Everything in the `RobotContainer` constructor runs here: motors are created, controllers are configured, and button bindings are registered.

**`robotPeriodic()`**  
Called every 20 ms regardless of mode. The single line `CommandScheduler.getInstance().run()` is the heartbeat of the entire command framework — it runs all scheduled commands, checks button triggers, and manages command state. If you forget this line, commands never execute.

**`autonomousInit()`**  
Called once when the robot enters autonomous mode. `getAutonomousCommand()` asks `RobotContainer` which autonomous routine was selected on SmartDashboard, then `.schedule()` registers it with the `CommandScheduler` so it starts running on the next `robotPeriodic()` call.

**`teleopInit()`**  
Called once when the robot enters teleop. The KitBot leaves this empty (any auto command is automatically cancelled by the scheduler when a conflict occurs), but you could explicitly cancel the auto command here for safety.

---

## The Full Lifecycle Diagram

```
Power on
    │
    ▼
robotInit()          ← runs once; create RobotContainer

    ┌──────────────────────────────────────┐
    │  robotPeriodic() every 20 ms always  │
    └──────────────────────────────────────┘

Disabled
    ├── disabledInit()     ← once on enter
    └── disabledPeriodic() ← every 20 ms

Autonomous
    ├── autonomousInit()     ← once on enter; schedule auto command
    └── autonomousPeriodic() ← every 20 ms

Teleop
    ├── teleopInit()     ← once on enter
    └── teleopPeriodic() ← every 20 ms

Test
    ├── testInit()     ← once on enter
    └── testPeriodic() ← every 20 ms
```

The robot always starts in Disabled. The driver station transitions it to Autonomous, then Teleop, or directly to Teleop for practice. Each transition fires the corresponding `Init` method.

---

## The CommandScheduler

`CommandScheduler.getInstance().run()` in `robotPeriodic` does four things every 20 ms:

1. **Checks button triggers** — if a button binding condition is met, it schedules the associated command
2. **Runs active commands** — calls `execute()` on every running command
3. **Checks `isFinished()`** — if a command returns `true`, calls `end(false)` and removes it
4. **Cancels interrupted commands** — if a new command requires a subsystem already in use, calls `end(true)` on the old one

You never call `execute()` or `isFinished()` yourself. The scheduler handles everything — you just define the behavior.

---

## Main.java

```java
package frc.robot;

import edu.wpi.first.wpilibj.RobotBase;

public final class Main {
    private Main() {}

    public static void main(String[] args) {
        RobotBase.startRobot(Robot::new);
    }
}
```

`Main.java` is the Java entry point. `RobotBase.startRobot(Robot::new)` creates the `Robot` instance and hands control to WPILib. You never need to modify this file.

`Robot::new` is a **method reference** — a shorthand for `() -> new Robot()`. It tells WPILib how to construct the robot class without calling the constructor directly.

---

## Why the KitBot Uses TimedRobot (Not CommandRobot)

WPILib ships two base classes:

| | `TimedRobot` | `CommandRobot` |
|--|-------------|----------------|
| What it does | Calls lifecycle methods on a 20 ms schedule | Wraps `TimedRobot`, calls `CommandScheduler.run()` for you |
| `CommandScheduler.run()` | You add it to `robotPeriodic()` manually | Called automatically |
| Flexibility | You control what happens in each period | Less boilerplate |

The KitBot uses `TimedRobot` and calls `CommandScheduler.getInstance().run()` manually. This is the more common pattern in FRC teams' code because it makes the scheduler call visible and lets you add other periodic logic alongside it.

---

## Exercise

1. Open your blank project from Module 00. Read the generated `Robot.java`. Identify which methods are `Init` vs `Periodic` and describe in one sentence what each one should contain for the KitBot.
2. Add a `System.out.println("Robot initialized!")` line to `robotInit()` and `System.out.println("Periodic tick")` to `robotPeriodic()`. Run the simulation. How many times per second does the periodic message print?
3. Explain in your own words: if `CommandScheduler.getInstance().run()` is removed from `robotPeriodic()`, what breaks and why?
