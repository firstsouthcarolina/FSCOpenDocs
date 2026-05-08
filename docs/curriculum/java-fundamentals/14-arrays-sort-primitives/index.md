# `Arrays.sort` for Primitives

You have now seen five sorting algorithms — three quadratic ([Module 12](../12-quadratic-sorts/index.md)) and two divide-and-conquer ([Module 13](../13-divide-and-conquer-sorts/index.md)). In real Java code you almost never implement any of them yourself. The standard library provides `java.util.Arrays.sort()`, which picks a fast algorithm internally and handles every edge case for you. This module covers what it actually uses for primitive arrays — **dual-pivot quicksort** — and how to call it.

Sorting *object* arrays with `Comparable` and `Comparator` is covered in [Module 27](../27-object-sorting/index.md), which uses a different algorithm (Timsort).

---

## Algorithm Comparison — All Five Plus the Real Thing

| Algorithm | Time (best) | Time (avg) | Time (worst) | Space | Stable | In-place |
|-----------|------------|-----------|-------------|-------|--------|---------|
| Bubble sort | O(n) | O(n²) | O(n²) | O(1) | Yes | Yes |
| Selection sort | O(n²) | O(n²) | O(n²) | O(1) | No | Yes |
| Insertion sort | **O(n)** | O(n²) | O(n²) | O(1) | Yes | Yes |
| Merge sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | No |
| Quicksort | O(n log n) | O(n log n) | O(n²) | O(log n) | No | Yes |
| **Dual-pivot quicksort** *(used by `Arrays.sort` for primitives)* | O(n log n) | O(n log n) | O(n²) pathological | O(log n) | No | Yes |

---

## When to Use Each — A Decision Guide

You will rarely make this choice yourself for primitives, but understanding the tradeoffs is useful when you need to pick an algorithm for a non-standard situation:

- **Bubble sort** — Never in production. Useful for teaching comparisons and swaps.
- **Selection sort** — When minimizing swaps matters more than time (e.g., writing to flash memory).
- **Insertion sort** — Small arrays (< ~20 elements) or nearly-sorted data. Often combined inside merge/quick sort for small sub-arrays.
- **Merge sort** — When stability is required, or when O(n²) worst case is unacceptable.
- **Quicksort** — General-purpose default. Fastest in practice with good pivot selection.

For most real Java code, just call `Arrays.sort()` — it chooses the right algorithm internally based on the array type and size.

---

## Dual-Pivot Quicksort — What `Arrays.sort` Uses

For `int[]`, `long[]`, `double[]`, `char[]`, and the other primitive array types, `Arrays.sort` uses **dual-pivot quicksort** — an algorithm developed by Vladimir Yaroslavskiy and introduced in Java 7.

Standard single-pivot quicksort (from [Module 13](../13-divide-and-conquer-sorts/index.md)) divides the array into two partitions around one pivot. Dual-pivot quicksort uses **two pivots** simultaneously, creating three partitions.

### How It Works

Given array `arr` with bounds `[left, right]`:

**Step 1 — Choose two pivots.**
Select elements near the ⅓ and ⅔ positions of the range. Call them `pivot1` and `pivot2`. If `pivot1 > pivot2`, swap them so `pivot1 ≤ pivot2`.

**Step 2 — Partition into three regions.**
Walk a pointer `k` from `left+1` to `right-1`:
- If `arr[k] < pivot1` → swap into the **left** region (less than pivot1)
- If `arr[k] > pivot2` → swap into the **right** region (greater than pivot2)
- Otherwise → leave in the **middle** region (between pivot1 and pivot2)

After partitioning, place `pivot1` at the boundary of the left region and `pivot2` at the boundary of the right region.

**Step 3 — Recurse on all three partitions.**
```
[ elements < pivot1 | pivot1 | pivot1 ≤ elements ≤ pivot2 | pivot2 | elements > pivot2 ]
         ↑ recurse ↑                    ↑ recurse ↑                       ↑ recurse ↑
```

**Step 4 — Fall back to insertion sort for small sub-arrays.**
When a partition shrinks below ~47 elements, dual-pivot quicksort stops recursing and finishes that partition with insertion sort. This is why insertion sort still matters in production — its small constant factor wins on tiny inputs.

### Why Dual-Pivot Is Faster Than Single-Pivot

Single-pivot quicksort produces approximately `n log₂ n` comparisons on average. Dual-pivot produces approximately `n log₃ n` — fewer comparisons because the search tree is ternary rather than binary. It also has better **cache locality**: three partitions fit better into CPU cache lines, and modern hardware rewards that pattern with substantially faster real-world execution.

### A Worked Example

