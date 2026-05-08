# Maps and the Collections Framework

A `Map` stores key-value pairs. Given a key, you get its associated value back in O(1). It is the third major collection type alongside `List` and `Queue`.

---

## The Problem Maps Solve

Arrays and lists give you values at numeric indexes. Maps give you values at **any key you choose** — a `String`, an `int`, an object.

```java
// Instead of parallel arrays — error-prone and hard to maintain:
String[] names  = {"Alice", "Bob", "Charlie"};
int[]    scores = {95,      87,    91};

// Use a Map — key and value stay together:
Map<String, Integer> scores = new HashMap<>();
scores.put("Alice",   95);
scores.put("Bob",     87);
scores.put("Charlie", 91);

System.out.println(scores.get("Bob"));   // 87
```

---

## `HashMap<K, V>`

`HashMap` is the most common implementation: fast O(1) average for `get` and `put`, but **no guaranteed order**.

```java
import java.util.HashMap;
import java.util.Map;

Map<String, Integer> population = new HashMap<>();

// Adding entries
population.put("Columbia",    137541);
population.put("Charleston",  150227);
population.put("Greenville",   70635);

// Reading
System.out.println(population.get("Columbia"));         // 137541
System.out.println(population.get("Spartanburg"));      // null — key not present
System.out.println(population.getOrDefault("Spartanburg", 0));  // 0

// Checking
System.out.println(population.containsKey("Charleston"));   // true
System.out.println(population.size());                       // 3

// Removing
population.remove("Greenville");
```

---

## Iterating a Map

```java
// Keys only
for (String city : population.keySet()) {
    System.out.println(city);
}

// Values only
for (int pop : population.values()) {
    System.out.println(pop);
}

// Key-value pairs — the most common pattern
for (Map.Entry<String, Integer> entry : population.entrySet()) {
    System.out.println(entry.getKey() + " → " + entry.getValue());
}
```

---

## Common Map Methods

| Method | What it does |
|--------|-------------|
| `put(key, value)` | Insert or overwrite |
| `get(key)` | Retrieve value, `null` if absent |
| `getOrDefault(key, fallback)` | Retrieve value, `fallback` if absent |
| `containsKey(key)` | Check if key exists |
| `containsValue(value)` | Check if value exists (O(n)) |
| `remove(key)` | Delete an entry |
| `size()` | Number of entries |
| `isEmpty()` | `true` if no entries |
| `keySet()` | `Set<K>` of all keys |
| `values()` | `Collection<V>` of all values |
| `entrySet()` | `Set<Map.Entry<K,V>>` of all pairs |
| `putIfAbsent(key, value)` | Only inserts if key is not already present |

---

## Map Factory Methods

For small, fixed maps:

```java
Map<String, Integer> codes = Map.of(
    "SC", 803,
    "NC", 704,
    "GA", 404
);
// Returns an unmodifiable map — put/remove throw UnsupportedOperationException
```

---

## Using Objects as Keys — `equals` and `hashCode`

`HashMap` stores entries in **buckets** determined by the key's `hashCode()`. When you call `get(key)`, Java first calls `key.hashCode()` to find the right bucket, then calls `key.equals(candidate)` to confirm the match.

This creates a contract every class must honour if its instances will ever be used as map keys:

> **If `a.equals(b)` is `true`, then `a.hashCode()` must equal `b.hashCode()`.**

The reverse is not required — two unequal objects may share the same hash code (a *collision*), and `HashMap` handles that gracefully. But if equal objects have different hash codes, `get()` will look in the wrong bucket and return `null` even when the key is present.

`String` and all wrapper types (`Integer`, `Double`, etc.) already implement both methods correctly. For a custom class, you must override them together. The easiest way is `Objects.hash()`:

```java
import java.util.Objects;

public class Student {
    private String name;
    private int id;

    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Student)) return false;
        Student other = (Student) obj;
        return this.id == other.id && this.name.equals(other.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, id);   // same fields as equals()
    }
}
```

```java
Map<Student, Double> grades = new HashMap<>();
Student alice = new Student("Alice", 1001);
grades.put(alice, 3.9);

Student lookup = new Student("Alice", 1001);   // different object, same data
System.out.println(grades.get(lookup));        // 3.9 — works because hashCode matches
```

Without the `hashCode()` override, `grades.get(lookup)` would return `null` — the two `Student` objects would hash to different buckets even though `equals()` says they are the same.

**Rule of thumb:** whenever you override `equals()`, override `hashCode()` using the same fields. IDEs and tools like `Objects.hash()` make this mechanical.

---

## Other Map Implementations

| Class | Order | Use when |
|-------|-------|----------|
| `HashMap` | None | Fastest; order does not matter |
| `LinkedHashMap` | Insertion order | You need to iterate in the order entries were added |
| `TreeMap` | Natural key order (sorted) | You need keys in sorted order; O(log n) operations |

```java
Map<String, Integer> sorted = new TreeMap<>(population);
// iterates cities alphabetically
```

---

## `Set<T>` — a Brief Aside

A `Set` is like a `List` with no duplicates and no meaningful index. `HashSet` is the common implementation.

```java
Set<String> seen = new HashSet<>();
seen.add("alpha");
seen.add("beta");
seen.add("alpha");   // ignored — already present
System.out.println(seen.size());   // 2
```

Sets are used internally by `Map.keySet()` — that is why the return type is `Set<K>` and not `List<K>`.

---

## The Java Collections Framework

All the collection types fit into a single hierarchy:

```
Iterable<T>
  └── Collection<T>
        ├── List<T>          — ordered, indexed, duplicates allowed
        │     ├── ArrayList<T>
        │     └── LinkedList<T>
        ├── Set<T>           — no duplicates, no index
        │     ├── HashSet<T>
        │     └── TreeSet<T>
        └── Queue<T>         — FIFO ordering
              ├── LinkedList<T>
              └── ArrayDeque<T>

Map<K, V>                    — key→value pairs (not a Collection)
  ├── HashMap<K, V>
  ├── LinkedHashMap<K, V>
  └── TreeMap<K, V>
```

All `List`, `Set`, and `Queue` types support `for-each` because they implement `Iterable`. `Map` does not extend `Collection` — you iterate its `keySet()`, `values()`, or `entrySet()` instead.

---

## `Collections` Utility Class

`java.util.Collections` provides static helper methods:

```java
List<Integer> nums = new ArrayList<>(List.of(3, 1, 4, 1, 5, 9));

Collections.sort(nums);             // [1, 1, 3, 4, 5, 9]
Collections.reverse(nums);          // [9, 5, 4, 3, 1, 1]
Collections.shuffle(nums);          // random order
System.out.println(Collections.min(nums));   // smallest
System.out.println(Collections.max(nums));   // largest
Collections.fill(nums, 0);          // every element set to 0

List<Integer> safe = Collections.unmodifiableList(nums);
// safe.add(1); ← throws UnsupportedOperationException
```

---

## Exercise

1. Write a program that counts word frequencies in a `String[]`. Store the results in a `Map<String, Integer>`. Print each word and its count. Use `getOrDefault` to handle words appearing for the first time.

2. Given two `Map<String, Integer>` instances representing team scores, write a method that merges them into a single map — if a team appears in both, sum the scores.

3. Write a method that inverts a `Map<String, Integer>` into a `Map<Integer, String>` (assume values are unique). What happens to the result if two keys have the same value?

4. Use a `TreeMap` to store the months of the year (name → number). Iterate and print them. Explain why the order is alphabetical, not calendar order, and how you would fix it.
