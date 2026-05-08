---
icon: lucide/triangle-alert
---

# Exceptions

Exceptions represent failures that normal return values cannot handle cleanly. They let code stop the current path and report what went wrong.

## Throwing an Exception

```java
public static double divide(double numerator, double denominator) {
    if (denominator == 0.0) {
        throw new IllegalArgumentException("Denominator cannot be zero");
    }

    return numerator / denominator;
}
```

## Catching an Exception

```java
try {
    double result = divide(10.0, 0.0);
    System.out.println(result);
} catch (IllegalArgumentException ex) {
    System.out.println("Bad input: " + ex.getMessage());
}
```

Only catch an exception when you can do something useful with it. Logging and continuing blindly can hide real bugs.

## Checked and Unchecked

- **Unchecked exceptions** extend `RuntimeException`. They usually signal programming mistakes or invalid arguments.
- **Checked exceptions** must be handled or declared. File IO often uses checked exceptions.

## Finally

`finally` runs whether the `try` succeeds or fails.

```java
try {
    System.out.println("Trying work");
} finally {
    System.out.println("Cleanup");
}
```

## Practice

Write a method named `requirePositive(double value)` that throws `IllegalArgumentException` if the value is zero or negative. Use it before calculating wheel circumference.

!!! success "Fundamentals checkpoint"
    After this page, students have enough Java context to begin WPILib without waiting for the full advanced Java theory sequence.
