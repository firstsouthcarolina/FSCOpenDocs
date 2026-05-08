# Streams and Optional

A stream is a pipeline that processes a sequence of values — filter, transform, reduce — without modifying the original collection. `Optional` is a wrapper for a value that may or may not be present, eliminating `null` as a special case.

Both require lambdas from [Module 30](../30-lambdas/index.md).

---

## What is a Stream?

A stream is **not** a data structure. It is a pipeline that lazily processes elements from a source (a collection, an array, a range) through a chain of operations.

```
source → [intermediate ops] → [terminal op]
list     .filter(...)          .toList()
         .map(...)             .count()
         .sorted(...)          .forEach(...)
```

The pipeline does not execute until a **terminal operation** is reached.

---

## Getting a Stream

```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Dave");

Stream<String> s = names.stream();          // from any Collection
Stream<String> p = names.parallelStream();  // parallel version (advanced)
Stream<Integer> r = Stream.of(1, 2, 3, 4); // from values directly
IntStream      n = IntStream.range(0, 5);  // 0, 1, 2, 3, 4
```

---

## Intermediate Operations

Intermediate operations return a new stream. They are **lazy** — nothing runs until a terminal operation is called.

| Operation | What it does |
|-----------|-------------|
| `filter(Predicate<T>)` | Keep only elements that pass the test |
| `map(Function<T,R>)` | Transform each element |
| `mapToInt(ToIntFunction<T>)` | Transform to a primitive `int` stream |
| `sorted()` | Sort in natural order |
| `sorted(Comparator<T>)` | Sort with a custom comparator |
| `distinct()` | Remove duplicates |
| `limit(long n)` | Keep only the first n elements |
| `skip(long n)` | Discard the first n elements |
| `peek(Consumer<T>)` | Observe each element without changing it (debugging) |

```java
List<String> names = List.of("Alice", "Bob", "Charlie", "Dave", "Anna");

List<String> result = names.stream()
    .filter(n -> n.startsWith("A"))    // Alice, Anna
    .map(String::toUpperCase)          // ALICE, ANNA
    .sorted()                          // ALICE, ANNA (already sorted)
    .toList();

System.out.println(result);   // [ALICE, ANNA]
```

---

## Terminal Operations

A terminal operation triggers the pipeline and produces a result. After a terminal operation, the stream is consumed and cannot be reused.

| Operation | What it returns |
|-----------|----------------|
| `toList()` | `List<T>` (Java 16+) |
| `collect(Collectors.toList())` | `List<T>` (older Java) |
| `forEach(Consumer<T>)` | `void` — process each element |
| `count()` | `long` — number of elements |
| `findFirst()` | `Optional<T>` — first element, if any |
| `findAny()` | `Optional<T>` — any element (useful in parallel) |
| `anyMatch(Predicate<T>)` | `boolean` — true if any element passes |
| `allMatch(Predicate<T>)` | `boolean` — true if all elements pass |
| `noneMatch(Predicate<T>)` | `boolean` — true if no element passes |
| `min(Comparator<T>)` | `Optional<T>` — smallest element |
| `max(Comparator<T>)` | `Optional<T>` — largest element |
| `reduce(identity, BinaryOperator<T>)` | `T` — fold all elements into one |
| `toArray()` | `Object[]` |
| `toArray(T[]::new)` | `T[]` — typed array |

```java
List<Integer> nums = List.of(3, 1, 4, 1, 5, 9, 2, 6);

long count      = nums.stream().filter(n -> n > 3).count();           // 4
int  sum        = nums.stream().mapToInt(Integer::intValue).sum();    // 31
Optional<Integer> max = nums.stream().max(Integer::compareTo);        // Optional[9]
boolean anyBig  = nums.stream().anyMatch(n -> n > 8);                 // true
```

---

## `collect` and Collectors

`collect` gathers stream results into a container.

