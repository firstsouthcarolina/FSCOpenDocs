# ArrayList

Arrays have a fixed size set at creation time. `ArrayList` is a resizable list that grows automatically — and it is the foundation for the data structures covered in the rest of this curriculum.

!!! note "From CSCE 146 — Java Review"
    Not in the original FSC spec, which restricted Module 11 to fixed-size arrays. `ArrayList` is introduced here as a bridge to CSCE 146 content: linked lists, stacks, queues, and the Java Collections Framework used throughout the course.

---

## The Problem with Fixed Arrays

```java
int[] scores = new int[5];
// Need to store a 6th score? You cannot — the array is full.
// You have to create a new larger array and copy everything over.
```

`ArrayList` solves this by managing its own internal array and resizing it automatically when it runs out of space.

---

## Importing and Creating an ArrayList

```java
import java.util.ArrayList;

ArrayList<String> names = new ArrayList<>();
ArrayList<Integer> scores = new ArrayList<>();
ArrayList<Double> readings = new ArrayList<>();
```

The `<String>` part is the **type parameter** — it tells the compiler what type of object this list holds. This is called **generics**. The `<>` on the right side (the diamond) lets the compiler infer the type from the left side.

---

## Primitive Types and Autoboxing

`ArrayList` cannot hold primitive types directly. Use the wrapper classes instead:

| Primitive | Wrapper class |
|-----------|--------------|
| `int` | `Integer` |
| `double` | `Double` |
| `boolean` | `Boolean` |
| `char` | `Character` |
| `long` | `Long` |

Java automatically converts between primitives and their wrappers — this is called **autoboxing** (wrapping) and **unboxing** (unwrapping):

```java
ArrayList<Integer> nums = new ArrayList<>();
nums.add(42);          // autoboxing: int 42 → Integer.valueOf(42)
int n = nums.get(0);   // unboxing:   Integer → int
```

---

## Core Methods

```java
ArrayList<String> names = new ArrayList<>();

names.add("Alice");            // append to end
names.add("Bob");
names.add("Charlie");

System.out.println(names.size());         // 3 — number of elements
System.out.println(names.get(1));         // "Bob" — zero-based index
System.out.println(names.contains("Bob")); // true
System.out.println(names.indexOf("Charlie")); // 2

names.set(1, "Barbara");       // replace index 1 — now: [Alice, Barbara, Charlie]
names.add(0, "Aaron");         // insert at index 0 — now: [Aaron, Alice, Barbara, Charlie]

names.remove(0);               // remove by index — now: [Alice, Barbara, Charlie]
names.remove("Barbara");       // remove by value — now: [Alice, Charlie]

System.out.println(names.isEmpty()); // false
names.clear();                 // remove everything
System.out.println(names.isEmpty()); // true
```

---

## Traversal

### `for-each` loop (preferred when index is not needed)

```java
ArrayList<String> names = new ArrayList<>();
names.add("Alice");
names.add("Bob");
names.add("Charlie");

for (String name : names) {
    System.out.println(name);
}
```

### Indexed `for` loop (when you need the index)

```java
for (int i = 0; i < names.size(); i++) {
    System.out.println(i + ": " + names.get(i));
}
```

### Safe Removal During Iteration

Calling `remove` inside a forward `for-each` loop throws `ConcurrentModificationException`. Removing inside a forward indexed loop skips elements because the indexes shift after each removal.

```java
// WRONG — skips "Bob" when "Alice" is removed because Bob shifts to index 0
ArrayList<String> names = new ArrayList<>(List.of("Alice", "Bob", "Charlie"));
for (int i = 0; i < names.size(); i++) {
    if (names.get(i).startsWith("A")) {
        names.remove(i);   // after removal, "Bob" is now at index 0 — the loop skips it
    }
}
```

**Fix 1 — iterate backwards:** removing a later index does not affect earlier ones.

```java
for (int i = names.size() - 1; i >= 0; i--) {
    if (names.get(i).startsWith("A")) {
        names.remove(i);   // safe — earlier indexes are unaffected
    }
}
```

**Fix 2 — decrement the index after removal:**

```java
for (int i = 0; i < names.size(); i++) {
    if (names.get(i).startsWith("A")) {
        names.remove(i);
        i--;   // compensate for the shift
    }
}
```

---

## Sorting an ArrayList

### `Collections.sort()` — Natural Order

