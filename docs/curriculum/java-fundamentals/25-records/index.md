# Records

A **record** is a special-purpose class designed for one job: holding immutable data. It generates the constructor, accessors, `equals`, `hashCode`, and `toString` automatically — eliminating the boilerplate you wrote by hand in [Module 15](../15-simple-classes/index.md).

Records were introduced in Java 16 (stable).

---

## The Problem Records Solve

Every time you write a class that just carries data, you end up writing the same boilerplate:

```java
// Full class — 20 lines for three fields
public class Point {
    private final double x;
    private final double y;
    private final String label;

    public Point(double x, double y, String label) {
        this.x = x;
        this.y = y;
        this.label = label;
    }

    public double x()      { return x; }
    public double y()      { return y; }
    public String label()  { return label; }

    @Override public boolean equals(Object o) { /* ... */ }
    @Override public int hashCode()           { /* ... */ }
    @Override public String toString()        { /* ... */ }
}
```

A record replaces all of it with one line:

```java
record Point(double x, double y, String label) { }
```

---

## What a Record Auto-Generates

Given `record Point(double x, double y, String label) { }`, Java creates:

| Auto-generated | What it does |
|----------------|-------------|
| `Point(double x, double y, String label)` | Canonical constructor — sets all fields |
| `double x()` | Accessor for `x` |
| `double y()` | Accessor for `y` |
| `String label()` | Accessor for `label` |
| `boolean equals(Object o)` | True if all fields are equal |
| `int hashCode()` | Consistent with `equals` |
| `String toString()` | `"Point[x=1.0, y=2.0, label=A]"` |

All fields are `private final` — you cannot change them after construction.

---

## Creating and Using Records

```java
record Point(double x, double y, String label) { }

public class Main {
    public static void main(String[] args) {
        Point a = new Point(3.0, 4.0, "A");
        Point b = new Point(3.0, 4.0, "A");

        System.out.println(a.x());          // 3.0
        System.out.println(a.label());      // A
        System.out.println(a);              // Point[x=3.0, y=4.0, label=A]
        System.out.println(a.equals(b));    // true — structural equality
        System.out.println(a == b);         // false — different objects
    }
}
```

Note the accessor naming: `a.x()`, not `a.getX()`. Records use the field name directly.

---

## Compact Constructors

A **compact constructor** runs additional logic (validation, normalization) inside the canonical constructor. It has no parameter list — it implicitly receives and assigns all the fields:

```java
record Point(double x, double y, String label) {
    Point {
        if (Double.isNaN(x) || Double.isNaN(y))
            throw new IllegalArgumentException("Coordinates cannot be NaN");
        label = label.trim();   // normalize before assignment
    }
}
```

The compact constructor body runs before the fields are assigned. Assignments inside it override the incoming values.

---

## Adding Methods

Records can have additional methods. They cannot add new fields.

```java
record Point(double x, double y) {

    public double distanceTo(Point other) {
        double dx = this.x - other.x;
        double dy = this.y - other.y;
        return Math.sqrt(dx * dx + dy * dy);
    }

    public Point translate(double dx, double dy) {
        return new Point(x + dx, y + dy);   // returns a new record — fields are final
    }
}
```

Because fields are immutable, "modifying" a record means returning a new one with updated values.

---

## Records and Interfaces

Records can implement interfaces — this is where they connect to Module 24.

```java
interface Describable {
    String describe();
}

record Sensor(String name, double value, String unit) implements Describable {
    @Override
    public String describe() {
        return name + ": " + value + " " + unit;
    }
}
```

```java
Sensor gyro = new Sensor("Gyro", 92.4, "deg");
System.out.println(gyro.describe());   // Gyro: 92.4 deg
System.out.println(gyro);             // Sensor[name=Gyro, value=92.4, unit=deg]
```

A record that implements `Comparable<T>` can be sorted by `Arrays.sort` and `Collections.sort` just like a class:

```java
record Score(String player, int points) implements Comparable<Score> {
    @Override
    public int compareTo(Score other) {
        return Integer.compare(other.points, this.points);   // descending
    }
}

Score[] scores = {
    new Score("Alice", 82),
    new Score("Bob", 95),
    new Score("Carol", 78)
};

Arrays.sort(scores);
for (Score s : scores) System.out.println(s.player() + " — " + s.points());
// Bob — 95
// Alice — 82
// Carol — 78
```

---

## Records vs. Classes

| | Record | Class |
|--|--------|-------|
| Fields | `private final` — always immutable | Any mutability |
| Constructor | Auto-generated (canonical) | Written manually |
| Accessors | Auto-generated as `x()` | Written manually as `getX()` |
| `equals` / `hashCode` / `toString` | Auto-generated | Written manually |
| Extend another class | No — records extend `java.lang.Record` | Yes |
| Implement interfaces | Yes | Yes |
| Can be subclassed | No — records are `final` | Yes (unless `final`) |
| Mutable state | No | Yes |

---

## When to Use a Record

Use a record when:

- The class's only job is **holding data** (a data transfer object, a coordinate, a measurement, a result pair)
- **Immutability** is the right default — you don't want the values changed after construction
- You want `equals` based on **field values**, not object identity

Use a full class when:

- The object needs **mutable state** (fields that change over time)
- The class needs to **extend another class** (records can't)
- The object has **complex lifecycle behavior** beyond data access
- You need a class others can **subclass**

In robotics: sensor readings, waypoints, configuration snapshots, and scored game pieces are natural records. Subsystems, commands, and motor controllers are not — they are stateful and need inheritance.

---

## Exercise

1. Write a record `Waypoint(double x, double y, String name)` with a compact constructor that rejects negative coordinates. Create three waypoints and print them.

2. Write a record `SensorReading(String sensor, double value, long timestamp)` that implements `Comparable<SensorReading>` sorted by `timestamp` ascending. Create an array of five readings (with different timestamps), sort it with `Arrays.sort`, and print them in order.

3. Write an interface `Scoreable` with a method `int score()`. Write a record `GamePiece(String type, int baseValue, boolean inZone)` that implements `Scoreable` — `score()` returns `baseValue * 2` if `inZone`, otherwise `baseValue`. Create a `GamePiece[]` and use a stream (or loop) to find the total score.
