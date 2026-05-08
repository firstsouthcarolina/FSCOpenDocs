---
icon: lucide/calculator
---

# Operators and Casting

Operators combine values. Casting converts a value from one type to another.

## Arithmetic

```java
int sum = 3 + 2;
int difference = 3 - 2;
int product = 3 * 2;
int quotient = 3 / 2;
int remainder = 3 % 2;
```

`3 / 2` is `1` because both operands are integers. Java keeps the result as an integer.

## Decimal Division

Use `double` when fractional values matter.

```java
double result = 3.0 / 2.0; // 1.5
```

## Comparisons

Comparisons produce booleans.

```java
boolean isFast = speed > 0.75;
boolean isStopped = speed == 0.0;
boolean isDifferent = leftPort != rightPort;
```

Use `==` for primitive equality and `!=` for primitive inequality.

## Boolean Operators

```java
boolean readyToDrive = enabled && joystickConnected;
boolean shouldStop = emergencyStop || batteryLow;
boolean notEnabled = !enabled;
```

## Casting

Casting is explicit conversion.

```java
double average = 7.0 / 2.0;
int roundedDown = (int) average; // 3
```

Casting from `double` to `int` discards the decimal part. It does not round.

## Practice

Write a program that converts encoder ticks to wheel rotations:

```java
int ticks = 8192;
int ticksPerRotation = 2048;
double rotations = (double) ticks / ticksPerRotation;
```

Print the number of rotations.
