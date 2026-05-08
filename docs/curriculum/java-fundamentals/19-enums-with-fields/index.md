# Enums with Fields and Constructors

[Module 09](../09-enums/index.md) introduced enums as named constant sets — a way to represent a closed group of values safely. Now that you know how to write classes with fields and constructors, you can see the rest of the picture: an enum is a special kind of class, and its constants can carry data.

---

## The Problem

A plain enum is type-safe but carries no data:

```java
public enum DriveMode { ARCADE, TANK, SLOW }
```

If you need to know the speed scaling values for each mode, you have to look them up in a separate constants file or put them in a `switch` statement every time. That coupling is fragile — if you add a new mode, you have to remember to update every `switch`.

---

## Adding Fields to an Enum

Each enum constant can have its own field values. You declare the fields, provide a constructor, and pass the values directly in the constant list:

```java
public enum DriveMode {
    ARCADE(0.7, 0.8),   // driveScaling, rotationScaling
    TANK(1.0, 1.0),
    SLOW(0.4, 0.4);

    public final double driveScaling;
    public final double rotationScaling;

    DriveMode(double driveScaling, double rotationScaling) {
        this.driveScaling    = driveScaling;
        this.rotationScaling = rotationScaling;
    }
}
```

The constructor runs **once per constant** when the class is first loaded — not each time you use the enum. Fields on enum constants are always `public final`: each constant's values are fixed and readable directly.

```java
DriveMode mode = DriveMode.ARCADE;
System.out.println(mode.driveScaling);     // 0.7
System.out.println(mode.rotationScaling);  // 0.8
```

---

## Why This Is Better Than a Switch

Without enum fields, every piece of code that needs scaling values must look them up:

```java
// Scattered logic — must update every switch if you add a new mode
double scale;
switch (mode) {
    case ARCADE: scale = 0.7; break;
    case TANK:   scale = 1.0; break;
    case SLOW:   scale = 0.4; break;
    default: scale = 0.5;
}
```

With enum fields, the data travels with the constant:

```java
// Self-contained — adding a new mode means adding one constant, nothing else
double scale = mode.driveScaling;
```

This is the same encapsulation principle from classes: the data lives with the thing it describes.

---

## Enum Methods

Enums can also have methods. A method on an enum operates on the constant's own fields:

```java
public enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS  (4.869e+24, 6.0518e6),
    EARTH  (5.976e+24, 6.37814e6);

    private final double mass;     // kg
    private final double radius;   // m

    Planet(double mass, double radius) {
        this.mass   = mass;
        this.radius = radius;
    }

    static final double G = 6.67300E-11;

    double surfaceGravity() {
        return G * mass / (radius * radius);
    }

    double surfaceWeight(double otherMass) {
        return otherMass * surfaceGravity();
    }
}
```

```java
double earthWeight = 75.0;   // kg
double mass = earthWeight / Planet.EARTH.surfaceGravity();

for (Planet p : Planet.values()) {
    System.out.printf("Weight on %s: %.2f%n", p, p.surfaceWeight(mass));
}
// Weight on MERCURY: 28.33
// Weight on VENUS: 67.88
// Weight on EARTH: 75.00
```

---

## Abstract Methods in Enums

An enum can declare an abstract method and have each constant provide its own implementation — the same polymorphism pattern from [Module 23](../23-polymorphism-abstract/index.md), applied to enum constants:

```java
public enum Operation {
    ADD {
        @Override public double apply(double x, double y) { return x + y; }
    },
    SUBTRACT {
        @Override public double apply(double x, double y) { return x - y; }
    },
    MULTIPLY {
        @Override public double apply(double x, double y) { return x * y; }
    };

    public abstract double apply(double x, double y);
}
```

```java
double result = Operation.ADD.apply(3, 4);      // 7.0
double product = Operation.MULTIPLY.apply(3, 4); // 12.0
```

Each constant provides its own `apply` body. This keeps all the operation logic together in one place rather than scattered across switch statements.

---

## Enum Fields vs. `public static final` Constants

| | Enum with fields | `public static final` |
|--|-----------------|----------------------|
| Type safety | Compile-time guaranteed | Any type, no grouping |
| Associated data | Built in | Separate variables per value |
| `switch` support | Yes — compiler warns on missing cases | No — just `int` or `String` |
| Iteration | `values()` — built in | Manual array required |
| Polymorphism | Abstract methods per constant | Not possible |

Use an enum with fields when constants naturally carry associated data and you want the data and the constant to travel together.

---

## Exercise

1. Create an enum `Gear` with constants `FIRST`, `SECOND`, `THIRD`, `FOURTH`, `REVERSE`. Give each constant two `public final double` fields: `speedRatio` and `torqueRatio`. Use values of your choice. Print all gears and their ratios with a `for-each` loop over `Gear.values()`.

2. Create an enum `MotorProfile` with constants `HIGH_SPEED`, `BALANCED`, `HIGH_TORQUE`. Each constant should carry a `public final int currentLimit` (amps) and `public final double speedScaling`. Add a method `boolean isWithinCurrentLimit(int actual)` that returns `true` if `actual <= currentLimit`. Test it in a tester class.

3. Create an enum `RobotState` with constants `DISABLED`, `AUTO`, `TELEOP`, `TEST`. Add an abstract method `String describe()` and implement it in each constant to return a short sentence about what the robot does in that state. Call `describe()` on each in a loop.
