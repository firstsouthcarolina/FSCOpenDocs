# Simple Classes

A class is a blueprint. An object is a specific thing built from that blueprint. This module focuses on designing a clean, self-contained class — the fields, constructors, and methods that make up a working type. The two modules that follow drill into how object variables behave at runtime ([Module 17](../17-reference-semantics/index.md)) and the more advanced field patterns that build on this foundation ([Module 18](../18-field-modifiers-composition/index.md)).

---

## Class vs. Object

A `class` defines what a thing *is* — its data and behaviour.  
An *object* is a specific instance of that class.

```java
// The class is the blueprint:
public class Dog {
    // fields, constructors, methods here
}

// Objects are created from the blueprint:
Dog rex = new Dog("Rex", 4);
Dog buddy = new Dog("Buddy", 2);
```

`rex` and `buddy` are two separate objects. Each has its own copy of the data, but both follow the same `Dog` blueprint.

---

## A Complete Class Example

```java
public class Dog {

    // --- Fields (instance variables) ---
    private String name;
    private int age;

    // --- Constructors ---

    public Dog() {
        this.name = "Unknown";
        this.age = 0;
    }

    public Dog(String name, int age) {
        this.name = name;
        this.age = (age >= 0) ? age : 0;
    }

    // --- Getters (accessors) ---

    public String getName() { return name; }
    public int getAge()     { return age; }

    // --- Setters (mutators) ---

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        if (age >= 0) this.age = age;
    }

    // --- Special methods ---

    @Override
    public String toString() {
        return "Dog[name=" + name + ", age=" + age + "]";
    }

    @Override
    public boolean equals(Object obj) {
        if (!(obj instanceof Dog)) return false;
        Dog other = (Dog) obj;
        return this.name.equals(other.name) && this.age == other.age;
    }
}
```

The rest of this module walks through every part of this class.

---

## Fields (Instance Variables)

Fields are the **data** that every object of this class holds. Each object gets its own independent copy.

```java
private String name;
private int age;
```

- `private` — cannot be accessed from outside the class directly
- Declared at the class level, not inside any method
- Each `new Dog(...)` creates a fresh `name` and `age` belonging to that one object

The `private` keyword is **encapsulation** — covered in detail in [Module 20](../20-encapsulation/index.md). For now, just know it means "outside code cannot touch this directly."

---

## Constructors

A constructor runs when you create an object with `new`. It has the same name as the class and no return type.

```java
Dog d1 = new Dog();              // calls the default (no-arg) constructor
Dog d2 = new Dog("Rex", 4);      // calls the parameterized constructor
```

**Default constructor:** sets fields to reasonable initial values.  
**Parameterized constructor:** accepts values so you can build a fully initialized object in one line.

A class can have multiple constructors as long as they take different parameter lists — this is **method overloading** applied to constructors.

### The `this` Keyword

`this` refers to the current object — the specific instance the method is operating on. It is most commonly used to distinguish a field from a parameter with the same name:

```java
public Dog(String name, int age) {
    this.name = name;   // this.name = the field; name = the parameter
    this.age = age;
}
```

Without `this.`, the assignment `name = name` would just assign the parameter to itself — the field would never be set.

### Constructor Chaining with `this()`

One constructor can call another constructor in the same class using `this(...)`. This avoids duplicating initialization logic.

```java
public class Dog {
    private String name;
    private int age;

    public Dog() {
        this("Unknown", 0);   // calls the two-parameter constructor below
    }

    public Dog(String name, int age) {
        this.name = (name != null && !name.isEmpty()) ? name : "Unknown";
        this.age  = (age >= 0) ? age : 0;
    }
}
```

The `this(...)` call must be the **first statement** in the constructor body. Only one other constructor is called — chaining more than two is possible but rare.

---

## Getters and Setters

Because fields are `private`, outside code cannot read or write them directly. Getters and setters provide controlled access.

```java
// Getter — returns the field value
public String getName() {
    return name;
}

// Setter — validates before allowing a change
public void setAge(int age) {
    if (age >= 0) {
        this.age = age;
    }
    // if age is negative, the field is simply not changed
}
```

