# Hello World

Your first Java program. Every token on every line is explained.

---

## The Program

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

Run it. You should see:

```
Hello, World!
```

Now let's go through every piece of it.

---

## Line by Line

### `public class HelloWorld {`

| Token | Meaning |
|-------|---------|
| `public` | An **access modifier** — this class is visible to all other code |
| `class` | Java is object-oriented. All code must live inside a **class** |
| `HelloWorld` | The **class name** — this must exactly match the filename (`HelloWorld.java`) |
| `{` | Opens the **class body** — everything between this `{` and its matching `}` belongs to the class |

!!! warning "File name must match class name"
    If your file is named `HelloWorld.java`, your class must be named `HelloWorld`. A mismatch is a compile error.

---

### `public static void main(String[] args) {`

This is the **entry point** — the JVM calls this exact method to start your program. The signature must be written exactly as shown.

| Token | Meaning |
|-------|---------|
| `public` | Visible to the JVM from outside this class |
| `static` | Belongs to the class itself, not to any object. The JVM can call it without creating a `HelloWorld` instance. You will revisit this concept in the OOP modules. |
| `void` | This method **returns nothing** — it just does work |
| `main` | The method name the JVM specifically looks for |
| `String[] args` | Command-line arguments passed to the program. Safe to ignore for now — it must be there but you do not have to use it |
| `{` | Opens the **method body** |

---

### `System.out.println("Hello, World!");`

| Token | Meaning |
|-------|---------|
| `System` | A built-in Java class that provides access to system-level resources |
| `out` | A field on `System` representing **standard output** (your terminal/console) |
| `println` | Short for "print line" — writes the text and then moves to a new line |
| `"Hello, World!"` | A **String literal** — text wrapped in double quotes |
| `;` | **Semicolons end every statement** in Java. Missing a semicolon is one of the most common compile errors. |

**`print` vs `println`:**

```java
System.out.print("Hello, ");   // prints without a newline — cursor stays on same line
System.out.print("World!");    // continues on the same line
// Output: Hello, World!

System.out.println("Hello,");  // prints and then moves to the next line
System.out.println("World!");  // starts on a new line
// Output:
// Hello,
// World!
```

---

### The Closing Braces `}`

Every `{` must have a matching `}`. This program has two opening braces — one for the class, one for the method — so it needs two closing braces.

```java
public class HelloWorld {          // class opens
    public static void main(...) { // method opens
        System.out.println("...");
    }                              // method closes
}                                  // class closes
```

---

## Comments

Comments are ignored by the compiler. They are notes for humans reading the code.

```java
// This is a single-line comment — everything after // is ignored

/*
   This is a multi-line comment.
   Useful for longer explanations.
*/

public class HelloWorld {
    public static void main(String[] args) {
        // Print a greeting to the console
        System.out.println("Hello, World!");
    }
}
```

!!! tip "When to write comments"
    Good code is mostly self-explanatory through clear naming. Write a comment when the **why** is not obvious — not to repeat what the code already says.

---

## The Compile-Run Cycle

```
HelloWorld.java  →  javac  →  HelloWorld.class  →  JVM  →  Hello, World!
```

1. You write `HelloWorld.java`
2. The **compiler** (`javac`) checks for errors and produces `HelloWorld.class` (bytecode)
3. The **JVM** reads the bytecode and executes it
4. Output appears in the console

Your IDE automates steps 2–4 when you press Run.

---

## Exercise

Write a program that prints your name and what team you are on, each on its own line. For example:

```
Name: Alex
Team: Team 1234
```

??? tip "Hint"
    You need two `System.out.println(...)` calls. Each one prints one line.
