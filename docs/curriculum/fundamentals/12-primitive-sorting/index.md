---
icon: lucide/arrow-down-up
---

# Primitive Sorting

Sorting puts values in a useful order. Java has built-in sorting, but writing a basic sort helps students understand comparisons and indexes.

## Built-In Sorting

```java
import java.util.Arrays;

int[] scores = {7, 2, 9, 4};
Arrays.sort(scores);
```

After sorting, `scores` contains `{2, 4, 7, 9}`.

## Selection Sort

Selection sort repeatedly finds the smallest remaining value and moves it to the front.

```java
public static void selectionSort(int[] values) {
    for (int start = 0; start < values.length - 1; start++) {
        int smallest = start;

        for (int i = start + 1; i < values.length; i++) {
            if (values[i] < values[smallest]) {
                smallest = i;
            }
        }

        int temp = values[start];
        values[start] = values[smallest];
        values[smallest] = temp;
    }
}
```

## Why This Matters

Sorting is common when ranking scores, filtering sensor samples, or preparing data for binary search. The exact algorithm matters more when lists get large.

## Practice

Write a method that returns the smallest value in an `int[]`. Then modify selection sort to sort from largest to smallest.