The setter validates input before applying it. This is the key benefit of keeping fields `private` — the class controls what values are allowed.

The naming convention is strict: `getX()` to read field `x`, `setX(value)` to change it. IDEs, frameworks, and serialization libraries all rely on this pattern.

---

## `toString()`

`toString()` returns a human-readable String representing the object. Java calls it automatically in string concatenation and `println`.

```java
Dog rex = new Dog("Rex", 4);
System.out.println(rex);                    // Dog[name=Rex, age=4]
System.out.println("My dog: " + rex);       // My dog: Dog[name=Rex, age=4]
```

Without overriding `toString()`, you get something like `Dog@1b6d3586` — a memory address, which is not useful.

A common format is `ClassName[field1=value1, field2=value2, ...]`, but anything readable works. Override `toString()` on every class you write — it makes debugging dramatically easier.

---

## `equals()`

`==` on objects compares memory addresses, not content. Override `equals()` to compare fields instead.

```java
Dog a = new Dog("Rex", 4);
Dog b = new Dog("Rex", 4);

System.out.println(a == b);        // false — different objects in memory
System.out.println(a.equals(b));   // true  — same name and age
```

The standard skeleton:

```java
@Override
public boolean equals(Object obj) {
    if (!(obj instanceof Dog)) return false;
    Dog other = (Dog) obj;
    return this.name.equals(other.name) && this.age == other.age;
}
```

The `instanceof` check rejects `null` and any non-`Dog` argument. The cast is then safe. Compare each significant field, using `.equals()` for `String` and `==` for primitives.

When you override `equals()`, you should also override `hashCode()` — the rules for that are covered when you start using objects as map keys in [Module 33](../33-maps/index.md).

---

## The Tester Class

Keep your class definition in one file and your `main` testing code in a separate file. This is the standard pattern:

```java
// DogTester.java
public class DogTester {
    public static void main(String[] args) {
        // Create objects
        Dog d1 = new Dog();
        Dog d2 = new Dog("Rex", 4);
        Dog d3 = new Dog("Rex", 4);

        // Test toString
        System.out.println(d1);   // Dog[name=Unknown, age=0]
        System.out.println(d2);   // Dog[name=Rex, age=4]

        // Test getters
        System.out.println(d2.getName());   // Rex
        System.out.println(d2.getAge());    // 4

        // Test setters with valid and invalid input
        d1.setName("Buddy");
        d1.setAge(2);
        d1.setAge(-5);               // should be ignored
        System.out.println(d1);      // Dog[name=Buddy, age=2]

        // Test equals
        System.out.println(d2.equals(d3));   // true
        System.out.println(d2.equals(d1));   // false
    }
}
```

The tester class is not part of `Dog`'s public API — it exists only to exercise the class while you build it.

Documenting a class formally — class-level and method-level Javadoc, with a worked example on this kind of class — is covered in [Module 16](../16-javadoc/index.md).

---

## Exercise

Design a `Robot` class with the following specification:

**Fields:**
- `name` (`private String`, set in constructor)
- `teamNumber` (`private int`, set in constructor)
- `maxSpeed` (`private double`, default `0.0` — can be updated, must be non-negative)

**Methods:**
- Default constructor
- Parameterized constructor (`name`, `teamNumber`, `maxSpeed`) with validation on `maxSpeed`
- Use constructor chaining: have the default constructor call the parameterized one with `"Unknown"`, `0`, `0.0`
- Getters for all fields
- Setter for `maxSpeed` (reject negative speeds)
- `toString()` that returns something like `Robot[name=Titan, team=1234, maxSpeed=4.5]`
- `equals()` that compares `name` and `teamNumber`

Write a `RobotTester` class that creates several `Robot` objects, attempts an invalid `maxSpeed`, and demonstrates `toString()` and `equals()`.

Keep this `Robot` class on hand — [Module 16](../16-javadoc/index.md) reuses it for the Javadoc exercises.
