# Quadratic Sorting Algorithms

Sorting puts a collection into a defined order. The simplest sorts are **comparison-based** algorithms that walk the array and swap adjacent or selected elements. The three classic O(n²) algorithms — bubble, selection, and insertion sort — are all variations on this idea. They are slow on large data but easy to understand and form the foundation for everything that follows.

The faster O(n log n) divide-and-conquer algorithms (merge sort, quicksort) are covered in [Module 13](../13-divide-and-conquer-sorts/index.md), and `Arrays.sort` itself is covered in [Module 14](../14-arrays-sort-primitives/index.md). Sorting *objects* (with `Comparable` and `Comparator`) is in [Module 27](../27-object-sorting/index.md), after you have learned how to write your own classes.

---

## What Makes a Good Sorting Algorithm?

Before we dive in, here are the properties you'll see compared throughout this module and the next:

| Property | Meaning |
|----------|---------|
| **Time complexity** | How many operations does it take? |
| **Space complexity** | How much extra memory does it need? |
| **Stable** | Equal elements keep their original relative order |
| **In-place** | Sorts without allocating significant extra memory |
| **Adaptive** | Runs faster on already-sorted or nearly-sorted input |

For this module, all three algorithms run in O(n²) time and use O(1) extra memory. They differ in stability, adaptiveness, and how many swaps they make.

---

## Bubble Sort

Compare adjacent pairs and swap them if they are in the wrong order. One full pass "bubbles" the largest unsorted element to its final position. Repeat until no swaps occur.

```java
public static void bubbleSort(int[] arr) {
    for (int i = 0; i < arr.length - 1; i++) {
        for (int j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j];
                arr[j]   = arr[j + 1];
                arr[j + 1] = temp;
            }
        }
    }
}
```

**Trace on `{5, 3, 8, 1}`:**
```
Pass 0: (5,3)→swap, (5,8)→ok, (8,1)→swap → {3, 5, 1, 8}
Pass 1: (3,5)→ok,  (5,1)→swap            → {3, 1, 5, 8}
Pass 2: (3,1)→swap                        → {1, 3, 5, 8}
```

| | Bubble Sort |
|--|------------|
| Time | O(n²) worst/average; O(n) best (already sorted, with early-exit flag) |
| Space | O(1) — in-place |
| Stable | Yes |
| Adaptive | Yes — add a `swapped` flag to exit early on sorted input |
| Weakness | Very slow in practice; use only as a teaching baseline |

### The Early-Exit Optimization

Bubble sort can detect a fully sorted array partway through: if a full pass made no swaps, the array is already sorted.

```java
public static void bubbleSort(int[] arr) {
    for (int i = 0; i < arr.length - 1; i++) {
        boolean swapped = false;
        for (int j = 0; j < arr.length - 1 - i; j++) {
            if (arr[j] > arr[j + 1]) {
                int temp = arr[j]; arr[j] = arr[j + 1]; arr[j + 1] = temp;
                swapped = true;
            }
        }
        if (!swapped) break;   // already sorted — stop
    }
}
```

Without the flag, bubble sort always runs n−1 passes. With it, an already-sorted array completes in a single pass: O(n) best case.

---

## Selection Sort

Find the minimum element in the unsorted portion and swap it to the front. Repeat.

```java
public static void selectionSort(int[] arr) {
    for (int i = 0; i < arr.length - 1; i++) {
        int minIndex = i;
        for (int j = i + 1; j < arr.length; j++) {
            if (arr[j] < arr[minIndex]) {
                minIndex = j;
            }
        }
        // swap arr[i] with arr[minIndex]
        int temp = arr[i];
        arr[i] = arr[minIndex];
        arr[minIndex] = temp;
    }
}
```

**Trace on `{5, 3, 8, 1, 9}`:**
```
Pass 0: min=1 at index 3 → swap with index 0 → {1, 3, 8, 5, 9}
Pass 1: min=3 at index 1 → already in place  → {1, 3, 8, 5, 9}
Pass 2: min=5 at index 3 → swap with index 2 → {1, 3, 5, 8, 9}
Pass 3: min=8 at index 3 → already in place  → {1, 3, 5, 8, 9}
```

