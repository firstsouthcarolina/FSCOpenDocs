---
icon: lucide/blocks
---

# Methods

Methods give names to reusable behavior. A good method does one clear job.

```java
public static double inchesToMeters(double inches) {
    return inches * 0.0254;
}
```

## Method Parts

```java
public static double clamp(double value, double min, double max)
```

- `public` is visibility.
- `static` means the method belongs to the class, not an object.
- `double` is the return type.
- `clamp` is the method name.
- The values inside parentheses are parameters.

## Return Values

```java
public static boolean isBatterySafe(double voltage) {
    return voltage >= 7.0;
}
```

`void` methods do not return a value.

```java
public static void printStatus(String status) {
    System.out.println(status);
}
```

## Overloading

Two methods can share a name if their parameter lists differ.

```java
public static int square(int value) {
    return value * value;
}

public static double square(double value) {
    return value * value;
}
```

## Practice

Write these methods:

- `feetToMeters(double feet)`
- `isInRange(double value, double min, double max)`
- `printRobotStatus(String name, boolean enabled)`
