---
icon: lucide/list-plus
---

# ArrayList

`ArrayList` stores a resizable list of objects. Unlike arrays, it can grow and shrink as the program runs.

```java
import java.util.ArrayList;

ArrayList<String> mechanisms = new ArrayList<>();
mechanisms.add("drivetrain");
mechanisms.add("intake");
mechanisms.add("shooter");
```

## Common Operations

```java
String first = mechanisms.get(0);
mechanisms.set(1, "indexer");
mechanisms.remove("shooter");
int count = mechanisms.size();
boolean hasIntake = mechanisms.contains("intake");
```

## Looping

```java
for (String mechanism : mechanisms) {
    System.out.println(mechanism);
}
```

Use indexes when you need positions:

```java
for (int i = 0; i < mechanisms.size(); i++) {
    System.out.println(i + ": " + mechanisms.get(i));
}
```

## Primitive Types

Generics require object types, so use wrapper classes for primitives:

```java
ArrayList<Integer> scores = new ArrayList<>();
ArrayList<Double> voltages = new ArrayList<>();
```

Java automatically boxes and unboxes many primitive wrapper values.

## Practice

Create an `ArrayList<Double>` of match scores. Add values, remove one value, and calculate the average.
