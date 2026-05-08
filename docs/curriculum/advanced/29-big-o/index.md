---
icon: lucide/timer
---

# Big O

Big O describes how an algorithm's work grows as the input grows. It focuses on growth rate, not exact stopwatch time.

## Common Growth Rates

| Big O | Name | Example |
| --- | --- | --- |
| `O(1)` | Constant | Read one array element |
| `O(log n)` | Logarithmic | Binary search |
| `O(n)` | Linear | Scan a list |
| `O(n log n)` | Linearithmic | Efficient comparison sorting |
| `O(n^2)` | Quadratic | Nested loops over the same list |

## Constant Time

```java
int first = values[0];
```

One operation does not become more work just because the array is larger.

## Linear Time

```java
for (int value : values) {
    System.out.println(value);
}
```

The loop does work once per value.

## Quadratic Time

```java
for (int a : values) {
    for (int b : values) {
        System.out.println(a + ", " + b);
    }
}
```

If the list doubles, the number of pairs grows by about four times.

## Why FRC Students Should Care

Most robot loops run around every 20 ms. Code that is fine for ten items may be too slow for thousands of path points, log entries, vision targets, or graph nodes.

## Practice

For each method you wrote in the arrays and sorting lessons, identify its Big O complexity.
