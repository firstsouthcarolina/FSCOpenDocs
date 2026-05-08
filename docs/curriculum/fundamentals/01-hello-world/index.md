---
icon: lucide/terminal
---

# Hello World

The smallest useful Java program has a class and a `main` method. Java starts execution inside `main`.

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

## The Pieces

- `public class Main` defines a class named `Main`.
- `{` and `}` mark the body of the class and method.
- `public static void main(String[] args)` is the entry point.
- `System.out.println(...)` prints a line to the console.
- Semicolons end most Java statements.

## Comments

Comments explain intent. They are ignored by Java.

```java
// Single-line comment

/*
 * Multi-line comment
 */
```

Use comments for decisions that are not obvious from the code. Do not comment every line.

## Practice

Write a program that prints:

```text
My name is Ada.
I am learning Java for FRC.
```

Then change the program so each sentence is printed by a separate `println` statement.
