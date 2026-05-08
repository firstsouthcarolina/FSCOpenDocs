---
icon: lucide/gauge
---

# Motor Controllers

Motor controllers convert robot code commands into electrical output for motors. In FRC, many modern controllers are connected over CAN.

## CAN IDs

Each CAN device needs a unique ID on the robot network. Store those IDs in constants.

```java
public static final int INTAKE_MOTOR_ID = 13;
```

Duplicate IDs cause confusing behavior because two devices may respond to the same command.

## Percent Output

The simplest control is percent output from `-1.0` to `1.0`.

```java
motor.set(0.5);
motor.set(-0.5);
motor.stopMotor();
```

Positive and negative directions depend on wiring, gearbox orientation, and inversion.

## Inversion

Set inversion so positive values match the mechanism's intended positive direction.

```java
motor.setInverted(true);
```

Choose a convention such as "positive intake speed pulls game pieces in" and document it.

## Current Limits and Safety

Configure current limits where supported. A stalled mechanism can burn motors, trip breakers, or brown out the robot.

```java
// Exact configuration APIs differ by motor controller vendor.
```

Use vendor examples for the specific controller your team owns.

## Practice

Make a table for every motor on a robot:

| Mechanism | Controller Type | CAN ID | Positive Direction |
| --- | --- | --- | --- |
| Intake | Spark MAX | 13 | Pulls game piece in |
