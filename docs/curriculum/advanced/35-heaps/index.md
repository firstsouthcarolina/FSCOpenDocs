---
icon: lucide/triangle
---

# Heaps

A heap is a tree-shaped structure that quickly returns the highest-priority value. Java provides heaps through `PriorityQueue`.

## PriorityQueue

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> scores = new PriorityQueue<>();
scores.add(80);
scores.add(120);
scores.add(95);

int lowest = scores.remove();
```

By default, Java's `PriorityQueue` is a min-heap, so the smallest value comes out first.

## Max-Heap

Use a comparator to reverse the order.

```java
PriorityQueue<Integer> highScores =
    new PriorityQueue<>(Comparator.reverseOrder());
```

## Custom Priority

```java
public record Task(String name, int priority) {
}

PriorityQueue<Task> tasks = new PriorityQueue<>(
    Comparator.comparingInt(Task::priority)
);
```

## Uses

Heaps are useful for:

- Priority scheduling.
- Finding the smallest or largest value repeatedly.
- Graph algorithms.
- Keeping only the top `k` values from a larger stream.

## Practice

Create a priority queue of repair tasks where lower numbers mean higher priority. Process tasks in priority order.
