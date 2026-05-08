# Inherited and Polymorphic Object Arrays

Two distinct patterns for working with arrays of objects in a class hierarchy, placed side by side so the difference is clear.

---

## Quick Recap

In [Module 23](../23-polymorphism-abstract/index.md), `Shape` is abstract and `Circle`, `Rectangle`, and `Triangle` extend it. `Animal` is concrete and `Dog`, `Cat`, and `Bird` extend it. Both hierarchies are used below.

---

## Pattern 1 — Polymorphic Array

A **polymorphic array** stores objects through their parent-type reference and calls **overridden methods** on all of them uniformly. You saw this in the Array of Shapes example.

```java
Shape[] shapes = {
    new Circle(5),
    new Rectangle(4, 6),
    new Triangle(3, 4, 5)
};

for (Shape s : shapes) {
    System.out.println(s.area());       // calls Circle's, Rectangle's, or Triangle's area()
    System.out.println(s.perimeter());  // same — correct version called automatically
}
```

- Every element is accessed only through the `Shape` reference
- You can call any method declared in `Shape` — abstract or concrete
- You **cannot** call subclass-specific methods without downcasting (see below)
- Adding a new subclass requires no changes to this loop

---

## Pattern 2 — Inherited Array with `instanceof`

When subclasses have **unique methods** that the parent does not declare, you need to identify the actual type and downcast.

```java
public class Dog extends Animal {
    public void fetch() { System.out.println(name + " fetches the ball!"); }
}

public class Cat extends Animal {
    public void purr() { System.out.println(name + " purrs..."); }
}

public class Bird extends Animal {
    public void fly() { System.out.println(name + " takes flight!"); }
}
```

```java
Animal[] animals = {
    new Dog("Rex"),
    new Cat("Mittens"),
    new Bird("Tweety"),
    new Dog("Buddy")
};

for (Animal a : animals) {
    a.speak();                      // polymorphic — calls each subclass's speak()

    if (a instanceof Dog) {
        Dog d = (Dog) a;            // downcast: treat as Dog
        d.fetch();                  // Dog-specific method
    } else if (a instanceof Cat) {
        Cat c = (Cat) a;
        c.purr();
    } else if (a instanceof Bird) {
        Bird b = (Bird) a;
        b.fly();
    }
}
```

```
Rex barks!
Rex fetches the ball!
Mittens meows.
Mittens purrs...
Tweety tweets.
Tweety takes flight!
Buddy barks!
Buddy fetches the ball!
```

---

## `instanceof`

`instanceof` tests whether an object is an instance of a given class at runtime.

```java
Animal a = new Dog("Rex");

System.out.println(a instanceof Animal);  // true  — Dog is an Animal
System.out.println(a instanceof Dog);     // true  — it is actually a Dog
System.out.println(a instanceof Cat);     // false
```

Always check with `instanceof` before downcasting. Casting without checking throws a `ClassCastException` at runtime:

```java
// Dangerous — throws ClassCastException if a is not a Dog
Dog d = (Dog) a;

// Safe
if (a instanceof Dog) {
    Dog d = (Dog) a;
    d.fetch();
}
```

---

## Side-by-Side Comparison

| | Polymorphic Array | Inherited Array with `instanceof` |
|--|-------------------|-----------------------------------|
| Array declared as | `Shape[]` / parent type | `Animal[]` / parent type |
| Methods called | Only methods declared in the parent | Parent methods + subclass-specific methods |
| How to call subclass methods | Not directly — must downcast | `instanceof` check + explicit cast |
| Extensibility | High — add a subclass, loop unchanged | Lower — add a subclass, add a new `instanceof` branch |
| When to use | Uniform behavior across all subtypes | Different subtypes need different extra behavior |

---

## Why Heavy `instanceof` Use Is a Warning Sign

If your loop has many `instanceof` checks for different subclasses, it often means the class hierarchy should be redesigned. The behavior you are checking for could usually be pushed down into a method on the parent:

```java
// Smells like a design problem
if (a instanceof Dog) { d.fetch(); }
else if (a instanceof Cat) { c.purr(); }
else if (a instanceof Bird) { b.fly(); }

// Better design — add an abstract method to the parent
public abstract class Animal {
    public abstract void doSpecialAction();
}
// Each subclass implements it, and the loop becomes:
for (Animal a : animals) {
    a.doSpecialAction();   // no instanceof needed
}
```

