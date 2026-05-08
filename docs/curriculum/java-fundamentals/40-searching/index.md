# Searching Algorithms

Searching is one of the most common operations in programming — finding whether a value exists, and if so, where. Two fundamental algorithms cover most use cases: linear search and binary search.

---

## Linear Search

**Linear search** scans every element from the beginning until it finds the target or reaches the end.

```java
public static int linearSearch(int[] arr, int target) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == target) return i;   // found — return index
    }
    return -1;                             // not found
}
```

```java
int[] data = {5, 3, 8, 1, 9, 2, 7};
System.out.println(linearSearch(data, 9));    // 4
System.out.println(linearSearch(data, 6));    // -1
```

### Characteristics

| | Linear Search |
|--|--------------|
| **Precondition** | None — works on unsorted arrays |
| **Best case** | O(1) — target is the first element |
| **Average case** | O(n) — target is in the middle |
| **Worst case** | O(n) — target is last or not present |
| **Space** | O(1) |

Linear search is simple and works on any array. For small arrays or one-time searches, it is often the right choice. For large arrays searched repeatedly, binary search is far faster — but requires sorted data.

---

## Binary Search

**Binary search** works by repeatedly halving the search space. Compare the target to the middle element:

- If equal → found.
- If the target is smaller → search the left half.
- If the target is larger → search the right half.

Each comparison eliminates half the remaining elements.

!!! warning "Sorted data required"
    Binary search only works correctly on a **sorted** array. Applying it to an unsorted array produces incorrect results.

### Iterative Implementation

```java
public static int binarySearch(int[] arr, int target) {
    int low = 0;
    int high = arr.length - 1;

    while (low <= high) {
        int mid = low + (high - low) / 2;   // avoids integer overflow vs (low+high)/2

        if (arr[mid] == target) return mid;
        else if (target < arr[mid]) high = mid - 1;
        else                        low  = mid + 1;
    }
    return -1;
}
```

```java
int[] sorted = {1, 3, 5, 8, 12, 17, 23, 38, 56, 72};
System.out.println(binarySearch(sorted, 17));   // 5
System.out.println(binarySearch(sorted, 10));   // -1
```

### Recursive Implementation

```java
public static int binarySearchRecursive(int[] arr, int target, int low, int high) {
    if (low > high) return -1;

    int mid = low + (high - low) / 2;
    if (arr[mid] == target) return mid;
    if (target < arr[mid])  return binarySearchRecursive(arr, target, low, mid - 1);
    else                    return binarySearchRecursive(arr, target, mid + 1, high);
}

// Call:
int index = binarySearchRecursive(sorted, 17, 0, sorted.length - 1);
```

### Tracing Binary Search on `{1,3,5,8,12,17,23,38,56,72}`, target = `17`

```
low=0, high=9  → mid=4 → arr[4]=12 < 17 → search right half
low=5, high=9  → mid=7 → arr[7]=38 > 17 → search left half
low=5, high=6  → mid=5 → arr[5]=17 == 17 → found at index 5
```

Three comparisons to find an element in a 10-element array. A linear search would need up to 10 comparisons in the worst case.

### Characteristics

| | Binary Search |
|--|--------------|
| **Precondition** | Array must be sorted |
| **Best case** | O(1) — target is the middle element |
| **Average case** | O(log n) |
| **Worst case** | O(log n) |
| **Space** | O(1) iterative / O(log n) recursive (call stack) |

---

## Why `low + (high - low) / 2` Instead of `(low + high) / 2`

`(low + high)` can overflow if both values are large `int`s (close to `Integer.MAX_VALUE`). `low + (high - low) / 2` computes the same midpoint without the overflow risk. This is a classic defensive pattern.

---

## Built-In Binary Search

Java's standard library provides `Arrays.binarySearch()`:

```java
import java.util.Arrays;

int[] sorted = {1, 3, 5, 8, 12, 17, 23, 38};
int index = Arrays.binarySearch(sorted, 17);    // 5
int missing = Arrays.binarySearch(sorted, 10);  // negative value — not found
```

`Arrays.binarySearch` returns a **negative value** when the target is not found (specifically, `-(insertion point) - 1`). Always check for a non-negative result before using the return value as an index.

`Arrays.binarySearch` requires the array to already be sorted. Sort first with `Arrays.sort(arr)`.

---

## Searching for Objects

For `String` arrays or other object arrays, binary search uses `compareTo()` internally. The array must be sorted in natural order:

```java
String[] names = {"Alice", "Bob", "Charlie", "Diana", "Eve"};
// Already sorted — if not, call Arrays.sort(names) first
int index = Arrays.binarySearch(names, "Charlie");   // 2
```

---

## Linear vs Binary Search

| | Linear Search | Binary Search |
|--|--------------|--------------|
| Sorted required? | No | Yes |
| Worst-case comparisons (n=1,000) | 1,000 | 10 |
| Worst-case comparisons (n=1,000,000) | 1,000,000 | 20 |
| Implementation complexity | Trivial | Moderate |
| When to use | Small arrays, unsorted data, one-time searches | Large sorted arrays, repeated searches |

---

## Exercise

1. Implement `linearSearch` for a `String[]` that returns the index of the first matching string, or `-1` if not found (use `.equals()`, not `==`).
2. Implement the iterative `binarySearch` and test it on an array of 20 sorted integers. Instrument it to print the `low`, `mid`, and `high` values on each iteration so you can trace the search.
3. How many comparisons does binary search need in the worst case for an array of 1,024 elements? Of 1,048,576 elements? Show your reasoning using logarithms.
4. Write a method `sortedInsertionIndex(int[] sortedArr, int target)` that uses binary search to find the correct index where `target` should be inserted to keep the array sorted (even if `target` is not present). This is what `Arrays.binarySearch` returns (negated minus one) when the value is missing.
