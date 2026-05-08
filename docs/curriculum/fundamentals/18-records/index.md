---
icon: lucide/id-card
---

# Records

A record is a compact class for immutable data.

```java
public record Pose2dSimple(double xMeters, double yMeters, double headingDegrees) {
}
```

Java automatically creates:

- Private final fields.
- A constructor.
- Accessor methods named `xMeters()`, `yMeters()`, and `headingDegrees()`.
- Useful `equals`, `hashCode`, and `toString` methods.

## Using a Record

```java
Pose2dSimple pose = new Pose2dSimple(1.0, 2.0, 90.0);
System.out.println(pose.xMeters());
```

Record fields cannot be changed after construction.

## Validation

Use a compact constructor to validate data.

```java
public record BatteryReading(double voltage) {
    public BatteryReading {
        if (voltage < 0.0) {
            throw new IllegalArgumentException("Voltage cannot be negative");
        }
    }
}
```

## When to Use Records

Use records for simple data carriers such as coordinates, measurements, configuration values, and parsed input. Use a normal class when the object has changing state.

## Practice

Create a `MotorPort` record with `int port` and `String name`. Reject negative port numbers.