| | Selection Sort |
|--|----------------|
| Time | O(n²) always |
| Space | O(1) — in-place |
| Stable | No |
| Adaptive | No |
| Strength | Very few swaps (exactly n−1) |

Selection sort makes fewer swaps than bubble sort, which matters when swapping is expensive — for example, writing to flash memory where each write costs significant time and wear. Read-heavy with cheap comparison and expensive write? Selection sort is the right choice.

It is **not stable**: when you swap an element from the middle into the sorted prefix, you can leapfrog over equal elements and disturb their original order.

---

## Insertion Sort

Build the sorted portion one element at a time. Take the next element and shift it left until it is in the correct position.

```java
public static void insertionSort(int[] arr) {
    for (int i = 1; i < arr.length; i++) {
        int key = arr[i];
        int j = i - 1;
        while (j >= 0 && arr[j] > key) {
            arr[j + 1] = arr[j];   // shift right
            j--;
        }
        arr[j + 1] = key;           // insert key in its correct spot
    }
}
```

**Trace on `{5, 3, 8, 1, 9}`:**
```
i=1: key=3  → shift 5 right → {3, 5, 8, 1, 9}
i=2: key=8  → 5 < 8, stop   → {3, 5, 8, 1, 9}
i=3: key=1  → shift 8,5,3 right → {1, 3, 5, 8, 9}
i=4: key=9  → 8 < 9, stop   → {1, 3, 5, 8, 9}
```

| | Insertion Sort |
|--|----------------|
| Time | O(n²) worst, **O(n) best** (already sorted) |
| Space | O(1) — in-place |
| Stable | Yes |
| Adaptive | Yes — excellent on nearly-sorted input |
| Strength | Best O(n²) algorithm for small or nearly-sorted arrays |

Insertion sort is the algorithm of choice for small arrays. Java's `Arrays.sort` actually uses insertion sort internally for sub-arrays smaller than ~47 elements — the constant factors beat the more complex algorithms at small sizes.

---

## Comparing the Three

| Algorithm | Time (best) | Time (avg/worst) | Stable | Adaptive | Best at |
|-----------|------------|------------------|--------|----------|---------|
| Bubble | O(n) | O(n²) | Yes | With flag | Teaching only |
| Selection | O(n²) | O(n²) | No | No | Minimizing swaps |
| Insertion | **O(n)** | O(n²) | Yes | Yes | Small / nearly-sorted arrays |

For real production code, none of these three is the right default — they are all O(n²). But each one has a niche:

- **Insertion sort** appears inside hybrid algorithms (Timsort, dual-pivot quicksort) for small sub-arrays.
- **Selection sort** wins when writes are far more expensive than comparisons.
- **Bubble sort** is only useful pedagogically.

The next module (divide-and-conquer) shows the O(n log n) algorithms that handle large data efficiently.

---

## Exercise

1. Implement `selectionSort` and trace it by hand on `{9, 4, 7, 2, 6}`. Write out the array state after each pass.

2. Implement `insertionSort` and test it on a nearly-sorted array (e.g., `{1, 2, 4, 3, 5}`). Count the number of shifts it makes. Then run it on a fully reversed array `{5, 4, 3, 2, 1}` and count again. Explain why one is dramatically larger.

3. Implement `bubbleSort` with the early-exit `swapped` flag. Time it (or count comparisons) on (a) a random array, (b) an already-sorted array, and (c) a reversed array. The early exit should make case (b) close to O(n).

4. Modify `selectionSort` to find the *maximum* in the unsorted portion and swap it to the *end* on each pass. Test that it produces the same result. Why does this version still run in O(n²) time?

5. Insertion sort is stable but selection sort is not. Construct a small array of `int` pairs (or `String` keys with a numeric tiebreaker) where stability would matter, sort it both ways, and observe the difference. (Stability for primitives is meaningless because equal `int` values are indistinguishable — this exercise illustrates *why* it matters for objects, which you'll revisit in [Module 27](../27-object-sorting/index.md).)
