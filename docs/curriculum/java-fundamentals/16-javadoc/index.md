# Javadoc and Documentation

Documentation is part of the code. Javadoc is Java's standard system for writing documentation that IDEs can display as tooltips, and that the `javadoc` tool can render into browsable HTML — the same format used by the official Java API docs.

You have now written methods (Module 10) and complete classes (Module 15). This module is how you document both — and why every public API in the standard library has Javadoc on every method and every class.

---

## Preconditions and Postconditions

Before the syntax: the *content* of good documentation. A method's contract has two halves.

A **precondition** is a condition that must be true before a method is called — it is a promise the caller makes. A **postcondition** is a condition that is guaranteed to be true after the method returns — it is a promise the method makes.

```java
// Precondition:  a and b are valid doubles (no NaN or Infinity)
// Postcondition: returns a value between min(a,b) and max(a,b) inclusive
public static double average(double a, double b) {
    return (a + b) / 2.0;
}
```

Preconditions and postconditions are a **method contract** — they document what inputs are acceptable and what output is guaranteed. You do not enforce a precondition inside the method (that is the caller's job), but documenting it prevents bugs at the call site.

Classes have contracts too — usually expressed as **invariants**, conditions that are always true about an instance. For example, a `BankAccount` has the invariant *balance ≥ 0*; the constructor establishes it, and every method preserves it.

---

## The Javadoc Format

Javadoc uses `/** */` (double-star opening) block comments placed **directly above** the item being documented:

```java
/**
 * Converts a temperature from Celsius to Fahrenheit.
 *
 * @param celsius  the temperature in degrees Celsius
 * @return         the equivalent temperature in degrees Fahrenheit
 */
public static double celsiusToFahrenheit(double celsius) {
    return celsius * 9.0 / 5.0 + 32;
}
```

The first sentence is the **summary sentence** — it appears in the overview table when the HTML is generated. Subsequent lines add detail.

---

## Tags

Javadoc tags fall into two groups: those that document a method's behaviour, and those that document a class's metadata.

### Method-level tags

| Tag | Purpose |
|-----|---------|
| `@param name description` | Documents one parameter; repeat for each parameter |
| `@return description` | Documents the return value; omit for `void` methods |
| `@throws ExceptionType description` | Documents an exception the method may throw |

### Class-level tags

| Tag | Purpose |
|-----|---------|
| `@author name` | Who wrote the class |
| `@version string` | Version string |
| `@since string` | When the class/method was added |
| `@deprecated reason` | Marks an item as obsolete; IDEs show a strikethrough |
| `@see reference` | Cross-reference to another class or method |

Class-level tags belong in the class's Javadoc block — not on individual methods. `@deprecated` is the exception: it can apply to either a class or a single method.

---

## Inline Tags

Inline tags appear inside the description text, surrounded by `{` and `}`:

| Tag | Purpose | Example |
|-----|---------|---------|
| `{@code expression}` | Renders as code (monospace, no HTML processing) | `{@code null}` |
| `{@link ClassName#method}` | Clickable link to another API element | `{@link Math#sqrt(double)}` |
| `{@literal text}` | Renders literally (like `{@code}` but without monospace) | `{@literal <T>}` |

```java
/**
 * Returns the absolute value of {@code n}.
 * Uses the same logic as {@link Math#abs(int)}.
 *
 * @param n  any integer
 * @return   {@code n} if {@code n >= 0}, otherwise {@code -n}
 */
public static int myAbs(int n) {
    return n >= 0 ? n : -n;
}
```

---

## Documenting a Complete Class

Class-level Javadoc goes directly above the `class` declaration. Every public constructor and method gets its own Javadoc block underneath. Here is a fully documented `Point` class — a complete worked example:

```java
/**
 * Represents a 2D point in Cartesian coordinates.
 *
 * <p>Coordinates are stored as {@code double} values.
 * All distance calculations use standard Euclidean geometry.
 *
 * @author FSC Robotics
 * @version 1.0
 */
public class Point {

    private double x;
    private double y;

    /**
     * Creates a point at the given coordinates.
     *
     * @param x  the horizontal position
     * @param y  the vertical position
     */
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    /**
     * Returns the horizontal coordinate.
     *
     * @return the x value
     */
    public double getX() { return x; }

    /**
     * Returns the vertical coordinate.
     *
     * @return the y value
     */
    public double getY() { return y; }

    /**
     * Returns the Euclidean distance from this point to another.
     *
     * @param other  the target point; must not be {@code null}
     * @return       distance in the same units as the coordinates
     * @throws NullPointerException if {@code other} is {@code null}
     */
    public double distanceTo(Point other) {
        double dx = this.x - other.x;
        double dy = this.y - other.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}
```

A few patterns worth noting:

- The class block has `@author` and `@version`. Method blocks do not — those tags only make sense at the class level.
- The class summary is followed by a `<p>` paragraph break and additional context. Multi-paragraph class Javadoc is normal.
- `getX()` and `getY()` get short blocks. Trivial accessors are sometimes left undocumented; here they are documented for completeness.
- `distanceTo` documents `null` as part of the precondition and pairs it with `@throws NullPointerException`. The contract is explicit on both sides.

---

## Preconditions in Javadoc

The conventional place for preconditions and postconditions is inside the Javadoc block. Use `@throws` for the failure case and free-text lines for the human-readable contract:

```java
/**
 * Returns the element at the given index.
 *
 * @param index  the position to read
 * @return       the element at {@code index}
 * @throws ArrayIndexOutOfBoundsException if {@code index < 0} or
 *         {@code index >= size()}
 *
 * Precondition:  {@code 0 <= index && index < size()}
 * Postcondition: the array is unchanged; returns the stored value
 */
public int get(int index) {
    return data[index];
}
```

`@throws` is the formal documentation; the free-text `Precondition` / `Postcondition` lines describe the contract in plain English.

---

## IDE Integration

IDEs read Javadoc from source files and display it as tooltips. When you hover over any method call, the summary sentence, parameter descriptions, and return description appear inline — no need to open the file.

This is why Javadoc matters even for code you will never publish: your teammates (and your future self) see the documentation without leaving the call site. A method whose name and parameters are clear can still benefit from a Javadoc summary explaining *why* it exists or what it guarantees.

---

## Generating HTML Docs

The `javadoc` command-line tool ships with the JDK and converts Javadoc comments into the same browsable HTML format used by the official Java API:

```
javadoc -d docs src/*.java
```

- `-d docs` — output directory
- `src/*.java` — source files to document

The output is a self-contained set of HTML pages. IDEs can also generate Javadoc HTML through their built-in menus.

---

## What Deserves a Javadoc Comment?

| Item | Write Javadoc? |
|------|---------------|
| Public class | Yes — always |
| Public method | Yes — always |
| Public field (rare) | Yes if not obvious from the name |
| Private/package-private method | Optional — team preference |
| `main` method | Usually not — it is the entry point |
| Trivial getter/setter | Often skipped if the field name is self-explanatory |

Within a class, the order is conventionally: class Javadoc → field declarations → constructors → methods. Each public item gets its own block.

---

## Exercise

1. Take the `Robot` class you wrote at the end of [Module 15](../15-simple-classes/index.md). Add a class-level Javadoc block with `@author` and `@version`. Then add a method-level Javadoc block to every public constructor and method, using `@param` and `@return` where appropriate.

2. Write three static methods — `double circlePerimeter(double radius)`, `boolean isEven(int n)`, and `int max(int a, int b, int c)` — and document each fully with a summary sentence, `@param`, and `@return`. For `circlePerimeter`, add a precondition note for the case where `radius` is negative.

3. Write a method `int clamp(int value, int min, int max)` that returns `value` clamped to the range `[min, max]`. Document it with Javadoc including a `@throws` tag for the case where `min > max`.

4. Add `@deprecated` Javadoc to a method `void oldPrint(String s)` that explains it was replaced by a `void print(String s, String prefix)` overload. Add a `@see` link pointing to the replacement.

5. Generate HTML docs for one of your classes using the `javadoc` command-line tool (or your IDE's built-in option). Open the result in a browser and navigate it. Confirm the summary sentences, parameter tables, and links work as expected.
