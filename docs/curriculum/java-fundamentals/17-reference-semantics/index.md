# Reference Semantics

Now that you know how to design a class ([Module 15](../15-simple-classes/index.md)), you need to understand how object variables actually behave at runtime. A primitive variable holds a value directly; an object variable holds a **reference** — a memory address pointing to where the object lives. Most surprising bugs in Java come from misunderstanding this distinction.

This module covers three intertwined concepts:

- **Aliasing** — two variables can point to the same object
- **Pass-by-value** — what happens when you pass an object to a method
- **`null` and `NullPointerException`** — what happens when a variable points to nothing

---

## Object Aliasing

Two variables can point to the **same** object in memory. This is called aliasing. Modifying the object through one variable is visible through the other.

```java
Dog rex = new Dog("Rex", 4);
Dog alias = rex;             // alias points to the same object — not a copy

alias.setAge(10);            // modifies the one Dog object

System.out.println(rex.getAge());   // 10 — rex sees the change too
System.out.println(rex == alias);   // true — same memory address
```

```
Memory:
                ┌────────────────────┐
   rex ────────▶│  Dog               │
                │   name = "Rex"     │
   alias ──────▶│   age  = 10        │
                └────────────────────┘
```

Both `rex` and `alias` are arrows pointing to the same box in memory. There is only one `Dog` object; there are two ways to reach it.

To get an **independent** copy you must construct a new object explicitly:

```java
Dog independent = new Dog(rex.getName(), rex.getAge());   // new object, same data
independent.setAge(99);
System.out.println(rex.getAge());        // 10 — unchanged
System.out.println(independent.getAge()); // 99
```

### `==` vs `.equals()` on Objects

`==` checks **reference equality** — are these two variables pointing to the same object?  
`.equals()` checks **content equality** — do these two objects have the same data?

```java
Dog a = new Dog("Rex", 4);
Dog b = new Dog("Rex", 4);   // separately constructed
Dog c = a;                    // alias

System.out.println(a == b);        // false — different objects
System.out.println(a == c);        // true — same object (aliased)
System.out.println(a.equals(b));   // true — same data
```

