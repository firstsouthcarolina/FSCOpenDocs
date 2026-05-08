# Lambdas, Functional Interfaces, and Method References

A lambda is a short, anonymous function you can pass as a value. It eliminates the need to write a named class just to hold a single method.

---

## The Problem

Before Java 8, passing behaviour meant creating an anonymous class:

```java
// Old way — verbose
Arrays.sort(names, new Comparator<String>() {
    @Override
    public int compare(String a, String b) {
        return a.length() - b.length();
    }
});
```

A lambda replaces all of that boilerplate:

```java
// Lambda — concise
Arrays.sort(names, (a, b) -> a.length() - b.length());
```

---

## What is a Functional Interface?

A **functional interface** is any interface with exactly one abstract method. That single method is what the lambda implements.

```java
@FunctionalInterface
public interface Transformer {
    double transform(double input);
}
```

The `@FunctionalInterface` annotation is optional but documents the intent and makes the compiler enforce the one-method rule.

---

## Lambda Syntax

```
(parameters) -> expression
(parameters) -> { statements; }
```

| Form | Example |
|------|---------|
| No parameters | `() -> System.out.println("hello")` |
| One parameter (parens optional) | `x -> x * 2` |
| Multiple parameters | `(a, b) -> a + b` |
| Multi-line body | `(x) -> { double r = x * 2; return r; }` |

Types are inferred from context — you rarely need to write them.

```java
Transformer doubler = x -> x * 2;
System.out.println(doubler.transform(5.0));   // 10.0

Transformer squarer = x -> x * x;
System.out.println(squarer.transform(3.0));   // 9.0
```

---

## `java.util.function` — Built-in Functional Interfaces

Java provides ready-made functional interfaces for the most common patterns. You never need to declare your own for these cases.

| Interface | Method signature | Use for |
|-----------|-----------------|---------|
| `Runnable` | `void run()` | A task with no input or output |
| `Supplier<T>` | `T get()` | Produce a value |
| `Consumer<T>` | `void accept(T t)` | Consume a value, no return |
| `Function<T, R>` | `R apply(T t)` | Transform a value |
| `Predicate<T>` | `boolean test(T t)` | Test a condition |
| `BiFunction<T, U, R>` | `R apply(T t, U u)` | Transform two inputs |
| `Comparator<T>` | `int compare(T a, T b)` | Define an ordering |

Primitive-specialised variants avoid autoboxing overhead:

| Interface | Method | Avoids boxing |
|-----------|--------|--------------|
| `DoubleSupplier` | `double getAsDouble()` | `double` → `Double` |
| `IntConsumer` | `void accept(int value)` | `int` → `Integer` |
| `DoublePredicate` | `boolean test(double value)` | `double` → `Double` |

```java
Supplier<String>   greeting  = () -> "Hello, world";
Consumer<String>   printer   = s  -> System.out.println(s);
Predicate<Integer> isEven    = n  -> n % 2 == 0;
Function<String, Integer> len = s -> s.length();

printer.accept(greeting.get());    // Hello, world
System.out.println(isEven.test(4)); // true
System.out.println(len.apply("hi")); // 2
```

---

## Lambdas with Arrays

Lambdas work directly with arrays you already know. `Arrays.sort` accepts a `Comparator` lambda as its second argument:

```java
String[] names = {"Charlie", "Alice", "Bob"};

// sort with a Comparator lambda
Arrays.sort(names, (a, b) -> a.compareTo(b));
System.out.println(Arrays.toString(names));   // [Alice, Bob, Charlie]

// sort by length
Arrays.sort(names, (a, b) -> a.length() - b.length());
System.out.println(Arrays.toString(names));   // [Bob, Alice, Charlie]
```

Using lambdas with `ArrayList` is covered in [Module 32](../32-arraylist/index.md#lambdas-with-arraylist).

---

## Capturing Variables

A lambda can use variables from the surrounding scope as long as they are effectively final (never reassigned after their declaration).

```java
double factor = 1.5;
Function<Double, Double> scale = x -> x * factor;   // factor is captured
System.out.println(scale.apply(4.0));   // 6.0

// factor = 2.0;  ← compile error: would make factor non-effectively-final
```

---

## Method References

A method reference is a shorthand for a lambda that simply calls an existing method. Use `::` to write it.

| Kind | Syntax | Equivalent lambda |
|------|--------|------------------|
| Static method | `ClassName::staticMethod` | `x -> ClassName.staticMethod(x)` |
| Instance method on a specific object | `object::method` | `x -> object.method(x)` |
| Instance method on a parameter | `ClassName::method` | `(obj, x) -> obj.method(x)` |
| Constructor | `ClassName::new` | `() -> new ClassName()` |

```java
// Static method reference
Function<String, Integer> parse = Integer::parseInt;
System.out.println(parse.apply("42"));   // 42

// Instance method on a specific object
String prefix = "Hello";
Predicate<String> starts = prefix::startsWith;  // actually s -> prefix.startsWith(s)
// Note: this uses the 1-arg version — prefix is the receiver
Consumer<String> print = System.out::println;
print.accept("test");   // test

// Constructor reference
Supplier<StringBuilder> makeBuilder = StringBuilder::new;
StringBuilder sb = makeBuilder.get();

// Instance method on a parameter — used with stream operations
List<String> words = List.of("hello", "world");
words.stream()
     .map(String::toUpperCase)   // s -> s.toUpperCase()
     .forEach(System.out::println);
```

---

## Storing and Passing Lambdas

Lambdas are values — store them in variables, pass them to methods, return them from methods.

```java
public static double applyTwice(Function<Double, Double> f, double x) {
    return f.apply(f.apply(x));
}

System.out.println(applyTwice(x -> x * 2, 3.0));   // 12.0
System.out.println(applyTwice(x -> x + 10, 1.0));  // 21.0
```

---

## Exercise

1. Write a `Validator<T>` functional interface with one method `boolean isValid(T value)`. Create three lambdas: one that checks a `String` is not empty, one that checks a `Double` is between 0 and 1, and one that checks an `Integer` is positive. Test each.

2. Write a method `<T> List<T> filter(List<T> list, Predicate<T> test)` that returns a new list containing only items that pass the predicate. Test it with a list of integers, keeping only the even ones.

3. Rewrite the `names.forEach(name -> System.out.println(name))` call from above using a method reference. Explain why the two forms are equivalent.

4. Write a method `Comparator<String> byLength()` that returns a `Comparator` implemented as a lambda. Use it to sort a list of strings by length, shortest first.
