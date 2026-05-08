# User Input with Scanner

Programs that can only print static text are limited. `Scanner` lets your program read input from the user at the keyboard.

---

## Basic Setup

```java
import java.util.Scanner;   // must be at the top of the file, before the class

public class InputExample {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);   // connect Scanner to the keyboard

        System.out.print("Enter your name: ");
        String name = sc.nextLine();

        System.out.print("Enter your age: ");
        int age = sc.nextInt();

        System.out.println("Hello, " + name + "! You are " + age + " years old.");

        sc.close();   // release the underlying stream when done
    }
}
```

```
Enter your name: Alice
Enter your age: 17
Hello, Alice! You are 17 years old.
```

---

## The `import` Statement

`Scanner` is not automatically available — you need to tell Java where to find it.

```java
import java.util.Scanner;
```

This line goes at the very top of your file, before `public class ...`. Without it, the compiler does not know what `Scanner` means.

---

## Creating a Scanner

```java
Scanner sc = new Scanner(System.in);
```

- `new Scanner(System.in)` creates a `Scanner` connected to standard input (the keyboard)
- The variable name `sc` is conventional but you can use any name you like

---

## Reading Methods

| Method | Reads | Notes |
|--------|-------|-------|
| `sc.nextInt()` | An `int` | Stops at whitespace |
| `sc.nextDouble()` | A `double` | Stops at whitespace |
| `sc.nextLong()` | A `long` | Stops at whitespace |
| `sc.next()` | One whitespace-delimited token | Reads one "word" |
| `sc.nextLine()` | The entire remaining line | Reads until `Enter` |
| `sc.nextBoolean()` | `true` or `false` | Reads literal text "true"/"false" |

```java
System.out.print("Enter a decimal: ");
double speed = sc.nextDouble();

System.out.print("Enter a word: ");
String word = sc.next();

System.out.print("Enter a full sentence: ");
String sentence = sc.nextLine();
```

---

## The `nextLine()` After `nextInt()` Gotcha

This is one of the most common Scanner bugs. When you call `nextInt()` (or `nextDouble()`), it reads the number but **leaves the newline character** (`\n`) from pressing Enter still in the input buffer. The very next `nextLine()` call reads that leftover newline and returns an empty string immediately.

```java
Scanner sc = new Scanner(System.in);

System.out.print("Enter your age: ");
int age = sc.nextInt();
// Buffer now contains: "\n" (the Enter key press)

System.out.print("Enter your name: ");
String name = sc.nextLine();   // reads the leftover "\n" — name will be ""!

System.out.println("Name: " + name);   // prints "Name: " with nothing after it
```

**Fix: call a throwaway `sc.nextLine()` immediately after `nextInt()` or `nextDouble()`.**

```java
System.out.print("Enter your age: ");
int age = sc.nextInt();
sc.nextLine();   // consume the leftover newline

System.out.print("Enter your name: ");
String name = sc.nextLine();   // now reads correctly
```

---

## Reading Multiple Values

```java
Scanner sc = new Scanner(System.in);

System.out.print("Enter team number and name: ");
int number = sc.nextInt();
sc.nextLine();                    // consume newline after the int
String teamName = sc.nextLine();

System.out.println("Team " + number + ": " + teamName);
```

---

## Input Validation

Use a loop to keep asking until the user gives valid input:

```java
Scanner sc = new Scanner(System.in);
int choice = -1;

while (choice < 1 || choice > 3) {
    System.out.print("Enter 1, 2, or 3: ");
    choice = sc.nextInt();
    if (choice < 1 || choice > 3) {
        System.out.println("Invalid. Try again.");
    }
}
System.out.println("You chose: " + choice);
```

---

## `sc.close()`

Calling `sc.close()` releases the underlying input stream when you are done. For small programs it makes little practical difference, but it is good practice.

!!! note
    Do not close `Scanner` in the middle of a program if you intend to read more input later. Close it only at the very end.

---

## `print` vs `println` for Prompts

Use `System.out.print` (without the `ln`) for input prompts so the cursor stays on the same line where the user types:

```java
System.out.print("Enter your name: ");    // cursor stays on same line
String name = sc.nextLine();
// User sees: Enter your name: _
```

Using `println` for a prompt puts the cursor on a blank line below, which looks awkward.

---

## Exercise

Write a program that:

1. Asks the user for their robot's name (a String)
2. Asks for the number of wheels (an int)
3. Asks for the robot's max speed in meters per second (a double)
4. Prints a summary like:
   ```
   Robot: Titan
   Wheels: 6
   Max Speed: 4.5 m/s
   ```
5. Add input validation so that the number of wheels must be greater than 0.
