# Algorithm Complexity and Big O Notation

When you write two programs that solve the same problem, how do you decide which one is better? **Algorithm analysis** gives you a language to compare solutions based on how their runtime and memory usage scale as the input grows.

---

## Why Input Size Matters

Imagine sorting 10 elements vs sorting 10 million elements. A slow algorithm that handles 10 elements in a millisecond might take hours on 10 million. Understanding how a solution scales tells you whether it will work at the sizes you actually care about.

---

## Big O Notation

**Big O** describes an algorithm's growth rate in the **worst case** as a function of input size `n`. It ignores constants and lower-order terms — we care about shape, not exact numbers.

| Notation | Name | Description |
|----------|------|-------------|
| O(1) | Constant | Runtime does not depend on input size |
| O(log n) | Logarithmic | Input is halved each step |
| O(n) | Linear | Each element is visited once |
| O(n log n) | Log-linear | Each element visited, with a log-n factor |
| O(n²) | Quadratic | Each element paired with every other element |
| O(2ⁿ) | Exponential | Doubles with each added input element |

---

## O(1) — Constant Time

The operation takes the same amount of time regardless of how large the input is.

```java
// Accessing an array element
int first = arr[0];    // O(1) — direct memory lookup

// Stack push / pop
stack.push(x);         // O(1)
stack.pop();           // O(1)
```

---

## O(n) — Linear Time

The operation visits each element once. Doubling the input doubles the work.

```java
// Finding the maximum value in an unsorted array
public static int findMax(int[] arr) {
    int max = arr[0];
    for (int x : arr) {      // visits n elements → O(n)
        if (x > max) max = x;
    }
    return max;
}
```

---

## O(log n) — Logarithmic Time

Each step eliminates half the remaining possibilities. Input can double without doubling the work — it only adds one more step.

```java
// Binary search (see Module 40)
// n=1000 → ~10 steps
// n=1,000,000 → ~20 steps
// n=1,000,000,000 → ~30 steps
```

Logarithmic algorithms handle enormous inputs easily. `log₂(1,000,000,000) ≈ 30`.

---

## O(n log n) — Log-Linear Time

Common in efficient sorting algorithms. Slightly worse than linear but far better than quadratic for large `n`.

```java
// Merge sort, quicksort (average case), heapsort
// n=1,000,000 → roughly 20,000,000 operations
```

---

## O(n²) — Quadratic Time

A nested loop where both loops run `n` times. Doubling the input quadruples the work.

```java
// Bubble sort, selection sort, insertion sort (worst case)
for (int i = 0; i < n; i++) {
    for (int j = 0; j < n; j++) {
        // inner body runs n² times total
    }
}
```

O(n²) algorithms are fine for small `n` (hundreds or low thousands) but become painfully slow at larger sizes.

---

## Growth Comparison Table

| n | O(1) | O(log n) | O(n) | O(n log n) | O(n²) |
|---|------|----------|------|------------|-------|
| 10 | 1 | 3 | 10 | 33 | 100 |
| 100 | 1 | 7 | 100 | 664 | 10,000 |
| 1,000 | 1 | 10 | 1,000 | 9,966 | 1,000,000 |
| 10,000 | 1 | 13 | 10,000 | 132,877 | 100,000,000 |
| 1,000,000 | 1 | 20 | 1,000,000 | 19,931,569 | 10¹² |

The difference between O(n) and O(n²) at 10,000 elements is 10,000 vs 100,000,000 operations — a 10,000× difference.

---

## Dropping Constants and Lower-Order Terms

Big O describes the dominant term only:

```
3n + 5       → O(n)       (constant 3 and offset 5 ignored)
n² + 100n    → O(n²)      (100n is dominated by n² for large n)
2 log n + n  → O(n)       (log n is dominated by n)
```

Two different O(n) algorithms might differ by a constant factor in practice (one might be twice as fast), but Big O treats them equivalently. For most problems, the class of the algorithm matters more than the constant.

---

## Best, Average, and Worst Case

An algorithm can behave differently depending on the input:

| | Meaning | Example (linear search) |
|--|---------|------------------------|
| **Best case** | Most favorable input | Target is the first element → O(1) |
| **Average case** | Expected input | Target is somewhere in the middle → O(n/2) → O(n) |
| **Worst case** | Least favorable input | Target is last or not present → O(n) |

Big O usually refers to the **worst case** unless stated otherwise.

---

## Space Complexity

Big O also applies to **memory usage**:

```java
// O(1) space — only a few variables, regardless of input size
public static int sum(int[] arr) {
    int total = 0;
    for (int x : arr) total += x;
    return total;
}

// O(n) space — allocates a new array proportional to input
public static int[] copy(int[] arr) {
    int[] result = new int[arr.length];
    for (int i = 0; i < arr.length; i++) result[i] = arr[i];
    return result;
}
```

Sometimes you trade space for speed (memoization caches results at the cost of memory), or speed for space (in-place sorting uses O(1) extra space but may be slower).

---

## Identifying Complexity from Code

```java
// O(1) — no loop, no recursion on input
int x = arr[0] + arr[arr.length - 1];

// O(n) — single loop over input
for (int i = 0; i < n; i++) { /* constant work */ }

// O(n²) — nested loops, both proportional to n
for (int i = 0; i < n; i++)
    for (int j = 0; j < n; j++) { /* constant work */ }

// O(n²) — triangular pattern still O(n²)
for (int i = 0; i < n; i++)
    for (int j = i + 1; j < n; j++) { /* ~n²/2 operations */ }

// O(log n) — input halved each iteration
while (n > 1) { n /= 2; }
```

---

## Practical Thresholds (Rule of Thumb)

| Algorithm class | Handles how many elements comfortably? |
|-----------------|---------------------------------------|
| O(n²) | Up to ~10,000 |
| O(n log n) | Up to ~10,000,000 |
| O(n) | Up to ~100,000,000 |
| O(log n) | Effectively unlimited |

These are rough guidelines. Hardware, constant factors, and the work done inside loops all affect real performance.

---

## Exercise

1. For each code fragment, identify the Big O time complexity and explain why:
   - A loop from `0` to `n` that prints each element.
   - Two nested loops from `0` to `n`.
   - Binary search on a sorted array of length `n`.
   - A loop that doubles its counter: `for (int i = 1; i < n; i *= 2)`.
2. You have an O(n²) algorithm that runs in 1 second on 1,000 elements. Estimate how long it would take on 10,000 elements. Show your reasoning.
3. Explain in your own words why O(n log n) sorting algorithms (like merge sort) are preferred over O(n²) algorithms (like bubble sort) for large datasets. At what input size does the difference become significant?
