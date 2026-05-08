---
icon: lucide/list-tree
---

# Enums

An enum defines a fixed set of named values. Enums are safer than loose strings because Java can check them at compile time.

```java
public enum RobotMode {
    DISABLED,
    AUTONOMOUS,
    TELEOP,
    TEST
}
```

## Using an Enum

```java
RobotMode mode = RobotMode.TELEOP;

if (mode == RobotMode.TELEOP) {
    System.out.println("Driver control");
}
```

Enum values can be compared with `==`.

## Switch with Enums

```java
switch (mode) {
    case DISABLED:
        System.out.println("Disabled");
        break;
    case AUTONOMOUS:
        System.out.println("Autonomous");
        break;
    case TELEOP:
        System.out.println("Teleop");
        break;
    case TEST:
        System.out.println("Test");
        break;
}
```

## When to Use Enums

Use enums for values like:

- Robot mode
- Starting position
- Game piece type
- Arm state
- Alliance color

## Practice

Create an enum named `GamePiece` with `CONE`, `CUBE`, and `NONE`. Write a method that prints what the robot is currently holding.
