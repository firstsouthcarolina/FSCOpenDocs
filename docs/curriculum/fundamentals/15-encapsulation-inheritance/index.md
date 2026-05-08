---
icon: lucide/lock-keyhole
---

# Encapsulation and Inheritance

Encapsulation protects object state. Inheritance lets one class reuse and specialize another class.

## Encapsulation

Keep fields private and expose behavior through methods.

```java
public class Counter {
    private int value;

    public void increment() {
        value++;
    }

    public int getValue() {
        return value;
    }
}
```

Other code cannot set `value` to an invalid number unless the class allows it.

## Inheritance

Inheritance uses `extends`.

```java
public class Sensor {
    private final int port;

    public Sensor(int port) {
        this.port = port;
    }

    public int getPort() {
        return port;
    }
}

public class DistanceSensor extends Sensor {
    public DistanceSensor(int port) {
        super(port);
    }
}
```

`super(...)` calls the parent constructor.

## When to Use Inheritance

Use inheritance when the child really is a specialized version of the parent. Do not use inheritance just to share a few helper methods.

## Practice

Create a base class named `Device` with a `name` field. Create `MotorDevice` and `SensorDevice` subclasses that call the parent constructor.