```java
int[] arr = {5, 1, 9, 3, 7, 4, 8, 2, 6};
// Pivots chosen near positions arr.length/3 and arr.length*2/3
// e.g. pivot1=3, pivot2=7

// After one partition pass:
// Left  (< 3):      [1, 2]
// Middle (3–7):     [3, 4, 5, 6, 7]
// Right  (> 7):     [8, 9]
// Recurse on each region...
```

| | Dual-Pivot Quicksort |
|--|----------------------|
| Time | O(n log n) average, O(n²) pathological worst case |
| Space | O(log n) — call stack |
| Stable | **No** |
| In-place | Yes |

Primitives do not need stability — two `int` values of `5` are indistinguishable, so their relative order is meaningless. This is also why `Arrays.sort` uses a different algorithm (the stable Timsort) when sorting *objects*.

---

## Using `Arrays.sort` on Primitives

```java
import java.util.Arrays;

int[] scores = {88, 72, 95, 61, 84};
Arrays.sort(scores);
System.out.println(Arrays.toString(scores));   // [61, 72, 84, 88, 95]

// Range sort — sorts only indices 1 through 3 (index 4 is excluded)
int[] data = {9, 5, 3, 7, 1};
Arrays.sort(data, 1, 4);
System.out.println(Arrays.toString(data));     // [9, 3, 5, 7, 1]
```

### All Primitive Overloads

```java
// Full-array sorts
Arrays.sort(int[] a)
Arrays.sort(long[] a)
Arrays.sort(double[] a)
Arrays.sort(float[] a)
Arrays.sort(char[] a)
Arrays.sort(byte[] a)
Arrays.sort(short[] a)

// Range sorts — [fromIndex, toIndex), toIndex is exclusive
Arrays.sort(int[] a, int fromIndex, int toIndex)
// ...same range pattern for the other primitive types
```

The range overloads are useful when you only want to sort a slice of the array without touching the rest:

```java
int[] data = {9, 5, 3, 7, 1, 0, 8};
//             ^- keep -^             unchanged
//                       ^^ sort ^^   sorted
Arrays.sort(data, 2, 5);
System.out.println(Arrays.toString(data));   // [9, 5, 1, 3, 7, 0, 8]
```

### Sorting Descending

`Arrays.sort` for primitives only sorts ascending. To sort an `int[]` in descending order, sort ascending then reverse — or use streams:

```java
int[] arr = {3, 1, 4, 1, 5, 9, 2, 6};
Arrays.sort(arr);                             // ascending
// reverse in-place
for (int i = 0, j = arr.length - 1; i < j; i++, j--) {
    int tmp = arr[i]; arr[i] = arr[j]; arr[j] = tmp;
}
System.out.println(Arrays.toString(arr));     // [9, 6, 5, 4, 3, 2, 1, 1]
```

For object arrays you can pass `Comparator.reverseOrder()` directly — see [Module 27](../27-object-sorting/index.md).

### `Arrays.parallelSort`

Java 8 added `Arrays.parallelSort` for both primitives and objects. It uses a parallel merge sort variant for large arrays and falls back to `Arrays.sort` for small ones.

```java
int[] big = new int[10_000_000];
// ... fill it ...
Arrays.parallelSort(big);   // uses multiple cores when available
```

Use `parallelSort` for arrays larger than ~10,000 elements when multiple CPU cores are available. For most use cases in this curriculum, plain `Arrays.sort` is the right default.

!!! note "JavaDoc"
    Full API reference: [`java.util.Arrays`](https://docs.oracle.com/en/java/docs/api/java.base/java/util/Arrays.html)

---

## Exercise

1. Call `Arrays.sort` on an `int[]` of your choice and use `Arrays.toString` to print the result. Then call it again with `fromIndex` and `toIndex` arguments to sort only the middle portion. Confirm the endpoints are unchanged.

2. Generate a random `int[]` of 1,000,000 elements and time `Arrays.sort` on it (use `System.nanoTime()` before and after). Then time `Arrays.parallelSort` on a fresh copy of the same array. Compare. On a multi-core machine, `parallelSort` should be noticeably faster.

3. Sort a `double[]` containing positive and negative values, including a few `Double.NaN` and `-0.0`. Print the result and observe how `Arrays.sort` orders these special values. (Hint: read the Javadoc for the contract.)

4. Implement an `int[]` descending sort by sorting ascending and then reversing in place. Wrap it in a method `sortDescending(int[] arr)`. Test it on `{3, 1, 4, 1, 5, 9, 2, 6}`.

5. Build a small benchmark: create three arrays of 100,000 random integers, then sort each one with (a) your insertion sort from Module 12, (b) your quicksort from Module 13, and (c) `Arrays.sort`. Print the time for each. The relative speeds will tell you why production code never hand-rolls these algorithms.
