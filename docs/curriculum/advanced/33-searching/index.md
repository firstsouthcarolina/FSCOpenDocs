---
icon: lucide/search
---

# Searching

Searching finds a target value in a collection.

## Linear Search

Linear search checks each value from start to end.

```java
public static int linearSearch(int[] values, int target) {
    for (int i = 0; i < values.length; i++) {
        if (values[i] == target) {
            return i;
        }
    }

    return -1;
}
```

Linear search works on unsorted data and runs in `O(n)`.

## Binary Search

Binary search repeatedly cuts a sorted search range in half.

```java
public static int binarySearch(int[] values, int target) {
    int low = 0;
    int high = values.length - 1;

    while (low <= high) {
        int mid = low + (high - low) / 2;

        if (values[mid] == target) {
            return mid;
        } else if (values[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }

    return -1;
}
```

Binary search requires sorted data and runs in `O(log n)`.

## Practice

Sort an array of team numbers, then use binary search to find whether a specific team attended an event.
