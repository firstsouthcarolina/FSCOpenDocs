# Queues

A queue is a **First In, First Out (FIFO)** data structure. The first element added is the first one removed — like a line at a counter. Elements enter at the **back** (rear) and leave from the **front**.

---

## Core Operations

| Operation | Description |
|-----------|-------------|
| `enqueue(value)` | Add an element to the back |
| `dequeue()` | Remove and return the front element |
| `peek()` | Return the front element without removing it |
| `isEmpty()` | Return `true` if the queue has no elements |
| `size()` | Return the number of elements |

---

## Queue Backed by a Linked List

The most natural implementation uses a linked list with references to both the **front** (for dequeue) and the **back** (for enqueue). Both operations are O(1).

```java
public class LinkedQueue {
    private Node front;
    private Node back;
    private int size;

    private static class Node {
        int data;
        Node next;
        Node(int data) { this.data = data; }
    }

    public boolean isEmpty() { return front == null; }
    public int size() { return size; }

    public void enqueue(int value) {
        Node newNode = new Node(value);
        if (back != null) {
            back.next = newNode;
        }
        back = newNode;
        if (front == null) {
            front = newNode;
        }
        size++;
    }

    public int dequeue() {
        if (isEmpty()) throw new java.util.NoSuchElementException();
        int value = front.data;
        front = front.next;
        if (front == null) back = null;   // queue is now empty
        size--;
        return value;
    }

    public int peek() {
        if (isEmpty()) throw new java.util.NoSuchElementException();
        return front.data;
    }
}
```

When the last element is dequeued, both `front` and `back` must be set to `null` to avoid a stale `back` reference.

---

## Queue Backed by a Circular Array

An array can implement a queue efficiently using a **circular buffer**: two indices (`front` and `back`) wrap around using the modulo operator, so the array is reused without shifting.

```java
public class CircularArrayQueue {
    private int[] data;
    private int front;
    private int back;
    private int size;

    public CircularArrayQueue(int capacity) {
        data = new int[capacity];
        front = 0;
        back = 0;
        size = 0;
    }

    public boolean isEmpty() { return size == 0; }
    public int size() { return size; }

    public void enqueue(int value) {
        if (size == data.length) throw new RuntimeException("Queue is full");
        data[back] = value;
        back = (back + 1) % data.length;
        size++;
    }

    public int dequeue() {
        if (isEmpty()) throw new java.util.NoSuchElementException();
        int value = data[front];
        front = (front + 1) % data.length;
        size--;
        return value;
    }

    public int peek() {
        if (isEmpty()) throw new java.util.NoSuchElementException();
        return data[front];
    }
}
```

`(index + 1) % capacity` wraps the index back to 0 when it reaches the end of the array. This avoids wasting array space when elements are dequeued from the front.

---

## Visualizing a Queue

```
enqueue(1) → enqueue(2) → enqueue(3) → dequeue() → peek()

After enqueue(1):  front → [1] ← back
After enqueue(2):  front → [1][2] ← back
After enqueue(3):  front → [1][2][3] ← back
After dequeue():   front → [2][3] ← back    returns 1
peek():            front → [2][3] ← back    returns 2 (no removal)
```

---

## Using `java.util.Queue`

Java's standard library provides the `Queue` interface, with `LinkedList` and `ArrayDeque` as the common implementations. `ArrayDeque` is preferred for queue use:

```java
import java.util.ArrayDeque;
import java.util.Queue;

Queue<String> queue = new ArrayDeque<>();

queue.offer("Alice");     // enqueue (prefer offer over add — returns false on failure)
queue.offer("Bob");
queue.offer("Charlie");

System.out.println(queue.peek());    // Alice (no removal)
System.out.println(queue.poll());    // Alice (removes it)
System.out.println(queue.size());    // 2
```

| `Queue` method | Behavior on failure |
|----------------|---------------------|
| `add(e)` | Throws exception |
| `offer(e)` | Returns `false` |
| `remove()` | Throws exception |
| `poll()` | Returns `null` |
| `element()` | Throws exception |
| `peek()` | Returns `null` |

Prefer `offer`, `poll`, and `peek` — they are null-safe and do not crash on empty queues.

---

## Use Cases

**Task scheduling** — Jobs waiting to be processed by a worker enter the back of the queue and are processed in the order they arrived.

**Breadth-First Search** — Graph traversal uses a queue to process nodes level by level (covered in Module 44).

**Command queues in robotics** — WPILib's command-based framework queues robot commands that execute in order. Understanding queue semantics directly maps to how autonomous routines are structured.

**Keyboard input buffers** — Keystrokes are buffered in a queue; the operating system processes them in order.

---

## Stack vs Queue

| | Stack (LIFO) | Queue (FIFO) |
|--|-------------|-------------|
| Add | `push` — top | `enqueue` — back |
| Remove | `pop` — top | `dequeue` — front |
| Peek | top | front |
| Real-world analogy | Stack of plates | Line at a checkout |
| Typical use | Undo, recursion, DFS | Scheduling, BFS, buffering |

---

## Exercise

1. Implement a `LinkedQueue` (linked-list backed) for `String` objects. Add `enqueue`, `dequeue`, `peek`, `isEmpty`, and `size`. Write a `main` method that enqueues five names, then dequeues and prints them all. Observe that they come out in the same order they went in.
2. Write a method `reverse(Queue<Integer> q)` that reverses the contents of a queue using only a stack as auxiliary storage. (Push all elements onto a stack, then pop them back into the queue.)
3. Simulate a simple print queue: users can add print jobs (a `String` description) and a printer processes one job at a time. Implement this with a `Queue<String>`. Add five jobs, then process them one at a time, printing `"Printing: <job>"` for each.
