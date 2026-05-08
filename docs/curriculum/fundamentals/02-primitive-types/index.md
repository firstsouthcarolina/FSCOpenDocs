---
icon: lucide/binary
---

# Primitive Types

Primitive types store simple values directly. They are the building blocks for calculations, decisions, and sensor readings.

## Common Primitive Types

| Type | Example | Use |
| --- | --- | --- |
| `int` | `42` | Whole numbers |
| `double` | `3.14` | Decimal numbers |
| `boolean` | `true` | True or false decisions |
| `char` | `'A'` | One character |

```java
int teamNumber = 281;
double batteryVoltage = 12.4;
boolean robotEnabled = true;
char startingPosition = 'B';
```

## Variables

A variable has a type, a name, and a value.

```java
int motorPort = 3;
motorPort = 4;
```

Java checks types at compile time. An `int` variable cannot store text.

## Constants

Use `final` when a value should not change.

```java
final double WHEEL_DIAMETER_INCHES = 6.0;
```

Constants make robot code easier to tune because important values have names.

## Practice

Create variables for a drivetrain:

- Left motor port
- Right motor port
- Wheel diameter
- Whether the robot is enabled

Print each value on its own line.
