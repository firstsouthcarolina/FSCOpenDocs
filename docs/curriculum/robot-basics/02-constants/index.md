# Constants

Robot code is full of numbers: motor IDs, controller ports, voltage limits, speed scaling values. Scattering those numbers throughout your code makes them hard to find, easy to break, and impossible to tune. A `Constants.java` file puts them all in one place.

This is the same `public static final` pattern from [Java Basics Module 02](../../java-fundamentals/02-primitive-types/index.md) — applied to a real problem.

---

## The Problem with Magic Numbers

```java
// Bad — what is 40? What is 0.7?
SparkMax leftLeader = new SparkMax(40, MotorType.kBrushless);
double output = input * 0.7;
```

When a motor ID changes (it happens), you have to find every place `40` appears. When you want to tune the drive scaling, you have to hunt through every file. Magic numbers are a maintenance nightmare.

---

## Constants.java — The Full KitBot File

```java
package frc.robot;

public final class Constants {

    public static final class DriveConstants {
        public static final int LEFT_LEADER_ID  = 40;
        public static final int LEFT_FOLLOWER_ID = 41;
        public static final int RIGHT_LEADER_ID  = 50;
        public static final int RIGHT_FOLLOWER_ID = 51;
        public static final int CURRENT_LIMIT = 60;
    }

    public static final class FuelConstants {
        public static final int FEEDER_MOTOR_ID        = 11;
        public static final int INTAKE_LAUNCHER_ID     = 21;

        // Default voltage values — overridable from SmartDashboard
        public static final double INTAKING_FEEDER_V   = -12.0;
        public static final double INTAKING_LAUNCHER_V =   8.5;
        public static final double LAUNCHING_FEEDER_V  =   9.0;
        public static final double LAUNCHING_LAUNCHER_V = 10.6;
        public static final double SPINUP_FEEDER_V     =  -6.0;
    }

    public static final class OperatorConstants {
        public static final int DRIVER_CONTROLLER_PORT   = 0;
        public static final int OPERATOR_CONTROLLER_PORT = 1;
        public static final double DRIVE_SCALING    = 0.7;
        public static final double ROTATION_SCALING = 0.8;
    }

    public static final class PrintConstants {
        public static final String BUTTON_A = "forty one";
        public static final String BUTTON_B = "six seven";
        public static final String BUTTON_X = "war thunder";
        public static final String BUTTON_Y = "for the plot";
    }
}
```

---

## Key Patterns

### `public static final`

Covered in [Java Basics Module 02](../../java-fundamentals/02-primitive-types/index.md):

- `public` — accessible from any class in the project
- `static` — belongs to the class itself, not an instance; access via `Constants.DriveConstants.LEFT_LEADER_ID` without ever calling `new Constants()`
- `final` — value cannot be changed after the class loads; the compiler will catch any attempt to reassign it

### Nested `static` classes for grouping

```java
public static final class DriveConstants { ... }
public static final class FuelConstants  { ... }
```

These are not separate files — they are nested inside `Constants`. The `static` keyword on the nested class means it has no hidden reference back to the outer `Constants` instance (which makes sense, since you never instantiate `Constants` anyway).

The pattern groups constants by subsystem. `DriveConstants` holds everything for the drivetrain; `FuelConstants` holds everything for the fuel mechanism. When you add a new subsystem later, you add a new nested class.

### `ALL_CAPS` naming

Constants use `ALL_CAPS_WITH_UNDERSCORES` by Java convention. This makes them instantly recognizable in code — you can see at a glance that `LEFT_LEADER_ID` is a constant, not a variable.

---

## Using Constants

```java
// In a subsystem constructor:
import static frc.robot.Constants.DriveConstants.*;

SparkMax leftLeader = new SparkMax(LEFT_LEADER_ID, MotorType.kBrushless);
leftLeader.setSmartCurrentLimit(CURRENT_LIMIT);
```

The `import static` form lets you use the constant names directly without qualifying them with the class path. If you prefer explicit imports, you can also write:

```java
import frc.robot.Constants;

SparkMax leftLeader = new SparkMax(
    Constants.DriveConstants.LEFT_LEADER_ID,
    MotorType.kBrushless
);
```

Both styles are correct. The KitBot uses the qualified form.

---

## SmartDashboard-Backed Tunable Values

Some values in the KitBot are not hardcoded — they are read from **SmartDashboard** at runtime so you can change them without redeploying code:

```java
// In CANFuelSubsystem — read the current value from the dashboard,
// fall back to the constant default if no value has been set yet.
double voltage = SmartDashboard.getNumber("Intaking Feeder V",
                     FuelConstants.INTAKING_FEEDER_V);
setFeederRoller(voltage);
```

`SmartDashboard.getNumber(key, defaultValue)` reads a value by name from the dashboard. If the key has never been written, it returns `defaultValue`. This gives you a sensible starting point from `Constants.java` while allowing live tuning from the driver station.

To make a value show up on the dashboard so you can edit it, `putNumber` it once at initialization:

```java
SmartDashboard.putNumber("Intaking Feeder V", FuelConstants.INTAKING_FEEDER_V);
```

---

## What Belongs in Constants?

| Put in Constants | Do not put in Constants |
|-----------------|------------------------|
| Hardware IDs (CAN, port numbers) | Logic that changes at runtime |
| Tuning values (speeds, voltages, timeouts) | Object instances (motors, controllers) |
| Thresholds and limits | State variables |
| Controller port numbers | Method implementations |

If a number appears more than once in your code, or if you might ever want to change it, it belongs in Constants.

---

## Exercise

1. Create `Constants.java` in your project exactly as shown above.
2. Add a new nested class `AutoConstants` with a `DRIVE_SPEED` constant of `0.5` and a `DRIVE_TIME_SECONDS` constant of `0.25`. You will use these in Module 08 (Autonomous).
3. In a temporary `main` method, print every constant in `DriveConstants` and `FuelConstants` using the static access syntax. Verify that the values match the KitBot spec.
