# Inheritance

Inheritance lets a new class **build on an existing class** — inheriting its fields and methods while adding new ones or replacing existing ones. It is the second OOP pillar, and it is the foundation for the polymorphism work in [Module 23](../23-polymorphism-abstract/index.md).

The relationship is described as "is-a": a `Dog` *is an* `Animal`, a `Car` *is a* `Vehicle`. Inheritance models that relationship in code so that an `Animal` reference can hold a `Dog`, a `Cat`, or anything else that extends `Animal`.

---

## A Worked Example — Animal / Dog / Cat

```java
public class Animal {
    protected String name;   // protected so subclasses can access it

    public Animal(String name) {
        this.name = name;
    }

    public void speak() {
        System.out.println(name + " makes a sound.");
    }

    public String getName() { return name; }
}
```

```java
public class Dog extends Animal {

    private String breed;

    public Dog(String name, String breed) {
        super(name);   // call the parent constructor — must be the first line
        this.breed = breed;
    }

    @Override
    public void speak() {
        System.out.println(name + " barks!");
    }

    public String getBreed() { return breed; }
}
```

```java
public class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }

    @Override
    public void speak() {
        System.out.println(name + " meows.");
    }
}
```

```java
Dog rex   = new Dog("Rex", "Lab");
Cat miso  = new Cat("Miso");

rex.speak();   // Rex barks!
miso.speak();  // Miso meows.
System.out.println(rex.getName());   // Rex — inherited from Animal
```

`Dog` and `Cat` did not redeclare `getName()` or the `name` field — those were inherited from `Animal`. Each class only adds or replaces what makes it distinct.

---

## Key Keywords

| Keyword | Purpose |
|---------|---------|
| `extends` | Declares the "is-a" relationship — `class Dog extends Animal` |
| `super(...)` | Calls the parent class constructor; must be the first line of the subclass constructor |
| `super.method()` | Calls the parent's version of a method, even after the subclass has overridden it |
| `@Override` | Annotation telling the compiler you intend to override an inherited method |

### `super(...)` — Calling the Parent Constructor

Every constructor in a subclass must run the parent's constructor first, so the parent's fields can be initialized. If you don't call `super(...)` explicitly, Java inserts an implicit `super()` (no-args) call. If the parent has no no-arg constructor, the compiler refuses to build.

```java
public Dog(String name, String breed) {
    super(name);          // run Animal(String name) first
    this.breed = breed;   // then initialize this class's own fields
}
```

### `super.method()` — Reusing Parent Behaviour

If a subclass overrides a method but still wants the parent's logic to run, it can call `super.method()`:

```java
@Override
public void speak() {
    super.speak();   // first run Animal's speak()
    System.out.println(name + " also wags its tail.");
}
```

### `@Override` — Compiler-Checked Overriding

`@Override` is technically optional, but always use it. It tells the compiler "this method is supposed to override one from the parent." If you misspell the method name or get the parameter list wrong, the compiler catches the mistake instead of silently letting you create a brand-new method that never gets called.

```java
@Override
public void speek() { ... }   // compile error — no such method on Animal
```

---

## Rules

- Java supports **single inheritance** only — a class can extend exactly one direct parent. (Multiple *interfaces* are allowed, covered in [Module 24](../24-interfaces/index.md).)
- A subclass inherits all `public` and `protected` members of the parent.
- A subclass **cannot** access `private` members of the parent directly — it must use inherited getters/setters (or the parent must mark the field `protected`).
- Constructors are not inherited. Each class declares its own.
- The chain bottoms out at `java.lang.Object`, which every class extends transitively.

### `protected` vs. `private` — A Design Choice

In the example above, `Animal.name` is `protected` so `Dog` can access it directly. The alternative is to keep it `private` and make `Dog` use `getName()`:

```java
// Alternative — name stays private; subclass uses the getter
public class Dog extends Animal {
    @Override
    public void speak() {
        System.out.println(getName() + " barks!");   // via inherited getter
    }
}
```

`protected` is convenient but loosens encapsulation. Prefer `private` + getters/setters for fields the subclass only needs to read. Use `protected` for fields the subclass must directly mutate, or for helper methods that subclasses are designed to call.

---

## Method Overriding vs. Method Overloading

These sound similar but mean very different things. Keep them distinct:

| | Overriding | Overloading |
|--|-----------|------------|
| Where | Subclass redefines a parent method | Same class, multiple methods with same name |
| Signature | Must match the parent exactly | Must differ in parameters |
| Resolved | At **runtime** (dynamic dispatch) | At **compile time** |
| Annotation | `@Override` | None needed |

```java
// Overloading — same class, same name, different parameters
public class Calculator {
    public int add(int a, int b)         { return a + b; }
    public double add(double a, double b) { return a + b; }
    public int add(int a, int b, int c)  { return a + b + c; }
}

// Overriding — subclass replaces the parent's method
public class LoudDog extends Dog {
    @Override
    public void speak() {
        System.out.println(getName().toUpperCase() + " BARKS!!!");
    }
}
```

Overloading is resolved at compile time: the compiler picks which `add` to call based on the argument types you wrote. Overriding is resolved at runtime: the JVM looks at the actual object's class and picks the right `speak`. The runtime resolution is what makes polymorphism (Module 23) possible.

---

## Building a Hierarchy — Design Tips

- **Start from the data.** Look at what fields are common to all subclasses; those are the parent's job. Fields unique to a subclass belong in that subclass.
- **Don't extend just to reuse code.** Inheritance models "is-a." If `Truck` does not feel like a kind of `Vehicle`, look for composition (a field of another type) instead — covered in [Module 18](../18-field-modifiers-composition/index.md).
- **Keep hierarchies shallow.** Two or three levels deep is normal. Anything deeper is often a sign that the design needs rethinking.
- **Don't override for the sake of overriding.** If the parent's method is fine, leave it alone. Only override when the subclass actually needs different behaviour.

---

## Exercise

1. Create a `Vehicle` class with `private` fields `make` (`String`), `model` (`String`), and `year` (`int`). Add a constructor, getters, and a `setYear` that rejects years before 1885 (the first gasoline automobile). Add `toString()` returning `"year make model"`.
2. Create a `Car extends Vehicle` with an additional `numDoors` field. Use `super(...)` in the constructor. Override `toString()` to append `"(numDoors-door)"` to the parent's output (use `super.toString()` for the parent part).
3. Create a `Truck extends Vehicle` with a `payloadKg` field. Override `toString()` to append `"(payload: Xkg)"`.
4. Extend the `Animal` / `Dog` / `Cat` hierarchy by adding a `Bird` class that overrides `speak()` to print `"[name] tweets."`. Create an `Animal[]` with a mix of dogs, cats, and birds and call `speak()` on each in a loop. Note that the loop knows nothing about the specific subclasses — this is a preview of [Module 23](../23-polymorphism-abstract/index.md).
5. Demonstrate overloading by writing a `Calculator` class with three `add` methods: one taking two `int`s, one taking two `double`s, and one taking three `int`s. Call each from `main` and confirm the right one runs.
