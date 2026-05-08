---
icon: lucide/filter
---

# Streams

Streams process collections through a pipeline of operations. They are useful for filtering, transforming, and summarizing data.

```java
List<Double> voltages = List.of(12.6, 12.1, 11.8, 12.4);

List<Double> lowVoltages = voltages.stream()
    .filter(voltage -> voltage < 12.0)
    .toList();
```

## Map

`map` transforms each value.

```java
List<String> labels = voltages.stream()
    .map(voltage -> voltage + " V")
    .toList();
```

## Reduce and Summary

```java
double average = voltages.stream()
    .mapToDouble(Double::doubleValue)
    .average()
    .orElse(0.0);
```

`average()` returns an `OptionalDouble` because the list might be empty.

## When Streams Help

Streams are best when the pipeline reads clearly:

```java
long passingCount = scores.stream()
    .filter(score -> score >= 70)
    .count();
```

Use a normal loop when the stream becomes harder to read or needs complicated state.

## Practice

Given a list of team records, use streams to collect only teams with a score above `100`, sorted by team number.