```java
import java.util.stream.Collectors;

List<String> names = List.of("Alice", "Bob", "Charlie");

// Collect to a list
List<String> shortNames = names.stream()
    .filter(n -> n.length() <= 3)
    .collect(Collectors.toList());    // [Bob]

// Collect to a map: name → length
Map<String, Integer> lengths = names.stream()
    .collect(Collectors.toMap(n -> n, String::length));
// {Alice=5, Bob=3, Charlie=7}

// Group by first letter
Map<Character, List<String>> grouped = names.stream()
    .collect(Collectors.groupingBy(n -> n.charAt(0)));
// {A=[Alice], B=[Bob], C=[Charlie]}

// Join into a String
String joined = names.stream()
    .collect(Collectors.joining(", "));
// "Alice, Bob, Charlie"
```

---

## `reduce`

`reduce` folds the entire stream into a single value.

```java
List<Integer> nums = List.of(1, 2, 3, 4, 5);

int sum     = nums.stream().reduce(0, (a, b) -> a + b);    // 15
int product = nums.stream().reduce(1, (a, b) -> a * b);    // 120
int max     = nums.stream().reduce(Integer.MIN_VALUE, Integer::max);  // 5
```

---

## Streams vs. Loops

Streams are not always better. Use the one that is clearer.

```java
// Loop — clear when the logic is complex or has side effects
int sum = 0;
for (int n : nums) {
    if (n > 3) sum += n;
}

// Stream — clear when filtering and transforming
int sum = nums.stream().filter(n -> n > 3).mapToInt(i -> i).sum();
```

Prefer streams for pure transformations and filtering. Prefer loops when you need to `break` early, maintain complex state, or modify external variables.

---

## `Optional<T>`

`Optional<T>` is a container for a value that might not exist. It forces you to handle the absent case instead of silently returning `null`.

```java
Optional<String> present = Optional.of("hello");
Optional<String> empty   = Optional.empty();
Optional<String> maybe   = Optional.ofNullable(null);   // empty
```

### Checking and Extracting

```java
Optional<String> name = Optional.of("Alice");

name.isPresent();              // true
name.isEmpty();                // false
name.get();                    // "Alice" — throws if empty

name.orElse("Unknown");        // "Alice" (falls back only if empty)
name.orElseGet(() -> compute()); // lazy fallback — only calls compute() if empty
name.orElseThrow();            // throws NoSuchElementException if empty
```

### Transforming Without Unpacking

```java
Optional<String> name = Optional.of("alice");

Optional<String> upper = name.map(String::toUpperCase);  // Optional["ALICE"]
Optional<String> long_ = name.filter(n -> n.length() > 3); // Optional["alice"]

name.ifPresent(n -> System.out.println("Found: " + n));   // Found: alice
```

### Common Pattern — Stream Terminal Operations Return `Optional`

```java
List<Integer> nums = List.of(3, 1, 4, 1, 5);

Optional<Integer> first = nums.stream()
    .filter(n -> n > 3)
    .findFirst();

first.ifPresent(n -> System.out.println("First > 3: " + n));   // First > 3: 4
int result = first.orElse(-1);   // 4
```

### Why Not Just Use `null`?

```java
// null — the caller might forget to check
String name = findUser(id);
System.out.println(name.toUpperCase());   // NullPointerException if not found

// Optional — the caller must handle both cases
Optional<String> name = findUser(id);
name.ifPresent(n -> System.out.println(n.toUpperCase()));
```

---

## Exercise

1. Given a `List<String>` of words, use a single stream pipeline to: filter out words shorter than 4 characters, convert the remaining words to uppercase, sort them alphabetically, and collect them into a `List<String>`.

2. Given a `List<Integer>`, use streams to: find the sum, the average (hint: `mapToInt(...).average()` returns an `OptionalDouble`), and the count of even numbers — each in one line.

3. Write a method `Optional<String> findFirst(List<String> words, Predicate<String> test)` that returns the first word in the list that passes the predicate, or an empty `Optional` if none does. Use `stream().filter(...).findFirst()`.

4. Given a `List<String>` representing student names and a separate `List<Integer>` of their scores (same order), use `Collectors.toMap` to build a `Map<String, Integer>`. Then use a stream on the map's `entrySet()` to find the student with the highest score.
