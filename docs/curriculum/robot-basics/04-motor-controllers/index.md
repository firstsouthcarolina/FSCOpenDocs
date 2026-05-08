# Motor Controllers

Motor controllers are the hardware between the RoboRIO and the motors. They take low-power commands from the RoboRIO and deliver high-current power to the motors. The KitBot uses **REV Robotics SparkMax** controllers for all six motors. This module explains how to talk to them from Java.

---

## The CAN Bus

The KitBot's motor controllers communicate over **CAN bus** (Controller Area Network) — a single two-wire network that runs from the RoboRIO through all the motor controllers. Each controller has a unique **CAN ID** that you assign with REV Hardware Client.

```
RoboRIO â”€â”€CAN+/CANâ”€â”€â”€ SparkMax(40) â”€â”€ SparkMax(41) â”€â”€ SparkMax(50) â”€â”€ SparkMax(51)
                  â””â”€â”€ SparkMax(11) â”€â”€ SparkMax(21)
```

All six controllers share the same two wires; the IDs prevent them from stepping on each other's messages. If two controllers have the same ID, you get communication errors — this is why CAN IDs are defined in `Constants.java` rather than typed inline.

---

## SparkMax — Constructor

```java
import com.revrobotics.spark.SparkLowLevel.MotorType;
import com.revrobotics.spark.SparkMax;

SparkMax leftLeader = new SparkMax(40, MotorType.kBrushless);
```

`SparkMax(int deviceId, MotorType motorType)`:

- `deviceId` — the CAN ID of this specific controller (must match what is flashed on the hardware)
- `MotorType.kBrushless` — tells the controller it is driving a brushless NEO motor; use `kBrushed` for older CIM-style motors

The constructor establishes communication with the physical controller. If no controller with that ID is on the bus (in simulation, for example), WPILib creates a simulated version that accepts commands but produces no movement.

---

## Inversion

By default, positive values spin the motor in one direction. "Positive" is arbitrary — it depends on how the motor is mounted and wired. You use `setInverted` to flip the direction so your code's convention ("positive = forward") matches reality.

```java
leftLeader.setInverted(true);   // flip left side so both sides drive forward on positive input
```

In the KitBot's drivetrain, the left and right motor pairs face opposite directions physically. Without inversion, commanding "drive forward" would spin one side forward and the other backward. Inverting the left side corrects this.

---

## Current Limits

Motors can draw enormous current when stalled — enough to trip breakers, burn windings, or damage gearboxes. A **smart current limit** caps the current to protect the hardware.

```java
leftLeader.setSmartCurrentLimit(60);  // 60 amps
```

`setSmartCurrentLimit` reduces motor power when the current exceeds the limit. Unlike a hard cut-off, it reduces output gradually, so the motor stays responsive under load without drawing dangerous current.

The KitBot uses 60 A for drive motors — a conservative limit that protects NEO motors during pushing matches.

---

## Following

When two motors drive the same mechanism, one can **follow** the other instead of being commanded separately.

```java
leftFollower.follow(leftLeader);   // leftFollower mirrors leftLeader's output exactly
```

After calling `follow`, you only ever command `leftLeader`. The follower copies the leader's output automatically on every cycle. The KitBot uses this pattern for both the left and right side of the drivetrain:

```
leftLeader  → commanded by Drive command
leftFollower → follows leftLeader automatically

rightLeader  → commanded by Drive command
rightFollower → follows rightLeader automatically
```

---

## Commanding a Motor

### `set(double speed)`

Sets the motor to a **percentage** of full output from -1.0 to 1.0.

```java
leftLeader.set(0.5);   // run at 50% forward
leftLeader.set(-1.0);  // run at 100% reverse
leftLeader.set(0.0);   // stop
```

`set` is the most common method. `DifferentialDrive.arcadeDrive` uses it internally.

### `setVoltage(double volts)`

Sets a specific **voltage** rather than a percentage of max output.

```java
feeder.setVoltage(9.0);    // run at exactly 9 volts
intakeLauncher.setVoltage(10.6);
```

`setVoltage` is preferred for the fuel system because it is battery-independent. A fully charged battery might be 12.5 V; a depleted one might be 11.8 V. With `set(0.75)`, motor speed changes as the battery drains. With `setVoltage(9.0)`, the controller compensates and delivers the same voltage regardless.

### `stopMotor()`

Immediately stops the motor and removes the active output.

```java
feeder.stopMotor();
```

---

## DifferentialDrive

The KitBot's drivetrain uses **arcade drive** — one axis for forward/back speed, one for rotation. WPILib's `DifferentialDrive` class handles the math.

```java
import edu.wpi.first.wpilibj.drive.DifferentialDrive;

DifferentialDrive drive = new DifferentialDrive(leftLeader::set, rightLeader::set);
```

The constructor takes the `set` method references of the left and right leaders. When you call `arcadeDrive`, it computes individual left/right outputs and calls those methods.

### `arcadeDrive(double xSpeed, double zRotation)`

```java
drive.arcadeDrive(0.5, 0.0);    // drive straight forward at 50%
drive.arcadeDrive(0.0, 0.3);    // rotate right at 30%
drive.arcadeDrive(0.5, -0.2);   // drive forward-left
```

`xSpeed` is forward/back (-1 to 1). `zRotation` is left/right turn (-1 to 1). The internal math:

```
left  = xSpeed + zRotation
right = xSpeed - zRotation
```

Both values are clamped to [-1, 1] and passed to the motor `set` methods.

### Motor Safety

`DifferentialDrive` has built-in **motor safety**: if `arcadeDrive` is not called for more than 100 ms, it stops both motors automatically. This prevents motors from running indefinitely if the periodic loop freezes. If you see `Drive output not updated often enough` in the console, it means your `arcadeDrive` call is not being reached every cycle.

---

## Full Drive Subsystem Constructor, Annotated

```java
public CANDriveSubsystem() {
    // 1. Invert left side so positive = forward on both sides
    leftLeader.setInverted(true);

    // 2. Wire up followers — only leaders need to be commanded
    leftFollower.follow(leftLeader);
    rightFollower.follow(rightLeader);

    // 3. Protect motors — followers inherit the leader's limit
    leftLeader.setSmartCurrentLimit(CURRENT_LIMIT);
    rightLeader.setSmartCurrentLimit(CURRENT_LIMIT);

    // 4. Create the arcade drive controller
    drive = new DifferentialDrive(leftLeader::set, rightLeader::set);
}
```

---

## Exercise

1. In `CANDriveSubsystem`, remove the `setInverted(true)` call. Simulate the robot and drive forward. Observe what happens. Explain why, then restore the call.
2. Change `CURRENT_LIMIT` to `20` in `Constants.java`. In simulation, what effect does this have? What would happen on a real robot with heavy load?
3. What does `leftLeader::set` mean as an argument to `DifferentialDrive`? Relate it to method references from [Java Basics Module 30](../../java-fundamentals/30-lambdas/index.md). (Hint: `Robot::new` was used in `Main.java` for the same reason.)
