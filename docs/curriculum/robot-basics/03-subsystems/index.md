# Subsystems

A subsystem represents one physical mechanism on the robot. The drivetrain is a subsystem. The fuel intake and launcher is a subsystem. Each one gets its own class that encapsulates the hardware it owns and exposes only the actions other code needs to call.

This is the same encapsulation idea from [Java Basics Module 15](../../java-fundamentals/15-simple-classes/index.md) — private fields, public methods — applied to robot hardware.

---

## What a Subsystem Does

- **Owns its hardware** — motors, sensors, pneumatics go here and nowhere else
- **Exposes actions, not internals** — `driveArcade(x, z)` not `getLeftMotor()`
- **Tracks state** — current position, whether a sensor is triggered
- **Provides periodic updates** — logging, sensor reads, dashboard output every 20 ms

One subsystem = one mechanism. Never let two subsystems share a motor. Never let a command directly touch hardware that belongs to a subsystem it does not require.

---

## SubsystemBase

All KitBot subsystems extend `SubsystemBase`:

```java
import edu.wpi.first.wpilibj2.command.SubsystemBase;

public class CANDriveSubsystem extends SubsystemBase {
    // ...
}
```

`SubsystemBase` is an abstract class from WPILib. Extending it (same pattern as [Java Basics Module 21](../../java-fundamentals/21-inheritance/index.md)) registers the subsystem with the `CommandScheduler` automatically so it can participate in requirement checking and default command assignment.

`SubsystemBase` provides:
- `getName()` — the class name by default, used in logging
- `setDefaultCommand(command)` — assigns the command that runs when nothing else requires this subsystem
- `@Override periodic()` — called every 20 ms from `CommandScheduler`

---

## CANDriveSubsystem — Full File

```java
package frc.robot.subsystems;

import com.revrobotics.spark.SparkLowLevel.MotorType;
import com.revrobotics.spark.SparkMax;
import edu.wpi.first.wpilibj.drive.DifferentialDrive;
import edu.wpi.first.wpilibj2.command.SubsystemBase;
import static frc.robot.Constants.DriveConstants.*;

public class CANDriveSubsystem extends SubsystemBase {

    private final SparkMax leftLeader   = new SparkMax(LEFT_LEADER_ID,   MotorType.kBrushless);
    private final SparkMax leftFollower  = new SparkMax(LEFT_FOLLOWER_ID,  MotorType.kBrushless);
    private final SparkMax rightLeader  = new SparkMax(RIGHT_LEADER_ID,  MotorType.kBrushless);
    private final SparkMax rightFollower = new SparkMax(RIGHT_FOLLOWER_ID, MotorType.kBrushless);

    private final DifferentialDrive drive;

    public CANDriveSubsystem() {
        leftLeader.setInverted(true);
        leftFollower.follow(leftLeader);
        rightFollower.follow(rightLeader);

        leftLeader.setSmartCurrentLimit(CURRENT_LIMIT);
        rightLeader.setSmartCurrentLimit(CURRENT_LIMIT);

        drive = new DifferentialDrive(leftLeader::set, rightLeader::set);
    }

    public void driveArcade(double xSpeed, double zRotation) {
        drive.arcadeDrive(xSpeed, zRotation);
    }

    @Override
    public void periodic() {
        // Add logging here as the robot grows
    }
}
```

### What each part does

**`private final SparkMax ...`**  
Motor controllers are `private` (nothing outside this class touches them directly) and `final` (the reference cannot be reassigned after construction). CAN IDs come from `Constants.DriveConstants`.

**`leftLeader.setInverted(true)`**  
The left and right sides of a differential drive face opposite directions physically. Inverting the left side means positive values drive both sides forward when you command forward speed.

**`leftFollower.follow(leftLeader)`**  
The follower motor mirrors the leader's output exactly. You only command the leader; the follower tracks it automatically. This halves the code you need for a 4-motor drivetrain.

