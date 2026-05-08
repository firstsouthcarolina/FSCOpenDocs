---
icon: lucide/package-check
---

# Generics

Generics let classes and methods work with a type chosen by the caller.

```java
ArrayList<String> names = new ArrayList<>();
ArrayList<Integer> scores = new ArrayList<>();
```

The same `ArrayList` class can store different types while still preserving type safety.

## Generic Classes

```java
public class Box<T> {
    private T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

```java
Box<String> nameBox = new Box<>("Drive");
String name = nameBox.getValue();
```

## Generic Methods

```java
public static <T> T first(T[] values) {
    return values[0];
}
```

The `<T>` before the return type declares a generic type parameter.

## Bounds

Bounds limit what type is allowed.

```java
public static <T extends Comparable<T>> T max(T a, T b) {
    if (a.compareTo(b) >= 0) {
        return a;
    }

    return b;
}
```

## Practice

Create a generic `Pair<A, B>` record with `first` and `second` values. Use it to store a team number and team name.
