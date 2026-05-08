# Exception Handling

Exceptions are runtime signals that something went wrong. Handling them keeps your program from crashing and gives you control over what happens when things go sideways.

---

## What Is an Exception?

When Java encounters a problem it cannot recover from on its own — dividing by zero, accessing an out-of-bounds array index, reading a file that does not exist — it **throws** an exception. If nothing catches it, the program crashes with a stack trace.

```java
int[] arr = {1, 2, 3};
System.out.println(arr[5]);   // throws ArrayIndexOutOfBoundsException — crash
```

---

## `try` / `catch` / `finally`

Wrap risky code in a `try` block. If an exception is thrown, execution jumps to the matching `catch` block.

```java
try {
    int result = 10 / 0;
    System.out.println(result);        // never reached
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());   // Cannot divide by zero
}

System.out.println("Program continues.");
```

After the `catch` block runs, the program continues normally — it does not crash.

### `finally`

A `finally` block runs **always** — whether an exception was thrown or not, and whether it was caught or not. Use it for cleanup that must happen regardless.

```java
try {
    int x = Integer.parseInt("not a number");
} catch (NumberFormatException e) {
    System.out.println("Bad input: " + e.getMessage());
} finally {
    System.out.println("This always runs.");
}
// Bad input: For input string: "not a number"
// This always runs.
```

---

## Checked vs. Unchecked Exceptions

Java divides exceptions into two categories:

| Category | Examples | Compiler requirement |
|----------|---------|---------------------|
| **Unchecked** (`RuntimeException` and subclasses) | `NullPointerException`, `ArrayIndexOutOfBoundsException`, `ArithmeticException`, `NumberFormatException` | No requirement — handle if you want |
| **Checked** (`Exception` subclasses, not `RuntimeException`) | `IOException`, `FileNotFoundException` | **Compiler forces you to handle or declare them** |

File I/O in the next module uses `IOException`, which is checked. You will get a compile error if you ignore it. This is why this module comes first.

---

## Common Exception Types

| Exception | Typical cause |
|-----------|--------------|
| `ArithmeticException` | Dividing an `int` by zero |
| `NullPointerException` | Calling a method on a `null` reference |
| `ArrayIndexOutOfBoundsException` | Index outside `[0, length-1]` |
| `NumberFormatException` | `Integer.parseInt("abc")` — non-numeric string |
| `IOException` | File not found, disk error, stream problem |
| `FileNotFoundException` | Specific `IOException` for missing files |
| `ClassCastException` | Downcasting to the wrong type |

---

## Catching Multiple Exception Types

```java
try {
    Scanner sc = new Scanner(new File("data.txt"));
    int n = Integer.parseInt(sc.nextLine());
    System.out.println(10 / n);
} catch (FileNotFoundException e) {
    System.out.println("File not found: " + e.getMessage());
} catch (NumberFormatException e) {
    System.out.println("File content is not a number.");
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero.");
}
```

Java checks `catch` blocks in order from top to bottom and runs the first matching one.

### Catching a Parent Exception Type

You can catch a broad parent type to handle multiple related exceptions at once:

```java
try {
    // ... risky code
} catch (Exception e) {
    System.out.println("Something went wrong: " + e.getMessage());
}
```

This is a safety net, not a recommended default — catching `Exception` can hide bugs. Be as specific as practical.

---

## `e.getMessage()` and `e.printStackTrace()`

```java
try {
    int[] arr = new int[3];
    arr[10] = 5;
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println(e.getMessage());      // short description
    e.printStackTrace();                     // full stack trace — useful for debugging
}
```

---

## `throws` — Declaring Instead of Handling

If a method does not want to handle a checked exception itself, it can declare that it **throws** it, passing responsibility to the caller:

```java
public static void readFile(String path) throws IOException {
    Scanner sc = new Scanner(new File(path));
    // ...
}

// The caller must then handle it:
try {
    readFile("data.txt");
} catch (IOException e) {
    System.out.println("Could not read file: " + e.getMessage());
}
```

---

## Try-with-Resources

When working with files and streams, the resource (file, scanner, writer) must be **closed** when you are done, even if an exception occurs. Try-with-resources handles this automatically.

```java
try (Scanner sc = new Scanner(new File("data.txt"))) {
    while (sc.hasNextLine()) {
        System.out.println(sc.nextLine());
    }
} catch (FileNotFoundException e) {
    System.out.println("File not found: " + e.getMessage());
}
// sc.close() is called automatically — even if an exception was thrown
```

The resource declared in the parentheses after `try` is closed when the block exits, no matter what. This is the correct way to open files, and you will use it throughout the next module.

---

## Wrapping User Input

A common use of `try-catch` is protecting `Integer.parseInt()` or `Double.parseDouble()` from non-numeric user input:

```java
Scanner sc = new Scanner(System.in);
int value = 0;
boolean valid = false;

while (!valid) {
    System.out.print("Enter a number: ");
    try {
        value = Integer.parseInt(sc.nextLine());
        valid = true;
    } catch (NumberFormatException e) {
        System.out.println("That is not a valid number. Try again.");
    }
}
System.out.println("You entered: " + value);
```

---

## Exercise

1. Write a method `divide(int a, int b)` that returns `a / b`. Wrap it in a `try-catch` for `ArithmeticException` and return `0` if division by zero is attempted. Test it from `main`.
2. Write a method `parseAge(String input)` that parses a String to an int. Catch `NumberFormatException` and return `-1` for invalid input. Test it with `"25"`, `"abc"`, and `""`.
3. Write a short program that asks the user for a filename. Use try-with-resources with a `Scanner` to open and print the file. Catch `FileNotFoundException` and print a friendly message if the file does not exist. (You will combine this directly with the next module.)