**`DifferentialDrive`**  
Takes references to the left and right leader `set` methods and provides `arcadeDrive(xSpeed, zRotation)`, which converts a single forward/turn input into individual left/right motor outputs. Covered in detail in [Module 04](../04-motor-controllers/index.md).

**`driveArcade(double xSpeed, double zRotation)`**  
The only method commands call. It hides the `DifferentialDrive` implementation — commands do not need to know how arcade drive is implemented.

**`periodic()`**  
Empty for now. As you add sensors or debugging, put `SmartDashboard.putNumber(...)` calls here.

---

## CANFuelSubsystem — Full File

```java
package frc.robot.subsystems;

import com.revrobotics.spark.SparkLowLevel.MotorType;
import com.revrobotics.spark.SparkMax;
import edu.wpi.first.wpilibj.smartdashboard.SmartDashboard;
import edu.wpi.first.wpilibj2.command.SubsystemBase;
import static frc.robot.Constants.FuelConstants.*;

public class CANFuelSubsystem extends SubsystemBase {

    private final SparkMax feeder         = new SparkMax(FEEDER_MOTOR_ID,    MotorType.kBrushless);
    private final SparkMax intakeLauncher = new SparkMax(INTAKE_LAUNCHER_ID, MotorType.kBrushless);

    public CANFuelSubsystem() {
        intakeLauncher.setInverted(true);

        // Publish default tuning values so they appear on SmartDashboard
        SmartDashboard.putNumber("Intaking Feeder V",    INTAKING_FEEDER_V);
        SmartDashboard.putNumber("Intaking Launcher V",  INTAKING_LAUNCHER_V);
        SmartDashboard.putNumber("Launching Feeder V",   LAUNCHING_FEEDER_V);
        SmartDashboard.putNumber("Launching Launcher V", LAUNCHING_LAUNCHER_V);
        SmartDashboard.putNumber("SpinUp Feeder V",      SPINUP_FEEDER_V);
    }

    public void setIntakeLauncherRoller(double voltage) {
        intakeLauncher.setVoltage(voltage);
    }

    public void setFeederRoller(double voltage) {
        feeder.setVoltage(voltage);
    }

    public void stop() {
        feeder.stopMotor();
        intakeLauncher.stopMotor();
    }

    @Override
    public void periodic() {}
}
```

### What each part does

**`intakeLauncher.setInverted(true)`**  
The intake/launcher motor faces the opposite direction from the feeder. Inverting it means positive voltage always moves game pieces in the same direction for both motors.

**`SmartDashboard.putNumber(...)`**  
Called once in the constructor, this publishes each tuning value to the driver station dashboard with its constant as the starting value. Drivers can then adjust voltages live between matches without redeploying code.

**`setVoltage(double volts)`**  
Direct voltage control rather than `set(percent)`. Voltage control is more predictable than percent because it does not depend on battery voltage — the motor controller compensates automatically. Used for the fuel system where consistent power matters more than consistent speed.

**`stop()`**  
Cuts power to both motors. Called from `end()` in commands that need to stop the mechanism when they finish.

---

## The Subsystem Pattern Summary

```
Subsystem
├── private final Motor m_motor = new Motor(ID)   ← own the hardware
├── Constructor                                    ← configure hardware
│   ├── setInverted
│   ├── setCurrentLimit
│   └── follow / DifferentialDrive setup
├── public action methods                          ← expose behavior
│   ├── driveArcade(x, z)
│   └── stop()
└── @Override periodic()                          ← 20 ms hook
    └── logging, sensor reads
```

---

## Exercise

1. Create `CANDriveSubsystem.java` in `src/main/java/frc/robot/subsystems/`. Write it from scratch using the file above as a reference (not a copy). Resolve any import errors with VS Code's quick-fix suggestions.
2. Create `CANFuelSubsystem.java` the same way.
3. In `RobotContainer.java`, declare both subsystems as `private final` fields and instantiate them. Confirm the project still builds. (You will add commands and bindings in later modules — for now just verify the subsystems construct without errors.)
