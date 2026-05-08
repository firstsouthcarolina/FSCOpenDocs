---
icon: lucide/package
---

# Simple Classes

A class defines a kind of object. Objects group data and behavior together.

```java
public class MotorConfig {
    private int port;
    private boolean inverted;

    public MotorConfig(int port, boolean inverted) {
        this.port = port;
        this.inverted = inverted;
    }

    public int getPort() {
        return port;
    }

    public boolean isInverted() {
        return inverted;
    }
}
```

## Fields

Fields store object state.

```java
private int port;
```

Use `private` by default so other code cannot change state carelessly.

## Constructors

A constructor prepares a new object.

```java
MotorConfig leftLeader = new MotorConfig(1, false);
```

`this.port` means the field on the current object.

## Methods

Methods expose useful behavior.

```java
public String describe() {
    return "Port " + port + ", inverted: " + inverted;
}
```

## Practice

Create a `BatteryReading` class with voltage and timestamp fields. Add a method that returns whether the voltage is safe.
