# Sorting Object Arrays

[Module 14](../14-arrays-sort-primitives/index.md) covered sorting `int[]` and how `Arrays.sort` uses dual-pivot quicksort for primitives. Objects are different: two objects are not equal just because they hold the same data — equality and order have to be *defined*. This module covers how Java defines order for objects, how `Arrays.sort` uses that definition, and how Timsort makes it efficient.

---

## Why Objects Need Explicit Order

With `int[]`, "less than" is built into the language: `3 < 7` is always true. With objects, what does it mean for one `Student` to be "less than" another? By GPA? By name? By ID? Java cannot guess — you have to tell it.

There are two ways to define order for objects:

| Approach | Interface | Where the order lives | Use when |
|----------|-----------|-----------------------|----------|
| **Natural order** | `Comparable<T>` | Inside the class | One "obvious" default ordering |
| **Custom order** | `Comparator<T>` | Outside the class | Multiple orderings, or ordering a class you didn't write |

---

## `Comparable<T>` — Natural Order

Implement `Comparable<T>` on the class you want to sort. You provide one method:

```java
public int compareTo(T other)
```

The contract:
- Return **negative** if `this` comes before `other`
- Return **zero** if they are equal in ordering
- Return **positive** if `this` comes after `other`

```java
public class Student implements Comparable<Student> {
    private String name;
    private double gpa;

    public Student(String name, double gpa) {
        this.name = name;
        this.gpa  = gpa;
    }

    @Override
    public int compareTo(Student other) {
        // Natural order: ascending by GPA
        return Double.compare(this.gpa, other.gpa);
    }

    @Override
    public String toString() {
        return name + "(" + gpa + ")";
    }
}
```

`Double.compare(a, b)` handles `NaN` and `-0.0` correctly — always prefer it over `(int)(a - b)`, which loses precision and gives wrong results for floating-point values.

Once `Student` implements `Comparable`, `Arrays.sort` can sort a `Student[]` without any additional arguments:

```java
Student[] roster = {
    new Student("Zara",  3.2),
    new Student("Alice", 3.8),
    new Student("Bob",   3.2),
    new Student("Chen",  3.5)
};

Arrays.sort(roster);
System.out.println(Arrays.toString(roster));
// [Zara(3.2), Bob(3.2), Chen(3.5), Alice(3.8)]
// Note: Zara stays before Bob — equal GPAs keep their original order (stable sort)
```

---

## `Comparator<T>` — Custom Order

A `Comparator<T>` is a separate object (or lambda) that defines one ordering. Use it when:
- You need multiple orderings of the same class
- You cannot modify the class (e.g., `String`, `Integer`)

```java
// Sort by name (alphabetical)
Comparator<Student> byName = (a, b) -> a.getName().compareTo(b.getName());

// Sort by GPA descending
Comparator<Student> byGpaDesc = (a, b) -> Double.compare(b.gpa, a.gpa);

// Sort by GPA, then by name as a tiebreaker
Comparator<Student> byGpaThenName = Comparator
    .comparingDouble(Student::getGpa)
    .thenComparing(Student::getName);
```

Pass the comparator as the second argument to `Arrays.sort`:

```java
Arrays.sort(roster, byName);
Arrays.sort(roster, byGpaDesc);
Arrays.sort(roster, byGpaThenName);
```

`Comparator.comparingDouble` and `.thenComparing` are covered in more detail in [Module 30](../30-lambdas/index.md) (Lambdas) and [Module 24](../24-interfaces/index.md) (Interfaces). For now, know that lambdas `(a, b) -> ...` are a concise way to write a `Comparator`.

---

## Stability

`Arrays.sort` on objects is **stable**: elements that compare as equal keep their original relative order. This is why Zara stayed before Bob in the example above even though both have GPA 3.2.

Stability matters whenever you sort by one criterion and later sort by another, or whenever equal elements have visible identity (like a name). Primitives do not need stability — two `int` values of `5` are indistinguishable.

---

## `Arrays.sort` for Objects — Timsort

For `Object[]` and `T[]` with a `Comparator`, `Arrays.sort` uses **Timsort** — a hybrid algorithm designed by Tim Peters (for Python, 2002) and adopted by Java 7. It is implemented in `java.util.TimSort`.

Timsort is built on one key observation: **real-world data is rarely random**. Arrays often contain pre-sorted sections, reverse-sorted sections, or repeated blocks. Timsort exploits this by finding and merging naturally ordered runs rather than ignoring existing order.

### How Timsort Works

**Step 1 — Compute `minRun`.**
Choose a minimum run length (32–64) based on the array size using a formula that ensures the number of runs is close to a power of two, making merging maximally efficient.

**Step 2 — Find natural runs.**
Walk the array left to right, identifying maximal runs:
- An **ascending run** is a sequence where each element ≥ the previous.
- A **descending run** is a sequence where each element < the previous. Descending runs are reversed in place (O(n) with two pointers) to make them ascending.

