# Divide-and-Conquer Sorts

The O(n²) algorithms in [Module 12](../12-quadratic-sorts/index.md) work fine on a few dozen elements but become painfully slow on thousands. The fix is a fundamentally different strategy: **divide and conquer**. Split the array in half, sort each half, then combine the results. This brings the running time down to **O(n log n)** — fast enough that sorting a million elements takes a fraction of a second.

This module covers the two classic divide-and-conquer sorts: **merge sort** and **quicksort**. Both are recursive — they call themselves on smaller pieces of the array.

!!! note "Recursion preview"
    Both algorithms call themselves. This pattern is called *recursion* and is covered in depth in [Module 35](../35-recursion/index.md). For this module, focus on the divide-and-conquer idea: trust that the recursive call returns a sorted sub-array, and worry only about how the current call combines its results.

---

## The Divide-and-Conquer Idea

```
Original problem:        Sort [5, 3, 8, 1, 9, 2, 7, 4]

Divide:                  [5, 3, 8, 1] and [9, 2, 7, 4]
Solve sub-problems:      [1, 3, 5, 8] and [2, 4, 7, 9]
Combine:                 [1, 2, 3, 4, 5, 7, 8, 9]
```

The two sorts in this module differ only in **how they divide and how they combine**:

- **Merge sort** divides cheaply (just split in half) and combines expensively (walk both halves merging in order).
- **Quicksort** divides expensively (partition around a pivot) and combines for free (the partition step already places the pivot in the right spot).

---

## Merge Sort

Split the array in half recursively until each piece has one element (trivially sorted), then **merge** the sorted halves back together.

```java
public static void mergeSort(int[] arr, int left, int right) {
    if (left >= right) return;   // base case: 0 or 1 element

    int mid = (left + right) / 2;
    mergeSort(arr, left, mid);          // sort left half
    mergeSort(arr, mid + 1, right);     // sort right half
    merge(arr, left, mid, right);       // merge them
}

private static void merge(int[] arr, int left, int mid, int right) {
    int n1 = mid - left + 1;
    int n2 = right - mid;

    int[] L = new int[n1];
    int[] R = new int[n2];

    for (int i = 0; i < n1; i++) L[i] = arr[left + i];
    for (int j = 0; j < n2; j++) R[j] = arr[mid + 1 + j];

    int i = 0, j = 0, k = left;
    while (i < n1 && j < n2) {
        if (L[i] <= R[j]) arr[k++] = L[i++];
        else               arr[k++] = R[j++];
    }
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}

// Call:
mergeSort(arr, 0, arr.length - 1);
```

### How the Merge Step Works

The merge takes two **already-sorted** halves and produces a single sorted whole. It uses two pointers, one walking each half, always taking the smaller of the two front elements:

```
L = [1, 3, 5, 8]    pointer i
R = [2, 4, 7, 9]    pointer j
                                output
i=0, j=0: 1 < 2 → take 1        [1]
i=1, j=0: 3 > 2 → take 2        [1, 2]
i=1, j=1: 3 < 4 → take 3        [1, 2, 3]
i=2, j=1: 5 > 4 → take 4        [1, 2, 3, 4]
i=2, j=2: 5 < 7 → take 5        [1, 2, 3, 4, 5]
i=3, j=2: 8 > 7 → take 7        [1, 2, 3, 4, 5, 7]
i=3, j=3: 8 < 9 → take 8        [1, 2, 3, 4, 5, 7, 8]
i=4 (done), copy rest of R      [1, 2, 3, 4, 5, 7, 8, 9]
```

The merge is **stable** because of the `<=` comparison: when two elements are equal, the one from the left half is taken first, preserving their original order.

### Visualizing the Recursion

```
                    [5, 3, 8, 1, 9, 2, 7, 4]
                            ↓ split
            [5, 3, 8, 1]              [9, 2, 7, 4]
                ↓ split                    ↓ split
        [5, 3]   [8, 1]            [9, 2]   [7, 4]
          ↓        ↓                 ↓        ↓
       [3, 5]   [1, 8]            [2, 9]   [4, 7]
            ↓ merge                    ↓ merge
        [1, 3, 5, 8]              [2, 4, 7, 9]
                          ↓ merge
                [1, 2, 3, 4, 5, 7, 8, 9]
```

| | Merge Sort |
|--|------------|
| Time | O(n log n) always |
| Space | O(n) — needs auxiliary arrays during merge |
| Stable | Yes |
| Adaptive | No |
| Strength | Guaranteed O(n log n) in all cases; stable |

Merge sort is the preferred algorithm when stability is required or when the worst case matters (the input might be adversarial). Java's `Arrays.sort` for objects uses a more sophisticated relative of merge sort called Timsort — covered in [Module 27](../27-object-sorting/index.md).

---

## Quicksort

Choose a **pivot** element, partition the array so everything less than the pivot is to its left and everything greater is to its right, then recursively sort the two partitions.

