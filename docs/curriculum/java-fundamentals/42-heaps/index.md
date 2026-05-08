# Heaps and Priority Queues

A **heap** is a tree-based data structure that always keeps the smallest (or largest) element at the top, making it very efficient for repeatedly fetching the highest-priority item.

---

## What Is a Heap?

A **min-heap** satisfies two properties:

1. **Shape property** — the tree is a **complete binary tree**: all levels are fully filled except possibly the last, which is filled left to right.
2. **Heap property** — every parent is **smaller than or equal to** its children.

```
         1          ← minimum is always at the root
        / \
       3   2
      / \ / \
     7  4 5  6
```

A **max-heap** is the same but every parent is **greater than or equal to** its children (maximum at root).

---

## Storing a Heap as an Array

The complete binary tree structure maps perfectly onto an array — no pointers needed. Given an element at index `i`:

| Relationship | Index formula |
|-------------|--------------|
| Left child  | `2 * i + 1` |
| Right child | `2 * i + 2` |
| Parent      | `(i - 1) / 2` |

The heap `[1, 3, 2, 7, 4, 5, 6]` corresponds to the tree above:

```
Index: 0  1  2  3  4  5  6
Value: 1  3  2  7  4  5  6
```

- Element at index 1 (value 3): left child = index 3 (7), right child = index 4 (4), parent = index 0 (1).

---

## Insert — Heapify Up

Add the new element at the end of the array (maintaining the shape property), then **bubble it up** by swapping it with its parent until the heap property is restored.

```java
public class MinHeap {
    private int[] data;
    private int size;

    public MinHeap(int capacity) {
        data = new int[capacity];
        size = 0;
    }

    public int size() { return size; }
    public boolean isEmpty() { return size == 0; }
    public int peek() {
        if (isEmpty()) throw new java.util.NoSuchElementException();
        return data[0];
    }

    public void insert(int value) {
        if (size == data.length) throw new RuntimeException("Heap is full");
        data[size] = value;
        heapifyUp(size);
        size++;
    }

    private void heapifyUp(int index) {
        while (index > 0) {
            int parent = (index - 1) / 2;
            if (data[parent] > data[index]) {
                swap(parent, index);
                index = parent;
            } else {
                break;
            }
        }
    }

    private void swap(int i, int j) {
        int temp = data[i]; data[i] = data[j]; data[j] = temp;
    }
}
```

**Inserting 2 into `[1, 3, 5, 7, 4, 6]`:**
```
Add at end:    [1, 3, 5, 7, 4, 6, 2]    index=6
Parent of 6 = index 2, value 5. 2 < 5 → swap
               [1, 3, 2, 7, 4, 6, 5]    index=2
Parent of 2 = index 0, value 1. 2 > 1 → stop
```

---

## Remove Min — Heapify Down

The minimum is always at index 0. To remove it: replace the root with the last element, shrink the size, then **push it down** by swapping it with its smaller child until the heap property is restored.

```java
public int removeMin() {
    if (isEmpty()) throw new java.util.NoSuchElementException();
    int min = data[0];
    data[0] = data[--size];   // move last element to root and shrink
    heapifyDown(0);
    return min;
}

private void heapifyDown(int index) {
    while (true) {
        int left  = 2 * index + 1;
        int right = 2 * index + 2;
        int smallest = index;

        if (left  < size && data[left]  < data[smallest]) smallest = left;
        if (right < size && data[right] < data[smallest]) smallest = right;

        if (smallest == index) break;   // heap property restored
        swap(index, smallest);
        index = smallest;
    }
}
```

---

## Complexity

| Operation | Time |
|-----------|------|
| `insert` | O(log n) |
| `removeMin` | O(log n) |
| `peek` | O(1) |
| Build heap from n elements | O(n) |

Because the heap is a complete binary tree, its height is always `⌊log₂ n⌋`. Heapify up and down travel at most that many levels.

---

## `java.util.PriorityQueue`

