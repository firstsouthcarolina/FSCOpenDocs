---
icon: lucide/shapes
---

# Polymorphism and Abstract Classes

Polymorphism lets code treat different object types through a shared parent type.

```java
abstract class RobotAction {
    public abstract void run();
}

class DriveForward extends RobotAction {
    @Override
    public void run() {
        System.out.println("Driving forward");
    }
}

class StopRobot extends RobotAction {
    @Override
    public void run() {
        System.out.println("Stopping");
    }
}
```

## Using Polymorphism

```java
RobotAction action = new DriveForward();
action.run();
```

The variable type is `RobotAction`, but the object is `DriveForward`. Java calls the overridden method on the actual object.

## Abstract Classes

An abstract class can define shared code and require subclasses to implement missing behavior.

```java
abstract class Sensor {
    public abstract double getValue();

    public boolean isConnected() {
        return !Double.isNaN(getValue());
    }
}
```

## Practice

Create an abstract `Shape` class with `area()`. Implement `Rectangle` and `Circle`, then store both in a `Shape[]` and print each area.
