---
icon: lucide/file-text
---

# Javadoc

Javadoc comments document public classes, methods, and fields. They are written for the next programmer using your code.

```java
/**
 * Converts wheel rotations into linear distance.
 *
 * @param rotations number of wheel rotations
 * @param wheelDiameterMeters diameter of the wheel in meters
 * @return distance traveled in meters
 */
public static double rotationsToMeters(double rotations, double wheelDiameterMeters) {
    return rotations * Math.PI * wheelDiameterMeters;
}
```

## What to Document

Document:

- Public methods and classes.
- Units for numbers.
- Assumptions and valid ranges.
- Side effects such as changing motor output.

Do not document obvious private helpers unless the helper has tricky behavior.

## Tags

| Tag | Meaning |
| --- | --- |
| `@param` | Explains a parameter |
| `@return` | Explains the returned value |
| `@throws` | Explains when an exception is thrown |

## Bad Javadoc

```java
/**
 * Gets speed.
 */
public double getSpeed() {
    return speed;
}
```

This repeats the method name and adds no useful information.

## Better Javadoc

```java
/**
 * Returns the requested drivetrain speed from -1.0 to 1.0.
 */
public double getSpeed() {
    return speed;
}
```

## Practice

Add Javadoc to a method that converts encoder ticks to meters. Include the units for every numeric parameter.
