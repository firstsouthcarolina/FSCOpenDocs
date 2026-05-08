---
icon: lucide/function-square
---

# Lambdas

A lambda is a compact way to pass behavior as a value. WPILib commands use lambdas heavily.

```java
() -> System.out.println("Hello")
```

This lambda takes no parameters and returns nothing.

## Functional Interfaces

A lambda must match an interface with one abstract method.

```java
Runnable task = () -> System.out.println("Running");
task.run();
```

`Runnable` has one method: `run()`.

## Parameters

```java
Function<Double, Double> square = value -> value * value;
double result = square.apply(3.0);
```

Useful built-in functional interfaces include:

| Interface | Method | Purpose |
| --- | --- | --- |
| `Runnable` | `run()` | No input, no output |
| `Supplier<T>` | `get()` | Produces a value |
| `Consumer<T>` | `accept(T)` | Uses a value |
| `Function<T, R>` | `apply(T)` | Converts a value |
| `Predicate<T>` | `test(T)` | Checks a condition |

## Method References

```java
teams.forEach(System.out::println);
```

`System.out::println` is a method reference. It is shorthand for `team -> System.out.println(team)`.

## Practice

Create a `Predicate<Double>` that returns true when a voltage is below `12.0`. Use it to test several values.
