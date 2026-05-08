---
icon: lucide/list-checks
---

# Enums with Fields

Enums can store data and behavior. This is useful when each fixed value has its own settings.

```java
public enum StartingPosition {
    LEFT(1.0, 2.0),
    CENTER(4.0, 2.0),
    RIGHT(7.0, 2.0);

    private final double xMeters;
    private final double yMeters;

    StartingPosition(double xMeters, double yMeters) {
        this.xMeters = xMeters;
        this.yMeters = yMeters;
    }

    public double getXMeters() {
        return xMeters;
    }

    public double getYMeters() {
        return yMeters;
    }
}
```

## Why Use This

An enum with fields keeps related constants together. It is cleaner than separate arrays or switch statements that must stay manually synchronized.

## Methods on Enums

```java
public boolean isCenter() {
    return this == CENTER;
}
```

Enums can have constructors, fields, and methods, but enum constructors are not called with `new`.

## Practice

Create an enum named `ArmPreset` with `STOWED`, `LOW`, `MID`, and `HIGH`. Give each preset an angle in degrees and a getter.
