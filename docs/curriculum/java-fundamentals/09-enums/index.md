# Enums

An **enum** (enumeration) is a type whose only valid values are a fixed, named set of constants. Use one whenever a variable should hold exactly one of a small list of named options.

---

## The Problem Enums Solve

Without enums, you might represent a drive mode as an `int`:

```java
int driveMode = 0;  // 0 = arcade, 1 = tank, 2 = slow
```

This works, but nothing stops you from writing `driveMode = 99`. The compiler accepts it; the bug shows up at runtime. Using a `String` is no better — typos like `"arcaed"` compile fine and fail silently.

An enum makes invalid values impossible:

```java
DriveMode mode = DriveMode.ARCADE;  // only ARCADE, TANK, or SLOW are legal
mode = DriveMode.TURBO;             // compile error — TURBO does not exist
```

---

## Declaring an Enum

```java
public enum DriveMode {
    ARCADE, TANK, SLOW
}
```

Save this in its own file: `DriveMode.java`. Each identifier (`ARCADE`, `TANK`, `SLOW`) is a constant of type `DriveMode`. By convention they are written in `ALL_CAPS`.

---

## Using Enum Values

```java
DriveMode mode = DriveMode.ARCADE;

System.out.println(mode);            // ARCADE
System.out.println(mode.name());     // ARCADE  (String)
System.out.println(mode.ordinal());  // 0  (zero-based declaration order)
```

Comparing enums with `==` is safe — unlike `String`, there is exactly one instance of each constant, so reference equality and value equality are the same thing:

```java
if (mode == DriveMode.ARCADE) {
    System.out.println("Arcade mode active");
}
```

---

## Enums in `switch`

This is where enums shine. The compiler knows every possible value, so it can warn you if a `case` is missing:

```java
switch (mode) {
    case ARCADE:
        System.out.println("Left stick: forward/back, right stick: turn");
        break;
    case TANK:
        System.out.println("Left stick: left side, right stick: right side");
        break;
    case SLOW:
        System.out.println("Arcade at reduced speed");
        break;
}
```

Notice the `case` labels use the constant name alone (`ARCADE`), not `DriveMode.ARCADE` — inside a `switch` on an enum, Java knows the type.

---

## Iterating All Values

`EnumType.values()` returns an array of every constant in declaration order:

```java
for (DriveMode m : DriveMode.values()) {
    System.out.println(m.ordinal() + ": " + m);
}
// 0: ARCADE
// 1: TANK
// 2: SLOW
```

---

## A Robotics Example — `MotorType`

When you write:

```java
new SparkMax(40, MotorType.kBrushless);
```

`MotorType` is an enum defined inside REVLib. Its constants are `kBrushless` and `kBrushed`. The constructor accepts a `MotorType` — not an `int`, not a `String` — so it is impossible to pass an invalid motor type. You will see this exact pattern in Module 04 of the robot curriculum.

---

## Enums vs `public static final` Constants

| | `public static final int` | `enum` |
|--|--------------------------|--------|
| Type safety | No — any `int` is accepted | Yes — only declared constants are valid |
| Readable in print/debug | No — prints `1`, not `"TANK"` | Yes — prints `"TANK"` |
| Works in `switch` | Yes | Yes (cleaner — no fall-through accidents) |
| Compiler warns on missing `case` | No | Yes |
| Can carry data per constant | No | Yes — covered in [Module 19](../19-enums-with-fields/index.md) after classes |

Use `public static final` for a single numeric or string constant (a CAN ID, a port number). Use an enum when you have a set of related named options where only those options should be valid.

---

## Exercise

1. Declare a `Direction` enum with constants `NORTH`, `SOUTH`, `EAST`, `WEST`. Write a method `opposite(Direction d)` that returns the opposite direction using a `switch`. Test all four cases.
2. Declare a `RobotState` enum: `DISABLED`, `AUTONOMOUS`, `TELEOP`, `TEST`. Print all four values and their ordinals using `RobotState.values()`.
3. Write a small program that stores a `DriveMode` variable, lets the user type `"arcade"`, `"tank"`, or `"slow"`, and updates the variable using `DriveMode.valueOf(input.toUpperCase())`. Print the active mode after each input. What happens if the user types an invalid string?