```java
public static void quickSort(int[] arr, int low, int high) {
    if (low < high) {
        int pivotIndex = partition(arr, low, high);
        quickSort(arr, low, pivotIndex - 1);
        quickSort(arr, pivotIndex + 1, high);
    }
}

private static int partition(int[] arr, int low, int high) {
    int pivot = arr[high];   // choose last element as pivot
    int i = low - 1;

    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            int temp = arr[i]; arr[i] = arr[j]; arr[j] = temp;
        }
    }
    // place pivot in its correct position
    int temp = arr[i + 1]; arr[i + 1] = arr[high]; arr[high] = temp;
    return i + 1;
}

// Call:
quickSort(arr, 0, arr.length - 1);
```

### How the Partition Step Works

The partition rearranges the array around the pivot so all smaller elements come first, then the pivot, then all larger elements. The pivot ends up in its final sorted position; the two halves are not yet sorted but are guaranteed to contain the right elements.

```
arr = [3, 8, 2, 5, 1, 4, 7]   pivot = 7 (last element)
                              i starts at -1, scans j = 0..5

j=0: arr[0]=3 ≤ 7 → i=0, swap arr[0] with itself → [3, 8, 2, 5, 1, 4, 7]
j=1: arr[1]=8 > 7 → no action                     → [3, 8, 2, 5, 1, 4, 7]
j=2: arr[2]=2 ≤ 7 → i=1, swap arr[1]↔arr[2]      → [3, 2, 8, 5, 1, 4, 7]
j=3: arr[3]=5 ≤ 7 → i=2, swap arr[2]↔arr[3]      → [3, 2, 5, 8, 1, 4, 7]
j=4: arr[4]=1 ≤ 7 → i=3, swap arr[3]↔arr[4]      → [3, 2, 5, 1, 8, 4, 7]
j=5: arr[5]=4 ≤ 7 → i=4, swap arr[4]↔arr[5]      → [3, 2, 5, 1, 4, 8, 7]

Place pivot:    swap arr[i+1=5] ↔ arr[high=6]    → [3, 2, 5, 1, 4, 7, 8]

Pivot 7 is now at index 5. Recurse on [3,2,5,1,4] and [8].
```

### Why Quicksort's Worst Case Is O(n²)

If the pivot you pick is consistently the largest or smallest element, the partition is unbalanced: one side gets everything, the other gets nothing. That makes the recursion tree linear instead of logarithmic, giving O(n²) total work.

The classic worst case: an already-sorted array with the *last element* chosen as pivot. Real implementations use techniques like **median-of-three** pivot selection (pick the median of the first, middle, and last elements) or **random pivots** to make this case astronomically unlikely.

| | Quicksort |
|--|-----------|
| Time | O(n log n) average, **O(n²) worst** (sorted input with bad pivot) |
| Space | O(log n) — recursive call stack |
| Stable | No |
| Adaptive | No |
| Strength | Fast in practice; small constant factor; in-place |

In practice, with good pivot selection, quicksort is the fastest general-purpose comparison sort. Java's `Arrays.sort` for primitive arrays uses a sophisticated variant called **dual-pivot quicksort** — see [Module 14](../14-arrays-sort-primitives/index.md).

---

## Merge Sort vs. Quicksort

| | Merge Sort | Quicksort |
|--|-----------|-----------|
| Time (worst) | O(n log n) | O(n²) |
| Time (avg) | O(n log n) | O(n log n) |
| Space | O(n) | O(log n) |
| Stable | Yes | No |
| In-place | No | Yes |
| Real-world speed | Slower constant factor | Usually fastest |
| Used by `Arrays.sort` for | Objects (as Timsort) | Primitives (as dual-pivot) |

The choice between them is a classic engineering tradeoff:

- Need a guaranteed worst case? **Merge sort.**
- Need stability? **Merge sort.**
- Need to sort huge data with limited memory? **Quicksort** (in-place).
- Default for general-purpose sorting? **Quicksort** with median-of-three or random pivot.

You almost never have to make this decision yourself in real Java code — `Arrays.sort` makes it for you, intelligently. The next module shows what it actually does.

---

## Exercise

1. Implement `mergeSort`. Add a `System.out.println(Arrays.toString(arr))` call inside the `merge` method, just before returning. Run it on `{5, 3, 8, 1, 9, 2, 7, 4}` and observe how the sorted halves grow.

2. Implement `quickSort` with the last-element-as-pivot strategy. Run it on a random array and confirm it sorts correctly. Then run it on an already-sorted array `{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}` and count comparisons or observe how many recursive calls happen — this is the O(n²) worst case in action.

3. Modify your `quickSort` to use **median-of-three** pivot selection: look at `arr[low]`, `arr[(low+high)/2]`, and `arr[high]`, find the median value, swap it to `arr[high]`, then proceed with the existing partition. Re-run on the sorted-array input from exercise 2 and confirm the worst case is gone.

4. Implement a **hybrid sort**: use quicksort recursively until a sub-array has 16 or fewer elements, then switch to insertion sort for that piece. This is similar to what real production sorts do. Compare its speed against pure quicksort on a large random array.

5. Explain in your own words why merge sort needs O(n) extra space but quicksort needs only O(log n). Where exactly in each algorithm does the space cost come from?
