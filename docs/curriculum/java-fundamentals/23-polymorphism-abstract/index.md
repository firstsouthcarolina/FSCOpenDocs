# Polymorphism and Abstract Classes

A parent-type reference can hold any subclass object, and the correct method is chosen at runtime based on the actual object — not the declared type. Paired with abstract classes, this lets you define *what* behaviour a hierarchy must have without specifying *how* each subclass provides it.

This module builds directly on the `Animal` / `Dog` / `Cat` hierarchy from [Module 21](../21-inheritance/index.md).

---

## Polymorphism

Polymorphism means "many forms." A variable declared as type `Animal` can hold a `Dog`, a `Cat`, or any other subclass. When you call a method on that variable, Java looks at the actual runtime type and calls the right version.

```java
Animal a = new Dog("Rex", "Lab");
a.speak();   // prints "Rex barks!" — not "Rex makes a sound."
```

The declared type (`Animal`) determines what methods are *available*. The actual type (`Dog`) determines which *implementation* runs. This decision at runtime is called **dynamic dispatch**.

### Why This Matters

Without polymorphism, you would need a separate loop for every subtype:

```java
// Without polymorphism — brittle
for (Dog d : dogs)   d.speak();
for (Cat c : cats)   c.speak();
for (Bird b : birds) b.speak();
```

With polymorphism, one loop handles all of them:

```java
// With polymorphism — open to extension
Animal[] animals = { new Dog("Rex", "Lab"), new Cat("Miso"), new Bird("Pip") };

for (Animal a : animals) {
    a.speak();   // correct method chosen at runtime for each element
}
```

Adding a new animal type (`Fish`, `Horse`) only requires writing the new class. The loop never changes.

---

## Abstract Classes

An **abstract class** is a class that cannot be instantiated directly. It exists to be extended.

```java
public abstract class Shape {
    public abstract double area();
    public abstract double perimeter();

    @Override
    public String toString() {
        return String.format("%s | Area: %.2f | Perimeter: %.2f",
            getClass().getSimpleName(), area(), perimeter());
    }
}
```

!!! note "Two elements used here without prior coverage"
    `getClass().getSimpleName()` returns the runtime class name as a `String` — for a `Circle` object it returns `"Circle"`, for a `Rectangle` it returns `"Rectangle"`. This means the parent's `toString()` automatically labels itself correctly for every subclass without hardcoding anything.

    `String.format("%.2f", value)` formats a `double` to two decimal places. Full coverage of `String.format` and `printf` is in [Module 29](../29-file-io/index.md#printf-and-stringformat-for-formatted-output).

```java
```

```java
Shape s = new Shape();   // compile error — Shape is abstract
Shape c = new Circle(5); // fine — Circle is a concrete subclass
```

### Abstract Methods

An abstract method declares a contract without a body. Every non-abstract subclass **must** implement it, or the compiler refuses to build.

```java
public abstract double area();      // no body
public abstract double perimeter(); // subclasses must provide one
```

The `toString()` above is a **concrete** method — the abstract class can share implementation while leaving the rest open.

### When to Use Abstract

Use an abstract class when:

- The base concept has no meaningful standalone implementation (a bare `Shape` has no area formula)
- You want to enforce that all subclasses implement certain methods
- You want to share some implementation (like `toString()`) while leaving others abstract

---

## The Array of Shapes

The Shape hierarchy is the canonical polymorphism example. Three concrete classes all extend the same abstract parent:

```java
public class Circle extends Shape {
    private double radius;

    public Circle(double radius) { this.radius = radius; }

    @Override public double area()      { return Math.PI * radius * radius; }
    @Override public double perimeter() { return 2 * Math.PI * radius; }
}
```

```java
public class Rectangle extends Shape {
    private double width, height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override public double area()      { return width * height; }
    @Override public double perimeter() { return 2 * (width + height); }
}
```

```java
public class Triangle extends Shape {
    private double a, b, c;

    public Triangle(double a, double b, double c) {
        this.a = a; this.b = b; this.c = c;
    }

    @Override public double area() {
        double s = (a + b + c) / 2;   // Heron's formula
        return Math.sqrt(s * (s - a) * (s - b) * (s - c));
    }

    @Override public double perimeter() { return a + b + c; }
}
```

### Driving the Array

```java
public class ShapeTester {
    public static void main(String[] args) {
        Shape[] shapes = {
            new Circle(5),
            new Rectangle(4, 6),
            new Triangle(3, 4, 5),
            new Circle(2),
            new Rectangle(10, 2)
        };

        for (Shape s : shapes) {
            System.out.println(s);   // toString() → area() + perimeter()
        }
    }
}
```

```
Circle    | Area: 78.54 | Perimeter: 31.42
Rectangle | Area: 24.00 | Perimeter: 20.00
Triangle  | Area:  6.00 | Perimeter: 12.00
Circle    | Area: 12.57 | Perimeter: 12.57
Rectangle | Area: 20.00 | Perimeter: 24.00
```

The loop has no `if`/`instanceof` checks. It does not know or care what each `Shape` actually is — it just calls the methods the abstract class guarantees will exist.

---

## Exercise

1. Add a `Square extends Shape`. A square has one side length; area is `side²`, perimeter is `4 × side`. Add it to the `shapes` array in `ShapeTester` and confirm it prints correctly.
2. Add a static method `totalArea(Shape[] shapes)` to `ShapeTester` that returns the sum of all areas. Call it and print the result.
3. Create an `abstract class Appliance` with:
   - abstract method `double powerUsageWatts()`
   - concrete method `double annualKWh()` that returns `powerUsageWatts() * 8760 / 1000.0`
   - abstract `String name()`

   Implement `Microwave` (1000 W) and `LEDLight` (10 W). Create an `Appliance[]` with a few of each and print each appliance's annual kWh in a loop.
4. Revisit the `Animal` / `Dog` / `Cat` / `Bird` hierarchy: declare `speak()` abstract in `Animal`. What changes? Which classes are now forced to provide an implementation?
