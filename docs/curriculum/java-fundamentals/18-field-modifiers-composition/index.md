# Field Modifiers and Composition

Now that you can write a basic class ([Module 15](../15-simple-classes/index.md)) and understand how object variables behave ([Module 17](../17-reference-semantics/index.md)), this module covers three patterns that build on top:

- **`private final` fields** — locking a value at construction time
- **Static fields** — data that belongs to the class itself, shared across all instances
- **Composition** — building complex objects out of simpler ones

These are not advanced topics — you'll use all three constantly in real code, including every WPILib subsystem.

---

## `private final` Fields

Adding `final` to a field means it can only be assigned **once** — either at the declaration site or in the constructor. After that, any attempt to reassign it is a compile error.

```java
public class Sensor {
    private final int port;       // set once in the constructor, never changed
    private double lastReading;   // changes over time — no final

    public Sensor(int port) {
        this.port = port;         // only valid assignment
    }

    public void update(double value) {
        this.port = 2;            // compile error — cannot reassign a final field
        this.lastReading = value; // fine
    }
}
```

Use `private final` for any field whose value is fixed at construction time and should never change — hardware ports, IDs, and injected dependencies are the clearest examples. It prevents accidental reassignment and signals to anyone reading the code that this value is stable.

This is different from the `final` local constants in [Module 02](../02-primitive-types/index.md#constants-with-final), which are method-scoped. `private final` fields are **object-scoped**: each instance has its own copy, set when the object is created.

```java
// Common pattern — store a collaborating object as a final field
public class Robot {
    private final Sensor distanceSensor;

    public Robot(Sensor distanceSensor) {
        this.distanceSensor = distanceSensor;   // injected, then locked
    }
}
```

### `final` Doesn't Make Objects Immutable

A `final` reference cannot be **reassigned**, but the object it points to can still be mutated:

```java
public class Inventory {
    private final List<String> items = new ArrayList<>();

    public void addItem(String s) {
        items.add(s);          // fine — modifying the list, not reassigning items
    }

    public void replace() {
        items = new ArrayList<>();   // compile error — items is final
    }
}
```

`final` locks the reference, not the contents. Truly immutable values require a [`record`](../25-records/index.md) or careful design where every field is itself unchangeable.

---

## Static Fields

A `static` field belongs to the **class**, not to any individual object. All instances share the same value. Changing it through one instance changes it for all of them.

```java
public class Dog {
    private static int count = 0;   // shared across all Dog objects
    private String name;

    public Dog(String name) {
        this.name = name;
        count++;                    // every new Dog increments the shared counter
    }

    public static int getCount() { return count; }
}
```

```java
Dog a = new Dog("Rex");
Dog b = new Dog("Buddy");
Dog c = new Dog("Max");

System.out.println(Dog.getCount());   // 3 — not 1
```

Notice the call: `Dog.getCount()` — accessed through the class name, not an instance. Static methods and fields are always reachable that way.

### When to Use Static Fields

- **Counters** — how many `Dog` objects exist?
- **Shared configuration** — a feature flag or singleton resource that all instances read
- **Constants** — `public static final` values like `Math.PI`

```java
public class CircleMath {
    public static final double PI = 3.141592653589793;
    public static final double TAU = 2 * PI;
}

// usage:
double area = CircleMath.PI * r * r;
```

### When NOT to Use Static Fields

Avoid `static` for **mutable shared state** between objects unless you have a specific reason. It is essentially a global variable, with all the testing and concurrency hazards that come with global state.

```java
// Anti-pattern: shared mutable state masquerading as a class
public class GameState {
    public static int score = 0;     // anyone, anywhere can change this
    public static String player;
}
```

Prefer passing state explicitly between objects (composition, below) over hiding it in `static` fields.

### Static vs Instance — A Side-by-Side

```java
public class Counter {
    private static int totalCount = 0;   // one for the class
    private int myCount = 0;             // one per instance

    public void tick() {
        totalCount++;
        myCount++;
    }

    public int getMyCount() { return myCount; }
    public static int getTotalCount() { return totalCount; }
}
```

```java
Counter a = new Counter();
Counter b = new Counter();

a.tick(); a.tick(); a.tick();
b.tick();

System.out.println(a.getMyCount());        // 3
System.out.println(b.getMyCount());        // 1
System.out.println(Counter.getTotalCount()); // 4 — total across all counters
```

---

## Composition — Objects Containing Objects

A field in a class can be another object type, not just a primitive or `String`. This is called **composition** (a "has-a" relationship).

```java
public class Wheel {
    private double diameter;
    public Wheel(double diameter) { this.diameter = diameter; }
    public double getDiameter() { return diameter; }
    @Override
    public String toString() { return "Wheel(diameter=" + diameter + ")"; }
}

public class Bicycle {
    private String make;
    private Wheel frontWheel;
    private Wheel backWheel;

    public Bicycle(String make, double wheelDiameter) {
        this.make = make;
        this.frontWheel = new Wheel(wheelDiameter);
        this.backWheel = new Wheel(wheelDiameter);
    }

    @Override
    public String toString() {
        return make + " | Front: " + frontWheel + " | Back: " + backWheel;
    }
}
```

```java
Bicycle bike = new Bicycle("Trek", 27.5);
System.out.println(bike);
// Trek | Front: Wheel(diameter=27.5) | Back: Wheel(diameter=27.5)
```

### "Has-a" vs "Is-a"

Composition models a "has-a" relationship — a `Bicycle` *has a* `Wheel`. Inheritance ([Module 21](../21-inheritance/index.md)) models "is-a" — a `Dog` *is an* `Animal`.

| | Composition | Inheritance |
|--|-------------|-------------|
| Relationship | "has-a" | "is-a" |
| Mechanism | Field of another type | `extends` keyword |
| Reuse | Delegate to the contained object | Inherit methods directly |
| Flexibility | Can swap the contained object at runtime | Hierarchy fixed at compile time |
| Coupling | Loose — the outer class only depends on the inner class's API | Tight — subclass depends on parent's structure |

A frequent design rule of thumb: **prefer composition over inheritance** unless the "is-a" relationship is genuine. A `Truck` is genuinely a `Vehicle`. A `Bicycle` is not really a kind of `Wheel`, so it should *contain* wheels rather than extend `Wheel`.

### Composition + `private final`

A common WPILib pattern: a robot subsystem stores hardware as `private final` collaborators that are passed in or constructed at startup, then never replaced:

```java
public class DriveSubsystem {
    private final SparkMax leftMotor;
    private final SparkMax rightMotor;
    private final DifferentialDrive drive;

    public DriveSubsystem(SparkMax left, SparkMax right) {
        this.leftMotor = left;
        this.rightMotor = right;
        this.drive = new DifferentialDrive(leftMotor::set, rightMotor::set);
    }
}
```

The subsystem `has a` left motor, `has a` right motor, and `has a` drive controller. Each one is constructed once, locked with `final`, and exposed only through methods.

---

## Exercise

1. Modify the `Sensor` class so its `port` is `private final` and set in the constructor. Add a `lastReading` field that *can* change. In a tester, create the sensor, call `update()` a few times, and verify the readings changed but `port` did not. Then try to add a setter for `port` and confirm the compile error.

2. Add a `private static int instanceCount` to a class of your choice. Increment it in the constructor. Add a `public static int getInstanceCount()`. Construct several instances and confirm the count works. What happens to the count if you set instances to `null` after creation? (Hint: there's no destructor — the count never goes down.)

3. Create a `public static final double GRAVITY = 9.81;` constant in a `Physics` class. Use it from a `FreeFall.distance(double seconds)` method. Try to reassign `Physics.GRAVITY` and confirm it is a compile error.

4. Design a `Car` class using composition: it should have an `Engine` (with horsepower and fuel type fields), four `Wheel` objects, and a `String make`. Create the constructor, a `toString()` that delegates to each component's `toString()`, and a tester that builds and prints a few cars. Make every field `private final`.

5. Pick one class you wrote earlier and add a `private final` field of another class type (composition). Demonstrate that the contained object can still have its state mutated through method calls, even though the outer field is `final`.
