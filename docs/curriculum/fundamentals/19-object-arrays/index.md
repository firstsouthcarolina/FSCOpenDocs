---
icon: lucide/boxes
---

# Object Arrays

An object array stores references to objects.

```java
MotorConfig[] motors = {
    new MotorConfig(1, false),
    new MotorConfig(2, true)
};
```

## References

The array does not contain the object data directly. It contains references to objects.

```java
MotorConfig first = motors[0];
```

Changing the object through `first` changes the same object the array points to.

## Null Values

Object arrays can contain `null`.

```java
MotorConfig[] motors = new MotorConfig[4];

if (motors[0] == null) {
    System.out.println("No motor configured");
}
```

Check for `null` before calling methods on a possibly missing object.

## Looping

```java
for (MotorConfig motor : motors) {
    if (motor != null) {
        System.out.println(motor.getPort());
    }
}
```

## Practice

Create an array of `BatteryReading` objects. Print only the readings that are below `12.0` volts.
