# Conditional Logic

Programs that always do the same thing regardless of input are not very useful. Conditionals let your program make decisions.

This module is the practical Java syntax for conditions: `if`/`else if`/`else`, comparison and logical operators, ternary expressions, and `switch`. The formal mathematical theory behind boolean logic — propositions, implication, inference rules — has its own optional module immediately after this one ([Module 07](../07-propositional-logic/index.md)).

---

## `if` / `else if` / `else`

```java
int score = 82;

if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else if (score >= 70) {
    System.out.println("C");
} else if (score >= 60) {
    System.out.println("D");
} else {
    System.out.println("F");
}
// Output: B
```

- Java evaluates conditions **top-to-bottom** and runs the first block that is `true`
- Once a match is found, all remaining `else if` and `else` branches are skipped
- The `else` block at the end is optional — it runs only if no condition above matched

---

## Comparison Operators

| Operator | Meaning | Example |
|----------|---------|---------|
| `==` | Equal to | `x == 5` |
| `!=` | Not equal to | `x != 0` |
| `<` | Less than | `x < 10` |
| `>` | Greater than | `x > 0` |
| `<=` | Less than or equal to | `x <= 100` |
| `>=` | Greater than or equal to | `x >= 18` |

!!! warning "Assignment `=` vs. comparison `==`"
    `x = 5` assigns the value 5 to `x`. `x == 5` tests whether `x` equals 5. Mixing them up is a common bug.

---

## Logical Operators

Combine multiple conditions:

| Operator | Meaning | True when |
|----------|---------|-----------|
| `&&` | AND | Both sides are true |
| `\|\|` | OR | At least one side is true |
| `!` | NOT | The condition is false |

```java
int age = 17;
boolean hasLicense = true;

if (age >= 16 && hasLicense) {
    System.out.println("Can drive.");
}

if (age < 16 || !hasLicense) {
    System.out.println("Cannot drive.");
}
```

### Short-Circuit Evaluation

With `&&`, if the left side is `false`, the right side is **never evaluated**. With `||`, if the left side is `true`, the right side is skipped. This is not just an optimization — it prevents errors:

```java
String input = null;
if (input != null && input.equals("yes")) {
    // Safe — if input is null, the right side is never reached
}
```

### De Morgan's Laws

De Morgan's Laws describe how to distribute `!` across `&&` and `||`. They are useful for simplifying conditions and are a common source of exam questions.

| Original | Equivalent |
|----------|-----------|
| `!(a && b)` | `!a \|\| !b` |
| `!(a \|\| b)` | `!a && !b` |

In plain English:
- NOT (A AND B) = (NOT A) OR (NOT B)
- NOT (A OR B) = (NOT A) AND (NOT B)

```java
int x = 5;
int y = 10;

// These two conditions are equivalent:
System.out.println(!(x > 3 && y > 3));    // false
System.out.println(!(x > 3) || !(y > 3)); // false

// These two are also equivalent:
System.out.println(!(x > 3 || y > 20));   // false
System.out.println(!(x > 3) && !(y > 20)); // false
```

A practical use: when you want to negate a guard condition without having to think through it step by step, apply De Morgan's and simplify term by term.

!!! info "Optional theory module follows"
    [Module 07 — Propositional Logic and Rules of Inference](../07-propositional-logic/index.md) extends this material with the formal mathematical framework: propositions, implication, contrapositive/converse/inverse, biconditional, full equivalence laws, tautologies, and the four classic inference rules (Modus Ponens, Modus Tollens, Hypothetical Syllogism, Disjunctive Syllogism). It is optional — you can skip ahead to loops if you only need the practical syntax.

---

## Nested `if` Statements

`if` blocks can be placed inside other `if` blocks:

```java
int speed = 45;
boolean isAutonomous = true;

if (isAutonomous) {
    if (speed > 40) {
        System.out.println("Autonomous mode: speed too high, reducing.");
    } else {
        System.out.println("Autonomous mode: speed OK.");
    }
} else {
    System.out.println("Manual mode.");
}
```