This is the same trap you saw with `String` in [Module 04](../04-strings/index.md#the--trap--always-use-equals-for-strings), now generalized: `==` on any object compares addresses, not content.

---

## Pass-by-Value

Java is **always** pass-by-value — a method always receives a *copy* of the argument. What the copy contains depends on the type:

- **Primitive:** the copy is the value itself. Changes inside the method do not affect the caller's variable.
- **Object:** the copy is the **reference** (memory address). The method can use it to modify the object's fields, but reassigning the parameter has no effect outside the method.

```java
public static void tryToChange(int n, Dog d) {
    n = 99;                    // changes the local copy only
    d.setAge(99);              // modifies the actual Dog object via its reference
    d = new Dog("Ghost", 0);   // reassigns local copy of reference — no effect outside
}

public static void main(String[] args) {
    int x = 5;
    Dog rex = new Dog("Rex", 4);

    tryToChange(x, rex);

    System.out.println(x);            // 5   — primitive unchanged
    System.out.println(rex.getAge()); // 99  — field was modified through the reference
    System.out.println(rex.getName());// Rex — not "Ghost"; reassignment had no effect
}
```

### What's Actually Happening

```
Before tryToChange runs:
  x ──▶ 5                              (primitive: stores the value)
  rex ──▶ [Dog: name="Rex", age=4]    (reference: stores an address)

Inside tryToChange (the method got copies):
  n ──▶ 5    →   n ──▶ 99             (only the local copy changed)
  d ──▶ [Dog: name="Rex", age=4]      (same address as rex — points to same Dog)
       d.setAge(99) follows the arrow and modifies the Dog
  d = new Dog(...) makes the LOCAL d point at a new object — rex still points at the old one
```

The method gets a **copy of the reference**, not a copy of the object. Following the reference modifies the real object. Replacing the reference variable replaces only the local copy.

This is why arrays and `ArrayList` passed to methods can have their elements modified — the method has a reference to the same underlying data.

---

## `null` and `NullPointerException`

`null` is a special value meaning "this reference points to nothing." Any object variable can hold `null`, and the default value of an unassigned object field is `null`.

```java
Dog d = null;
System.out.println(d);   // prints "null" — safe
d.getName();             // NullPointerException — cannot call a method on null
```

A `NullPointerException` (NPE) is thrown at runtime whenever you call a method or access a field on a `null` reference. It is one of the most common runtime errors in Java.

### Common Causes

```java
// Cause 1 — Method returns null and the result is used without checking
Dog found = findDog("Rex");          // may return null if not found
System.out.println(found.getName()); // NPE if found is null

// Cause 2 — Array of objects: slots start null until individually constructed
Dog[] dogs = new Dog[3];
dogs[0].getName();   // NPE — slot 0 is still null

// Cause 3 — Field that was never initialized
public class Owner {
    private Dog pet;   // defaults to null
    public String getPetName() { return pet.getName(); }   // NPE if pet wasn't set
}

// Cause 4 — Chained call where an intermediate result is null
String upper = config.getUser().getName().toUpperCase();
//             ^ if getUser() returns null, NPE on the next call
```

### Guarding Against NPE

The simplest defense is checking before using:

```java
Dog found = findDog("Rex");
if (found != null) {
    System.out.println(found.getName());
} else {
    System.out.println("Dog not found.");
}
```

Putting the String literal first in `.equals()` (shown in [Module 04](../04-strings/index.md#the--trap--always-use-equals-for-strings)) is a related habit:

```java
"yes".equals(answer)    // safe even if answer is null
answer.equals("yes")    // NPE if answer is null
```

Filling object arrays before use prevents Cause 2:

```java
Dog[] dogs = new Dog[3];
for (int i = 0; i < dogs.length; i++) {
    dogs[i] = new Dog("Dog " + i, 1);   // every slot now points to a real Dog
}
```

For values that legitimately may or may not exist, `Optional<T>` from [Module 34](../34-streams/index.md) is a more structured alternative to returning `null`.

### Reading a Stack Trace

When an NPE fires, the stack trace tells you exactly which line caused it:

```
Exception in thread "main" java.lang.NullPointerException: 
  Cannot invoke "Dog.getName()" because "found" is null
        at Kennel.report(Kennel.java:42)
        at Kennel.main(Kennel.java:14)
```

Read the top frame: `Kennel.java:42`. That's the line where the null reference was dereferenced. The variable name (`found`) is in the message. Modern Java (14+) tells you exactly what was null — older versions just say "NullPointerException" and you have to figure it out from the line.

---

## Exercise

1. Write a method `void resetCounter(Counter c)` that takes a `Counter` object (with an `int count` field, getter, setter, and an `increment()` method) and calls `c.setCount(0)`. Also write `void replaceCounter(Counter c)` that does `c = new Counter()`. Construct a `Counter`, increment it a few times, then call each method. Confirm that `resetCounter` changes the original but `replaceCounter` does not. Explain why in one sentence.

2. Create two `Dog` objects with the same name and age but constructed separately. Compare them with `==` and with `.equals()` and explain the results. Then make a third variable that aliases the first one. Compare all three pairs.

3. Trigger an NPE intentionally by:
   - Declaring a `Dog[] dogs = new Dog[5];` and accessing `dogs[0].getName()` without filling the array.
   - Reading the stack trace and identifying the line number.
   - Fixing the bug by initializing each slot in a loop, then re-running.

4. Write a method `String safeName(Dog d)` that returns the dog's name if `d` is not null, or the literal string `"(no dog)"` if it is. Use it to print a `Dog[]` that includes a few `null` slots without ever throwing an exception.
