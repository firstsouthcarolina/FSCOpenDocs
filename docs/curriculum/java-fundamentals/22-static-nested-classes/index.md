# Static Nested Classes

A class can be declared inside another class. When marked `static`, it has no implicit link to the enclosing instance — it is just a class that happens to live inside another class's namespace. This is a small, self-contained tool for keeping closely related types together without spawning extra files.

---

## Why This Exists — Connecting to What You Already Know

In [Module 18](../18-field-modifiers-composition/index.md) you learned **composition**: a class can hold another class as a field (`Bicycle` has a `Wheel`). Each type lived in its own file, visible to anyone in the project.

In [Module 20](../20-encapsulation/index.md) you learned that `private` fields hide implementation details from outside code. The same instinct applies at the type level: if a helper class only makes sense *inside one other class*, there is no reason to give it its own file — doing so suggests it is a general-purpose type when it is not.

A `static` nested class is how you express that. It is still a full class, but it lives inside the outer class's namespace, and its visibility can be restricted to just that outer class with `private`.

```java
// Without a nested class — Node is a top-level file anyone can use
public class Node { int data; Node next; }   // nothing stops other code from importing this

// With a private static nested class — Node is an implementation detail of LinkedList only
public class LinkedList {
    private static class Node { int data; Node next; }   // invisible outside LinkedList
}
```

You will see this exact pattern when you build a linked list in [Module 37](../37-linked-lists/index.md), a stack in [Module 38](../38-stacks/index.md), and a queue in [Module 39](../39-queues/index.md). Every one of those data structures uses a `private static class Node` that exists only to serve the outer class. Learning the tool now means the code in those modules will not surprise you.

The `static` keyword carries the same meaning it did in [Module 18](../18-field-modifiers-composition/index.md): no link to a specific enclosing *instance*. A static nested class can be created without first creating the outer class, and it cannot reach the outer class's instance fields — it only has access to `static` members of the enclosing class.

---

## Declaration and Use

```java
public class DriveState {

    public static class Measurement {
        public final double distance;
        public final double heading;

        public Measurement(double distance, double heading) {
            this.distance = distance;
            this.heading  = heading;
        }
    }
}
```

The nested class is referenced through its enclosing class:

```java
DriveState.Measurement m = new DriveState.Measurement(1.5, 45.0);
System.out.println(m.distance);   // 1.5
```

You can `import` the nested class directly to shorten references:

```java
import frc.robot.DriveState.Measurement;

Measurement m = new Measurement(1.5, 45.0);
```

---

## Why `static`?

Without the `static` keyword, a nested class is an **inner class** — it carries an implicit reference to the enclosing object and can only exist alongside an instance of the enclosing class. That coupling is rarely what you want and adds memory overhead.

A `static` nested class:

- Does **not** hold a reference to an enclosing instance
- Can be instantiated without an enclosing object: `new DriveState.Measurement(...)`
- Can only access `static` members of the enclosing class
- Behaves like a top-level class in every other respect

For nearly every use case, you want `static`. Inner classes (non-static nested) are an advanced feature and are not used in this curriculum.

---

## When to Use

Use a static nested class when:

- The nested type is **only meaningful in the context of the enclosing class** — it is conceptually a part of the parent.
- You want to **keep the file count down** for small data-holders that don't deserve their own file.
- The nested class **does not need access** to the enclosing object's instance fields.

Common cases:

- A small data-bundle returned from a method (`PathPlanner.Trajectory`, `DriveState.Measurement`)
- A `Builder` for the enclosing class
- A specialized exception type tied to one class

For anything more than a small data-holder, prefer a separate top-level file. A 200-line nested class buried inside another file is hard to find and harder to read.

---

## Static Nested vs. Top-Level — Comparison

| | Static nested | Top-level |
|--|--------------|-----------|
| File | Lives inside another file | Its own file |
| Reference | `Outer.Nested` | `Nested` |
| Best for | Small types tightly coupled to one parent | Anything reusable across the codebase |
| Encapsulation | Can be `private` to the enclosing class | Always visible at package level (or wider) |

A `private static` nested class is invisible outside the enclosing class entirely — useful for implementation details that no one else should depend on:

```java
public class LinkedQueue {
    private static class Node {   // only LinkedQueue can see this
        int data;
        Node next;
    }
}
```

This pattern shows up throughout the data structures in [Module 37](../37-linked-lists/index.md) onward.

---

## Records as Nested Types

A [`record`](../25-records/index.md) is even more concise than a nested class for pure data bundles. The static nested class approach above with `distance` and `heading` could just as well be written:

```java
public class DriveState {
    public record Measurement(double distance, double heading) {}
}
```

Records are implicitly `static` when nested. Use a nested record when you need a value object; use a nested static class when you need a builder, helper, or implementation node with mutable state.

---

## Exercise

1. Create a top-level class `RobotPose` with a `public static class Snapshot` that bundles `double x`, `double y`, and `double headingDegrees` as `public final` fields. Add a constructor and a `toString()` that formats the snapshot. Instantiate one as `new RobotPose.Snapshot(1.0, 2.0, 90.0)` and print it.

2. Convert your `Snapshot` from exercise 1 into a nested `record` instead. Confirm the constructor and accessors work the same way. (Note: record accessors are `x()`, `y()`, `headingDegrees()` — not `getX()`.)

3. Create a class `LinkedStack` with a `private static class Node` containing `int data` and `Node next`. Add basic `push(int)` and `pop()` methods that use the nested `Node` type. Confirm that code outside `LinkedStack` cannot reference `Node` directly.

4. Explain in your own words why a `Node` class used internally by a list or stack should be `private static` rather than top-level. What does encapsulating it that way buy you?