Java's standard library provides a min-heap implementation. Use it instead of building your own in production code.

```java
import java.util.PriorityQueue;

PriorityQueue<Integer> pq = new PriorityQueue<>();

pq.offer(5);
pq.offer(1);
pq.offer(3);
pq.offer(2);

System.out.println(pq.peek());    // 1 — smallest, no removal
System.out.println(pq.poll());    // 1 — removes min
System.out.println(pq.poll());    // 2
System.out.println(pq.poll());    // 3
```

Elements always come out in ascending order regardless of insertion order.

### Max-Heap with PriorityQueue

Use `Collections.reverseOrder()` to reverse the comparator:

```java
import java.util.Collections;
import java.util.PriorityQueue;

PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
maxHeap.offer(5);
maxHeap.offer(1);
maxHeap.offer(3);

System.out.println(maxHeap.poll());   // 5
System.out.println(maxHeap.poll());   // 3
System.out.println(maxHeap.poll());   // 1
```

### PriorityQueue with Custom Objects

Use a lambda or `Comparator` to define priority:

```java
// Priority queue of tasks ordered by priority number (lower = higher priority)
PriorityQueue<String[]> taskQueue = new PriorityQueue<>(
    (a, b) -> Integer.parseInt(a[1]) - Integer.parseInt(b[1])
);

taskQueue.offer(new String[]{"Drive forward", "2"});
taskQueue.offer(new String[]{"Emergency stop", "0"});
taskQueue.offer(new String[]{"Rotate arm", "1"});

while (!taskQueue.isEmpty()) {
    String[] task = taskQueue.poll();
    System.out.println("Priority " + task[1] + ": " + task[0]);
}
// Priority 0: Emergency stop
// Priority 1: Rotate arm
// Priority 2: Drive forward
```

---

## Heap Sort

A heap can sort an array in O(n log n) time with O(1) extra space by building a max-heap and repeatedly removing the maximum.

```java
public static void heapSort(int[] arr) {
    int n = arr.length;

    // Build max-heap (heapify from last non-leaf to root)
    for (int i = n / 2 - 1; i >= 0; i--) {
        heapifyDown(arr, n, i);
    }

    // Extract elements from heap one by one
    for (int i = n - 1; i > 0; i--) {
        // Move current root (max) to end
        int temp = arr[0]; arr[0] = arr[i]; arr[i] = temp;
        // Heapify the reduced heap
        heapifyDown(arr, i, 0);
    }
}

private static void heapifyDown(int[] arr, int heapSize, int index) {
    int largest = index;
    int left  = 2 * index + 1;
    int right = 2 * index + 2;

    if (left  < heapSize && arr[left]  > arr[largest]) largest = left;
    if (right < heapSize && arr[right] > arr[largest]) largest = right;

    if (largest != index) {
        int temp = arr[index]; arr[index] = arr[largest]; arr[largest] = temp;
        heapifyDown(arr, heapSize, largest);
    }
}
```

---

## Use Cases

**Priority scheduling** — tasks with higher priority execute first. Robot command systems benefit directly: an emergency stop command can be enqueued with the highest priority and always executes before queued drive commands.

**Dijkstra's shortest path** — the graph traversal algorithm in Module 44 uses a priority queue to always process the nearest unvisited node first.

**Event simulation** — events are processed in time order by storing them in a min-heap keyed by timestamp.

---

## Exercise

1. Implement the `MinHeap` class fully (insert and removeMin) and test it by inserting `{10, 4, 15, 1, 7, 2, 8}` then extracting all elements — they should come out in ascending order.
2. Use `java.util.PriorityQueue` to find the **k smallest** elements in an array of 20 random integers. (Hint: add all elements to the priority queue, then poll `k` times.)
3. Use a `PriorityQueue` with a custom comparator to simulate a hospital triage queue: patients have a name and a severity level (1 = critical, 5 = minor). Always treat the most critical patient first. Test with at least five patients added in non-priority order.
