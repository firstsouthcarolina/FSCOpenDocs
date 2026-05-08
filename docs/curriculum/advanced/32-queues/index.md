---
icon: lucide/list-start
---

# Queues

A queue is a first-in, first-out data structure. The oldest value is removed first.

## Operations

- `add` or `offer`: enqueue a value.
- `remove` or `poll`: dequeue a value.
- `element` or `peek`: read the front value.
- `isEmpty`: check whether the queue has values.

## Java Queue

```java
import java.util.ArrayDeque;
import java.util.Queue;

Queue<String> events = new ArrayDeque<>();
events.add("robotInit");
events.add("teleopInit");

String next = events.remove();
```

## Offer and Poll

`offer` and `poll` are often friendlier than `add` and `remove` because they avoid exceptions in normal empty-queue handling.

```java
String event = events.poll();

if (event != null) {
    System.out.println(event);
}
```

## Uses

Queues are useful for:

- Event processing.
- Breadth-first search.
- Scheduling tasks.
- Buffering sensor or log data.

## Practice

Simulate a queue of robot events. Add five events, process them in order, and print each processed event.
