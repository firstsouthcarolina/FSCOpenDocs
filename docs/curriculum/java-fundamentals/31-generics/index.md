# Generics

A generic class or method works with any type, decided at the call site. You have already been using generics without knowing it — `ArrayList<String>`, `Comparator<Student>`, `Predicate<Integer>` all use the same `<T>` mechanism this module explains.

---

## The Problem Without Generics

Before Java 5, collections stored `Object` references and required a cast every time you retrieved a value:

```java
// Pre-generics — no type safety
List box = new ArrayList();
box.add("hello");
box.add(42);          // also compiles — no type check

String s = (String) box.get(1);  // ClassCastException at runtime — it's an Integer
```

With generics, the compiler catches the mistake at compile time:

```java
List<String> box = new ArrayList<>();
box.add("hello");
box.add(42);          // compile error — 42 is not a String
```

---

## Type Parameters

A type parameter is a placeholder — usually a single uppercase letter — that stands in for a real type when you write a class or method.

```java
public class Box<T> {
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }

    @Override
    public String toString() {
        return "Box[" + value + "]";
    }
}
```

`T` is the placeholder. When you create an instance, you supply the actual type:

```java
Box<String>  stringBox = new Box<>("Hello");
Box<Integer> intBox    = new Box<>(42);
Box<Double>  dblBox    = new Box<>(3.14);

System.out.println(stringBox.getValue());   // Hello
System.out.println(intBox.getValue());      // 42
```

The compiler substitutes `String` for `T` in one instance and `Integer` for `T` in another — but the class is written only once.

---

## Common Type Parameter Names

| Letter | Conventional meaning |
|--------|---------------------|
| `T` | Type (most common) |
| `E` | Element (collections) |
| `K` | Key (maps) |
| `V` | Value (maps) |
| `R` | Return type (functions) |

These are conventions, not rules. `T` works everywhere.

---

## Generic Methods

A method can be generic independently of its class. Declare the type parameter before the return type:

```java
public static <T> void printArray(T[] arr) {
    for (T element : arr) {
        System.out.print(element + " ");
    }
    System.out.println();
}
```

```java
String[]  words  = {"hello", "world"};
Integer[] nums   = {1, 2, 3};
Double[]  floats = {1.1, 2.2, 3.3};

printArray(words);   // hello world
printArray(nums);    // 1 2 3
printArray(floats);  // 1.1 2.2 3.3
```

Type inference: the compiler infers `T` from the argument, so you almost never write `Util.<String>printArray(words)` explicitly.

---

## Bounded Type Parameters

Use `extends` to restrict what types are allowed. This lets you call methods defined on the bound:

```java
// T must implement Comparable<T> — so you can call compareTo
public static <T extends Comparable<T>> T max(T a, T b) {
    return a.compareTo(b) >= 0 ? a : b;
}
```

```java
System.out.println(max(3, 7));          // 7
System.out.println(max("apple", "banana")); // banana
```

`T extends Comparable<T>` means "T must implement `Comparable<T>`." Passing a type that doesn't implement `Comparable` is a compile error.

---

## Generic Classes with Multiple Type Parameters

```java
public class Pair<A, B> {
    private final A first;
    private final B second;

    public Pair(A first, B second) {
        this.first  = first;
        this.second = second;
    }

    public A getFirst()  { return first; }
    public B getSecond() { return second; }

    @Override
    public String toString() {
        return "(" + first + ", " + second + ")";
    }
}
```

```java
Pair<String, Integer> nameAndAge = new Pair<>("Alice", 25);
Pair<Double, Double>  coordinates = new Pair<>(40.71, -74.01);

System.out.println(nameAndAge);    // (Alice, 25)
System.out.println(coordinates);  // (40.71, -74.01)
```

---

## Generics and Records

Records from [Module 25](../25-records/index.md) work exactly like classes as generic type arguments — and records can themselves be generic:

```java
// Record as a generic type argument
Box<Point> pointBox = new Box<>(new Point(3.0, 4.0));
System.out.println(pointBox.getValue());     // Point[x=3.0, y=4.0]

// Generic record
record Pair<A, B>(A first, B second) { }
Pair<String, Integer> p = new Pair<>("Alice", 99);
System.out.println(p.first() + " scored " + p.second());
```

Records are particularly natural as generic type arguments because their immutability means the generic container cannot accidentally receive a mutated value.

---

## Type Erasure

Generics are a compile-time feature. At runtime, the JVM does not know what `T` was — it only sees `Object`. This is called **type erasure**. The compiler inserts casts automatically where needed and removes the type parameter information.

The practical consequence: you cannot use a type parameter in `instanceof`, create an array of a type parameter (`new T[10]`), or use it in certain reflection scenarios. For this curriculum, type erasure is a footnote — everything in the modules ahead works correctly as written.

---

## Exercise

1. Write a generic class `Stack<T>` backed by an `ArrayList<T>`. Implement `push(T item)`, `T pop()`, `T peek()`, `boolean isEmpty()`, and `int size()`. Test it with a `Stack<String>` and a `Stack<Integer>`.

2. Write a static generic method `<T extends Comparable<T>> T min(T[] arr)` that returns the smallest element. Test it with `String[]`, `Integer[]`, and `Double[]`.

3. Write a generic record `Result<T>` with fields `T value` and `String message`. Use it as a return type for a method `Result<Integer> safeDivide(int a, int b)` that returns the quotient or an error message when `b == 0`. Print the result of several calls.
