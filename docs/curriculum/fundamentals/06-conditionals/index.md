---
icon: lucide/git-branch
---

# Conditionals

Conditionals let a program choose what to do.

```java
if (batteryVoltage < 7.0) {
    System.out.println("Battery is too low");
} else {
    System.out.println("Battery is ready");
}
```

## `else if`

Use `else if` when exactly one branch should run.

```java
if (speed > 0.0) {
    System.out.println("Moving forward");
} else if (speed < 0.0) {
    System.out.println("Moving backward");
} else {
    System.out.println("Stopped");
}
```

## Guard Clauses

A guard clause handles an invalid or special case early.

```java
if (!enabled) {
    System.out.println("Robot disabled");
    return;
}

System.out.println("Driving");
```

Guard clauses keep the main logic less nested.

## Switch

`switch` is useful when one value has several named cases.

```java
switch (mode) {
    case "auto":
        System.out.println("Autonomous");
        break;
    case "teleop":
        System.out.println("Teleoperated");
        break;
    default:
        System.out.println("Unknown mode");
        break;
}
```

## Practice

Write a program that prints a battery status:

- Below `7.0`: unsafe
- `7.0` through `11.9`: low
- `12.0` or higher: ready