Deep nesting quickly becomes hard to read. When you have more than two levels, consider restructuring your logic.

---

## Ternary Expressions

A ternary expression compresses a simple two-branch `if/else` into a single line.

```
condition ? valueIfTrue : valueIfFalse
```

```java
int score = 74;

// if/else version
String result;
if (score >= 60) {
    result = "Pass";
} else {
    result = "Fail";
}

// Ternary version — equivalent
String result = (score >= 60) ? "Pass" : "Fail";
System.out.println(result);   // Pass
```

Ternaries are useful for **assignment** and **printing** where the two branches produce a single value:

```java
System.out.println("Motor is " + (isEnabled ? "ON" : "OFF"));

int absValue = (n >= 0) ? n : -n;
```

!!! tip "Keep ternaries simple"
    Use ternary for straightforward two-option decisions. Avoid nesting ternaries inside ternaries — the result is nearly unreadable.

---

## `switch` Statements

`switch` is useful when a single variable is being tested against several specific values.

```java
int day = 3;

switch (day) {
    case 1:
        System.out.println("Monday");
        break;
    case 2:
        System.out.println("Tuesday");
        break;
    case 3:
        System.out.println("Wednesday");
        break;
    case 4:
        System.out.println("Thursday");
        break;
    case 5:
        System.out.println("Friday");
        break;
    default:
        System.out.println("Weekend");
}
// Output: Wednesday
```

### `break` is Required

Without `break`, execution **falls through** to the next case and keeps going:

```java
int x = 1;
switch (x) {
    case 1:
        System.out.println("One");
        // no break — falls through!
    case 2:
        System.out.println("Two");
        break;
    case 3:
        System.out.println("Three");
        break;
}
// Output:
// One
// Two
```

Fall-through is occasionally intentional (grouping cases), but is usually a bug. Always add `break` unless you explicitly intend fall-through.

### `default`

`default` runs when no `case` matches. It is optional but recommended to handle unexpected values.

### `switch` with Strings

`switch` works with `String`, `int`, `char`, and `enum` values:

```java
String command = "forward";

switch (command) {
    case "forward":
        System.out.println("Driving forward");
        break;
    case "backward":
        System.out.println("Driving backward");
        break;
    case "stop":
        System.out.println("Stopping");
        break;
    default:
        System.out.println("Unknown command: " + command);
}
```

### Grouping Cases

Multiple `case` labels can share one block:

```java
int month = 4;
switch (month) {
    case 1: case 3: case 5: case 7:
    case 8: case 10: case 12:
        System.out.println("31 days");
        break;
    case 4: case 6: case 9: case 11:
        System.out.println("30 days");
        break;
    case 2:
        System.out.println("28 or 29 days");
        break;
}
```

---

## `if/else` vs. `switch` — When to Use Which

| Situation | Prefer |
|-----------|--------|
| Range checks (`>`, `<`, `>=`) | `if/else if/else` |
| Testing one variable against many exact values | `switch` |
| Complex boolean logic (`&&`, `\|\|`, `!`) | `if/else if/else` |
| Simple two-outcome assignment or print | Ternary |

---

## Exercise

1. Ask the user to enter a month number (1–12). Use `if/else if` to print the number of days in that month. Handle invalid input (less than 1 or greater than 12).
2. Ask the user to enter a drive mode character (`'T'` for tank, `'A'` for arcade, `'C'` for cheesy). Use a `switch` statement to print a description of each mode. Handle unknown input with `default`.
3. Ask the user for a score (0–100). Use a ternary to print either `"Pass"` or `"Fail"` depending on whether the score is 60 or above.
4. Take this nested `if`:
   ```java
   if (isEnabled) {
       if (!isLocked) {
           run();
       }
   }
   ```
   Rewrite it as a single `if` using `&&`. Then write the De Morgan's negation: an `if` that runs an `else` branch (`disable()`) whenever the original would *not* call `run()`.