**Step 3 — Extend short runs with binary insertion sort.**
If a natural run is shorter than `minRun`, use binary insertion sort to extend it. Binary insertion sort uses binary search to find the insertion point (O(n log n) comparisons) while keeping the O(n²) shifts.

**Step 4 — Push runs onto a stack and merge.**
Each run is pushed as a `(start, length)` pair. After each push, Timsort checks two invariants and merges adjacent runs if either is violated:

```
Invariant 1: len[i-2] > len[i-1] + len[i]
Invariant 2: len[i-1] > len[i]
```

These invariants keep the stack shallow (O(log n)) and ensure the merge tree stays balanced, giving O(n log n) total merge work.

**Step 5 — Galloping mode during merge.**
When merging two runs, if one run wins many consecutive comparisons (default threshold: 7 in a row), Timsort switches to **galloping mode**: it uses binary search to find how far ahead the winning run extends. This accelerates merges when one run is much smaller than the other — common in real data.

**Step 6 — Force-merge remaining runs.**
At the end, merge all remaining runs on the stack until one sorted array remains.

### Example

```
Input: [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]

Step 2 — find runs:
  Run 1 (desc): [3, 1]      → reverse → [1, 3]
  Run 2 (asc):  [1, 4]
  Run 3 (asc):  [5, 9]
  Run 4 (desc): [6, 5, 3]   → reverse → [3, 5, 6]
  Run 5 (asc):  [5]

Step 3 — runs shorter than minRun are extended via binary insertion sort.

Steps 4–6 — merge until one array remains:
  [1, 3] + [1, 4] → [1, 1, 3, 4]
  [1, 1, 3, 4] + [5, 9] → [1, 1, 3, 4, 5, 9]
  ... continue until fully merged
```

| | Timsort |
|--|---------|
| Time | O(n log n) worst, **O(n) best** (already sorted) |
| Space | O(n) — temporary buffer during merge |
| Stable | **Yes** |
| In-place | No |

---

## When Each Algorithm Is Used

| Call | Array type | Algorithm | Stable |
|------|-----------|-----------|--------|
| `Arrays.sort(int[] a)` | primitive | Dual-pivot quicksort | No |
| `Arrays.sort(double[] a)` | primitive | Dual-pivot quicksort | No |
| `Arrays.sort(Object[] a)` | object | Timsort | **Yes** |
| `Arrays.sort(T[] a, Comparator<T> c)` | object | Timsort | **Yes** |

---

## All Object Sort Overloads

```java
// Full-array sorts
Arrays.sort(Object[] a)                                         // natural order (Comparable)
Arrays.sort(T[] a, Comparator<? super T> c)                    // custom order

// Range sorts — [fromIndex, toIndex)  (toIndex is exclusive)
Arrays.sort(Object[] a, int fromIndex, int toIndex)
Arrays.sort(T[] a, int fromIndex, int toIndex, Comparator<? super T> c)
```

```java
import java.util.Arrays;

// Natural order — requires Student implements Comparable<Student>
Student[] roster = { new Student("Zara", 3.2), new Student("Alice", 3.8) };
Arrays.sort(roster);

// Custom order — sort by name length
String[] words = {"banana", "apple", "cherry", "avocado"};
Arrays.sort(words, (a, b) -> a.length() - b.length());
System.out.println(Arrays.toString(words));
// [apple, banana, cherry, avocado]
// banana(6) stays before cherry(6) — stability preserved
```

---

## `Arrays.parallelSort`

Java 8 added `Arrays.parallelSort`, which uses a parallel merge sort for large arrays and falls back to `Arrays.sort` for small ones. It splits the array across multiple threads using the common `ForkJoinPool`.

```java
// Same API as Arrays.sort — drop-in replacement for large arrays
Arrays.parallelSort(scores);
Arrays.parallelSort(roster, byName);
```

Use `parallelSort` when sorting arrays of more than ~10,000 elements and multiple CPU cores are available. For typical use in this curriculum, `Arrays.sort` is the right default.

!!! note "JavaDoc"
    Full API reference: [`java.util.Arrays`](https://docs.oracle.com/en/java/docs/api/java.base/java/util/Arrays.html)

---

## Exercise

1. Add `implements Comparable<Student>` to a `Student` class with `name` (String) and `gpa` (double) fields. Implement `compareTo` to sort by GPA ascending. Sort a `Student[]` with `Arrays.sort` and print the result.

2. Write two `Comparator<Student>` instances as lambdas: one that sorts by name alphabetically, and one that sorts by GPA descending. Sort the same array both ways and confirm the output.

3. Create a `Comparator` that sorts by GPA descending and uses name as a tiebreaker for equal GPAs. Use `Comparator.comparingDouble(...).reversed().thenComparing(...)`.

4. Explain why `Arrays.sort` uses Timsort for objects but dual-pivot quicksort for primitives. What property of objects requires a stable sort that primitives do not?
