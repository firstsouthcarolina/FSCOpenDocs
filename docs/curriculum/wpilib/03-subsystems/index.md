---
icon: lucide/circuit-board
---

# Subsystems

A subsystem represents one robot mechanism or responsibility. Examples include drivetrain, intake, shooter, arm, elevator, and LEDs.

## Basic Subsystem

```java
public class Intake extends SubsystemBase {
    private final SparkMax motor = new SparkMax(
        IntakeConstants.MOTOR_ID,
        MotorType.kBrushless
    );

    public void run(double speed) {
        motor.set(speed);
    }

    public void stop() {
        motor.stopMotor();
    }
}
```

Subsystems own hardware. Commands ask subsystems to do things.

## Periodic

`periodic()` runs once per scheduler loop.

```java
@Override
public void periodic() {
    SmartDashboard.putNumber("Intake Current", motor.getOutputCurrent());
}
```

Use `periodic()` for telemetry and repeated state updates. Avoid hiding major control decisions there unless the subsystem is intentionally stateful.

## Requirements

Commands declare which subsystems they require. The scheduler uses requirements to prevent two commands from fighting over the same hardware.

```java
addRequirements(intake);
```

## Practice

Create a subsystem for one motor. Add `run(double speed)` and `stop()` methods. Then add telemetry showing the commanded speed.