`Collections.sort()` sorts an `ArrayList` in ascending natural order. For `String`, that is alphabetical; for `Integer`, it is numeric.

```java
import java.util.Collections;

ArrayList<Integer> scores = new ArrayList<>(List.of(88, 72, 95, 61, 84));
Collections.sort(scores);
System.out.println(scores);   // [61, 72, 84, 88, 95]

ArrayList<String> names = new ArrayList<>(List.of("Charlie", "Alice", "Bob"));
Collections.sort(names);
System.out.println(names);    // [Alice, Bob, Charlie]
```

### Sorting Objects That Implement `Comparable`

If a class implements `Comparable<T>` (covered in [Module 24](../24-interfaces/index.md#comparable-making-your-objects-sortable)), `Collections.sort()` uses its `compareTo` method automatically.

```java
ArrayList<Student> roster = new ArrayList<>();
roster.add(new Student("Alice", 3.6));
roster.add(new Student("Bob",   3.9));
roster.add(new Student("Carol", 3.7));

Collections.sort(roster);   // uses Student.compareTo — sorts by GPA descending
```

### Reversing and Other Utilities

```java
Collections.reverse(scores);    // reverses in place
Collections.shuffle(scores);    // randomises order
int lo = Collections.min(scores);
int hi = Collections.max(scores);
```

---

## Converting Between Array and ArrayList

```java
// Array → ArrayList
String[] arr = {"Alice", "Bob", "Charlie"};
ArrayList<String> list = new ArrayList<>(java.util.Arrays.asList(arr));

// ArrayList → Array
String[] back = list.toArray(new String[0]);
```

---

## Lambdas with ArrayList

Lambdas from [Module 30](../30-lambdas/index.md) work naturally with `ArrayList`. Two patterns you will use constantly:

**`list.sort(comparator)`** — sort in place using a lambda:

```java
ArrayList<String> names = new ArrayList<>(List.of("Charlie", "Alice", "Bob"));

names.sort((a, b) -> a.compareTo(b));           // alphabetical ascending
System.out.println(names);                      // [Alice, Bob, Charlie]

names.sort((a, b) -> b.compareTo(a));           // descending
System.out.println(names);                      // [Charlie, Bob, Alice]

names.sort((a, b) -> a.length() - b.length());  // by length
System.out.println(names);                      // [Bob, Alice, Charlie]
```

**`list.forEach(consumer)`** — visit every element:

```java
names.forEach(name -> System.out.println(name));

// Equivalent with a method reference
names.forEach(System.out::println);
```

`list.sort` and `list.forEach` accept a `Comparator<T>` and `Consumer<T>` respectively — the same functional interfaces from Module 30.

---

## ArrayList vs Array

| | Array | ArrayList |
|--|-------|-----------|
| Size | Fixed at creation | Grows dynamically |
| Syntax | `int[]`, `String[]` | `ArrayList<Integer>`, `ArrayList<String>` |
| Primitives | Supported | Wrappers only (`Integer`, `Double`, …) |
| Length | `.length` (field) | `.size()` (method) |
| Insert/remove middle | Manual shifting | Built-in `add(i, val)` / `remove(i)` |
| Performance | Faster for random access | Slight overhead from boxing and resizing |
| When to use | Known, fixed size; primitives in tight loops | Unknown size; convenience operations |

---

## Practical Example — Building a List from User Input

```java
import java.util.ArrayList;
import java.util.Scanner;

public class NameCollector {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        ArrayList<String> names = new ArrayList<>();

        System.out.println("Enter names (blank line to stop):");
        while (true) {
            String input = sc.nextLine().trim();
            if (input.isEmpty()) break;
            names.add(input);
        }

        System.out.println("\nYou entered " + names.size() + " names:");
        for (int i = 0; i < names.size(); i++) {
            System.out.println((i + 1) + ". " + names.get(i));
        }

        sc.close();
    }
}
```

---

## Exercise

1. Write a program that collects integers from the user (one per line, stop at `-1`) into an `ArrayList<Integer>`. Print the list, the sum, and the average.
2. Write a method `removeDuplicates(ArrayList<String> list)` that returns a new `ArrayList<String>` with all duplicate values removed (preserve the first occurrence). Test it with a list containing several repeated names.
3. Write a generic method `printAll(ArrayList<T> list)` that prints every element on its own line. Call it with an `ArrayList<String>` and an `ArrayList<Integer>`.