`instanceof` and downcasting are legitimate tools, but reaching for them constantly suggests the polymorphism is not being fully used.

---

## Counting Subclass Types in an Array

```java
Animal[] animals = {
    new Dog("Rex"), new Cat("Mittens"), new Dog("Buddy"),
    new Bird("Tweety"), new Cat("Whiskers")
};

int dogs = 0, cats = 0, birds = 0;

for (Animal a : animals) {
    if (a instanceof Dog)  dogs++;
    else if (a instanceof Cat)  cats++;
    else if (a instanceof Bird) birds++;
}

System.out.println("Dogs: " + dogs);    // 2
System.out.println("Cats: " + cats);    // 2
System.out.println("Birds: " + birds);  // 1
```

---

## Using Both Patterns Together

A real program might use both: polymorphism for shared behavior, targeted `instanceof` for the rare case where a specific subtype needs special handling.

```java
Shape[] shapes = {
    new Circle(5), new Rectangle(4, 6), new Triangle(3, 4, 5)
};

double totalArea = 0;
for (Shape s : shapes) {
    totalArea += s.area();                          // polymorphic

    if (s instanceof Circle) {
        System.out.println("(This is a circle)");   // type-specific note
    }
}
System.out.println("Total area: " + totalArea);
```

---

## Records in Polymorphic Arrays

From [Module 25](../25-records/index.md), a record can implement an interface. That means records participate in polymorphic arrays the same way classes do — the caller only sees the interface type, and the record provides the implementation.

This works because records support `implements` even though they cannot use `extends`. Define an interface, let the record implement it, and the array pattern is identical.

```java
interface Measurable {
    String name();
    double measure();
    String unit();
}
```

Now write one implementation as a full class and one as a record:

```java
// Full class — mutable, could be extended
public class Thermometer implements Measurable {
    private String name;
    private double celsius;

    public Thermometer(String name, double celsius) {
        this.name = name;
        this.celsius = celsius;
    }

    @Override public String name()    { return name; }
    @Override public double measure() { return celsius; }
    @Override public String unit()    { return "°C"; }
}

// Record — immutable, one line
record Accelerometer(String name, double measure, String unit) implements Measurable { }
```

Both fit into the same `Measurable[]`:

```java
Measurable[] sensors = {
    new Thermometer("Intake Temp", 38.5),
    new Accelerometer("X-Axis Accel", 2.1, "m/s²"),
    new Thermometer("Motor Temp", 71.2),
    new Accelerometer("Y-Axis Accel", -0.4, "m/s²")
};

for (Measurable m : sensors) {
    System.out.printf("%-20s  %.2f %s%n", m.name(), m.measure(), m.unit());
}
```

```
Intake Temp           38.50 °C
X-Axis Accel          2.10 m/s²
Motor Temp            71.20 °C
Y-Axis Accel         -0.40 m/s²
```

The loop does not care whether an element is a class or a record. Polymorphism works the same way — the interface is the contract, the concrete type is an implementation detail.

### What `instanceof` looks like with records

If you do need to branch on type, `instanceof` works with records identically:

```java
for (Measurable m : sensors) {
    if (m instanceof Accelerometer a) {   // pattern variable (Java 16+)
        System.out.println(a.name() + " is an accelerometer reading");
    }
}
```

Java 16+ allows the pattern variable `a` to be declared directly in the `instanceof` check, eliminating the manual cast. This works for records and regular classes alike.

---

## Exercise

1. Using the `Shape` hierarchy from Module 23, create a `Shape[]` containing at least two of each subclass. Write a loop that prints each shape's info, then separately prints the total area and the total perimeter of all shapes combined.
2. Add a `getColor()` method to `Circle` only (not on `Shape`). Create a mixed `Shape[]`, and for each element print the shape's info. When the element is a `Circle`, also print its color using `instanceof` and a downcast.
3. Redesign exercise 2: instead of using `instanceof`, add a `describe()` method to the `Shape` abstract class. Each subclass overrides it to include any extra information specific to that shape. Rewrite the loop to call `describe()` polymorphically without any `instanceof`. Compare the two approaches.
4. Define a `Readable` interface with a single method `String read()`. Implement it with a regular class `FileSource(String path)` and a record `InlineSource(String content)`. Build a `Readable[]` mixing both types and call `read()` polymorphically in a loop.
