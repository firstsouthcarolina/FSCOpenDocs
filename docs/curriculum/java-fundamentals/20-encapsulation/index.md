# Encapsulation

Encapsulation is the first of the four OOP pillars. It means **hiding the internal state of an object** and requiring all interaction to go through defined methods. The class controls what values are allowed to exist, and outside code cannot bypass those rules.

You have already been doing this informally — `private` fields with public getters and validating setters from [Module 15](../15-simple-classes/index.md). This module formalizes the concept and introduces the full access modifier system.

---

## Why Encapsulation Matters

Consider a class with `public` fields:

```java
public class BankAccount {
    public double balance;   // anyone can write anything
}
```

```java
BankAccount acc = new BankAccount();
acc.balance = -500_000;     // perfectly legal — class has no defenses
```

The class has no way to enforce rules. Any caller can put the object into an invalid state. With encapsulation, the field is hidden and every change goes through a method that enforces the rules:

```java
public class BankAccount {
    private double balance;   // hidden

    public BankAccount(double initialBalance) {
        this.balance = (initialBalance >= 0) ? initialBalance : 0;
    }

    public double getBalance() { return balance; }

    public void deposit(double amount) {
        if (amount > 0) balance += amount;
    }

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) balance -= amount;
    }
}
```

Now `balance = -500_000` is a compile error. Every change goes through `deposit` or `withdraw`, both of which reject invalid input. The class enforces its own invariants.

---

## Access Modifiers

Java has four levels of visibility, controlled by the modifier on a field, method, or class:

| Modifier | Accessible from |
|----------|----------------|
| `private` | Only within this class |
| `protected` | This class, subclasses, and same package |
| `public` | Everywhere |
| *(none)* | Same package only (package-private) |

### Choosing the Right Modifier

- **`private`** — the default for fields. Only loosen if a specific use case demands it.
- **`protected`** — for fields and methods that subclasses legitimately need. You will see this in [Module 21](../21-inheritance/index.md).
- **`public`** — for the methods that form your class's API: constructors, getters, setters, action methods.
- **package-private** *(no modifier)* — useful for helper classes and methods that are only meant to be used inside one package.

A good heuristic: start every field as `private` and every method as the lowest visibility that still works. Open access up only when you have a concrete reason.

---

## The Validation Pattern

Encapsulation is most useful when setters validate their inputs:

```java
public void setAge(int age) {
    if (age >= 0 && age < 150) {
        this.age = age;
    }
    // outside the valid range — silently ignore, or throw an exception
}
```

There are two common ways to handle invalid input:

```java
// Option 1 — silently ignore (simple, but the caller doesn't know it failed)
public void setAge(int age) {
    if (age >= 0 && age < 150) this.age = age;
}

// Option 2 — throw an exception (forces the caller to handle the problem)
public void setAge(int age) {
    if (age < 0 || age >= 150) {
        throw new IllegalArgumentException("Age out of range: " + age);
    }
    this.age = age;
}
```

Exception-based validation is covered in [Module 28](../28-exceptions/index.md). For now, the silent-reject pattern is fine for most simple classes.

---

## Encapsulation and `private final`

Combining encapsulation (`private`) with immutability (`final`) is the strongest possible protection — the field is hidden *and* unchangeable:

```java
public class Sensor {
    private final int port;   // set once, never changed, never visible

    public Sensor(int port) {
        this.port = port;
    }

    public int getPort() { return port; }
}
```

Once constructed, a `Sensor`'s `port` cannot be modified by any code, anywhere. This is the right default for any field whose value is fixed at construction time.

---

## Why Not Just Make Everything Public?

Beginners sometimes ask why all this hiding is necessary. The reasons all come down to long-term cost:

1. **Invariants** — a class can promise things about its state ("balance is never negative") only if it controls who can write to that state.
2. **Refactoring** — if `balance` is `public`, it becomes part of every caller's code. Renaming it or replacing it with a computed value breaks everything. If it is `private`, only the class's own methods need to change.
3. **Validation** — a setter can enforce rules; a public field cannot.
4. **Debugging** — when something goes wrong with `balance`, you only need to look at the methods that mutate it. With a public field, anywhere in the codebase could be the culprit.

The tax of writing a getter and setter is small. The benefit compounds for the entire life of the project.

---

## Exercise

1. Create a `Thermostat` class with a `private double temperature` field. Add a constructor that initializes it to 20.0. Add a getter and a `setTemperature(double t)` setter that only accepts values between -10 and 40 (inclusive). In a tester, attempt to set values inside and outside the range and confirm the field is only updated for valid inputs.

2. Add a `private final String location` field to `Thermostat`, set in the constructor. Add a getter for it. Confirm with a compile-time check that you cannot reassign `location` outside the constructor.

3. Take a class you wrote in [Module 15](../15-simple-classes/index.md) and audit every field. For each one, ask: should this be `private`? Should it be `final`? Could a malicious caller put the object into an invalid state? Tighten the access modifiers accordingly.

4. In your own words, explain what would go wrong in the `BankAccount` example if the `balance` field were `public` instead of `private`. Give a specific scenario where a bug would be much harder to track down.
