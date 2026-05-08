# Interfaces

An interface is a contract. It declares what a class can do without specifying how. Any class that `implements` the interface must provide the implementations.

---

## The Problem Interfaces Solve

Abstract classes from [Module 23](../23-polymorphism-abstract/index.md) enforce a contract through inheritance — but Java only allows a class to extend **one** parent. Interfaces remove that limit. A class can implement as many interfaces as it needs.

```java
// A class can only extend one parent:
class Dog extends Animal { ... }

// But it can implement multiple interfaces:
class Dog extends Animal implements Runnable, Comparable<Dog> { ... }
```

---

## Declaring an Interface

```java
public interface Driveable {
    void drive(double speed);
    void stop();
}
```

- All methods without a body are implicitly `public abstract`
- All fields are implicitly `public static final`
- Save in its own file: `Driveable.java`

---

## Implementing an Interface

```java
public class Car implements Driveable {

    @Override
    public void drive(double speed) {
        System.out.println("Car driving at " + speed);
    }

    @Override
    public void stop() {
        System.out.println("Car stopped");
    }
}
```

The compiler enforces the contract: if `Car` does not implement every method declared in `Driveable`, it will not compile (unless `Car` is declared `abstract`).

---

## Multiple Interfaces

```java
public interface Chargeable {
    void charge();
    double getBatteryLevel();
}

public class ElectricCar extends Car implements Chargeable {

    private double battery = 100.0;

    @Override
    public void charge() { battery = 100.0; }

    @Override
    public double getBatteryLevel() { return battery; }
}
```

---

## Interface as a Type

An interface can be used as a variable type, just like a class. This is where the power shows — code that depends on `Driveable` works with any class that implements it.

```java
Driveable d = new Car();
d.drive(0.5);   // calls Car's implementation

Driveable e = new ElectricCar();
e.drive(0.3);   // calls ElectricCar's implementation (inherits from Car)
```

---

## `default` Methods

A `default` method provides a body inside the interface. Classes inherit it automatically but can override it if needed.

```java
public interface Loggable {
    String getLabel();

    default void log(String message) {
        System.out.println("[" + getLabel() + "] " + message);
    }
}
```

```java
public class Sensor implements Loggable {
    @Override
    public String getLabel() { return "DistanceSensor"; }
    // log() is inherited — no need to override
}

Sensor s = new Sensor();
s.log("reading: 1.5m");   // [DistanceSensor] reading: 1.5m
```

Default methods let you add new behaviour to an interface without breaking every existing implementation.

---

## `static` Interface Methods

A `static` method on an interface belongs to the interface itself, not to implementing classes.

```java
public interface MathUtil {
    static double clamp(double value, double min, double max) {
        if (value < min) return min;
        if (value > max) return max;
        return value;
    }
}
```

```java
double speed = MathUtil.clamp(1.8, -1.0, 1.0);   // 1.0
```

---

## Common Java Standard Library Interfaces

You have already used some of these without knowing they were interfaces:

| Interface | Where you see it | Key method |
|-----------|-----------------|------------|
| `Comparable<T>` | Objects that can be sorted (`String`, `Integer`) | `int compareTo(T other)` |
| `Comparator<T>` | External sort logic, passed to `Arrays.sort` | `int compare(T a, T b)` |
| `Iterable<T>` | Anything usable in a `for-each` loop | `Iterator<T> iterator()` |
| `Runnable` | A task with no return value | `void run()` |
| `AutoCloseable` | Resources in try-with-resources | `void close()` |

### `Comparable<T>` — Making Your Objects Sortable

`Comparable<T>` is the interface that gives an object a **natural ordering**. It has one method:

```java
int compareTo(T other)
```

The return value follows a fixed contract:
- **negative** → `this` comes before `other`
- **zero** → `this` and `other` are equal
- **positive** → `this` comes after `other`

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
        // Sort by GPA descending (highest first)
        if (this.gpa > other.gpa) return -1;
        if (this.gpa < other.gpa) return  1;
        return 0;
    }

    @Override
    public String toString() { return name + " (" + gpa + ")"; }
}
```

Once a class implements `Comparable`, `Arrays.sort` and `Collections.sort` can sort it without any extra argument:

```java
Student[] roster = {
    new Student("Alice", 3.6),
    new Student("Bob",   3.9),
    new Student("Carol", 3.7)
};

Arrays.sort(roster);

for (Student s : roster) System.out.println(s);
// Bob (3.9)
// Carol (3.7)
// Alice (3.6)
```

`String` and all numeric wrapper classes (`Integer`, `Double`, etc.) already implement `Comparable` — that is why you can sort arrays of them directly.

---

## Interface vs. Abstract Class

| | Abstract Class | Interface |
|--|---------------|-----------|
| Can have fields | Yes | Only `public static final` |
| Can have constructors | Yes | No |
| Extends limit | One | Unlimited |
| Default implementations | Yes (regular methods) | Only `default` methods |
| Use when | Sharing state and partial implementation | Defining a capability contract |

Use an **abstract class** when subclasses share state (fields) or a partial implementation. Use an **interface** when you are defining a capability that multiple unrelated classes might have.

---

## Exercise

1. Declare an interface `Printable` with one method: `String formatted()`. Implement it in a `Student` class (fields: `name`, `gpa`) and a `Course` class (fields: `title`, `credits`). Write a method `printAll(Printable[] items)` that calls `formatted()` on each — notice it works for both types without knowing which is which.

2. Add a `default` method `void printToConsole()` to `Printable` that calls `System.out.println(formatted())`. Confirm that `Student` and `Course` inherit it without any changes.

3. Declare an interface `Comparable2<T>` with one method `int compareTo2(T other)`. Implement it in a `Box` class that compares by volume. Write a method that finds the largest `Box` in an array using only the interface method.
