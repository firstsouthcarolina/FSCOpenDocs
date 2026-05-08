---
icon: lucide/sliders-horizontal
---

# Constants

Constants give names to robot-wide values such as motor ports, controller ports, physical measurements, and control gains.

## Why Constants Matter

Hard-coded values make robot code brittle:

```java
leftMotor.set(0.35);
```

A named constant explains intent:

```java
leftMotor.set(DriveConstants.ALIGN_SPEED);
```

## Organizing Constants

A common pattern is nested static classes in `Constants.java`.

```java
public final class Constants {
    private Constants() {
    }

    public static final class OperatorConstants {
        public static final int DRIVER_CONTROLLER_PORT = 0;

        private OperatorConstants() {
        }
    }

    public static final class DriveConstants {
        public static final int LEFT_LEADER_ID = 1;
        public static final int RIGHT_LEADER_ID = 2;
        public static final double MAX_SPEED_METERS_PER_SECOND = 4.5;

        private DriveConstants() {
        }
    }
}
```

Private constructors prevent accidental object creation.

## Units

Put units in names when the type is just a number.

```java
public static final double WHEEL_DIAMETER_METERS = 0.1524;
public static final double TRACK_WIDTH_METERS = 0.62;
```

Robot code becomes easier to review when units are visible at the call site.

## Practice

Create constants for:

- Driver controller port
- Intake motor CAN ID
- Shooter target RPM
- Arm stowed angle in degrees
