# Strings and Simple Object Types

`String` is Java's built-in type for text. Unlike the eight primitive types, `String` is an **object** — and that distinction has important consequences.

---

## Declaring a String

```java
String name = "Alice";
String team = "Team 1234";
String empty = "";
String fromNumber = String.valueOf(42);   // "42"
```

Strings use **double quotes**. Single quotes are for `char` values.

---

## Strings Are Objects, Not Primitives

Primitive variables store their value directly. Object variables store a **reference** — a pointer to where the object lives in memory. This matters for equality (see below).

---

## Common String Methods

All `String` methods return a **new** String — they never modify the original. Strings in Java are **immutable**.

```java
String s = "  Hello, World!  ";
```

| Method | Example | Result |
|--------|---------|--------|
| `length()` | `s.length()` | `17` (includes spaces) |
| `charAt(i)` | `s.charAt(2)` | `'H'` |
| `indexOf(str)` | `s.indexOf("World")` | `9` |
| `contains(str)` | `s.contains("Hello")` | `true` |
| `toUpperCase()` | `s.toUpperCase()` | `"  HELLO, WORLD!  "` |
| `toLowerCase()` | `s.toLowerCase()` | `"  hello, world!  "` |
| `trim()` | `s.trim()` | `"Hello, World!"` (strips leading/trailing spaces) |
| `replace(old, new)` | `s.replace("World", "Java")` | `"  Hello, Java!  "` |
| `substring(start)` | `"Hello".substring(2)` | `"llo"` |
| `substring(start, end)` | `"Hello".substring(1, 4)` | `"ell"` (end is exclusive) |
| `equals(other)` | `"hi".equals("hi")` | `true` |
| `equalsIgnoreCase(other)` | `"Hi".equalsIgnoreCase("hi")` | `true` |
| `isEmpty()` | `"".isEmpty()` | `true` |
| `split(delimiter)` | `"a,b,c".split(",")` | `["a", "b", "c"]` |

```java
String message = "Hello, World!";

System.out.println(message.length());             // 13
System.out.println(message.toUpperCase());        // HELLO, WORLD!
System.out.println(message.substring(7, 12));     // World
System.out.println(message.replace("World", "Java")); // Hello, Java!

// Chaining methods — each call operates on the result of the previous
String cleaned = "  user input  ".trim().toLowerCase();
System.out.println(cleaned);  // "user input"
```

---

## The `==` Trap — Always Use `.equals()` for Strings

This is one of the most common bugs in Java:

```java
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b);        // false — compares memory addresses
System.out.println(a.equals(b));   // true  — compares content
```

`==` on objects asks "are these the exact same object in memory?" — not "do they contain the same text?" Almost always use `.equals()` when comparing Strings.

```java
String input = "yes";

// Wrong
if (input == "yes") { ... }

// Correct
if (input.equals("yes")) { ... }

// Case-insensitive comparison
if (input.equalsIgnoreCase("YES")) { ... }
```

!!! tip "Putting the literal first"
    A common defensive style puts the known string literal on the left side of `.equals()` to prevent a `NullPointerException` if the variable is null:
    ```java
    if ("yes".equals(input)) { ... }   // safe even if input is null
    ```

---

## String Concatenation

Use `+` to join strings together:

```java
String first = "Alice";
String last = "Smith";
String full = first + " " + last;    // "Alice Smith"

int teamNum = 1234;
String teamName = "Team " + teamNum; // "Team 1234" — int is converted automatically
```

Concatenating inside a loop is inefficient for large amounts of text (use `StringBuilder` for that), but fine for typical short uses.

---

## Escape Characters

Some characters inside a string need to be **escaped** with a backslash:

| Escape | Character |
|--------|-----------|
| `\n` | Newline — moves to the next line |
| `\t` | Tab — inserts a horizontal tab |
| `\\` | Literal backslash |
| `\"` | Literal double quote inside a string |

```java
System.out.println("Line one\nLine two");
// Line one
// Line two

System.out.println("Name:\tAlice");
// Name:   Alice

System.out.println("She said \"Hello\"");
// She said "Hello"

System.out.println("C:\\Users\\Alice");
// C:\Users\Alice
```

---

## Converting Between Strings and Primitives

```java
// String → int
int n = Integer.parseInt("42");

// String → double
double d = Double.parseDouble("3.14");

// int/double → String
String s1 = String.valueOf(42);
String s2 = String.valueOf(3.14);
String s3 = Integer.toString(42);

// Quickest way — concatenate with empty string
String s4 = 42 + "";
```

---

## `split()` — Parsing Delimited Text

`split()` returns a `String[]` (array of Strings). You will use this in the File I/O module to parse CSV data.

```java
String csv = "Alice,20,95.5";
String[] parts = csv.split(",");

System.out.println(parts[0]);   // Alice
System.out.println(parts[1]);   // 20
System.out.println(parts[2]);   // 95.5

// Parse the numeric fields
int age = Integer.parseInt(parts[1]);
double score = Double.parseDouble(parts[2]);
```

---

## `StringBuilder` — Building Strings Efficiently

Because every `+` creates a new `String` object, concatenating inside a loop can create many short-lived objects:

```java
// Each += allocates a new String — fine for a few joins, slow for hundreds
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i + ", ";
}
```

`StringBuilder` is a **mutable** text buffer. Append to it freely; call `toString()` once at the end to get the final `String`.

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 5; i++) {
    sb.append(i);
    sb.append(", ");
}
System.out.println(sb.toString());   // 0, 1, 2, 3, 4,
```

| Method | What it does |
|--------|-------------|
| `append(value)` | Adds any value to the end — `String`, `int`, `double`, `char`, etc. |
| `toString()` | Returns the accumulated `String` |
| `length()` | Number of characters currently stored |
| `reverse()` | Reverses the contents in place |
| `deleteCharAt(index)` | Removes one character |
| `insert(index, value)` | Inserts at a position |

Use plain `+` for short, one-time joins (error messages, `toString()` bodies). Switch to `StringBuilder` when building a string inside a loop or appending many pieces in sequence.

---

## Strings Are Immutable

Every String method that appears to "modify" a string actually returns a **new** String. The original is unchanged.

```java
String s = "hello";
s.toUpperCase();               // does nothing useful — result is discarded
System.out.println(s);         // still "hello"

String upper = s.toUpperCase(); // correct — capture the new string
System.out.println(upper);      // "HELLO"
```

---

## Traversing a String

A `String` can be processed character by character using `charAt(i)` in a `for` loop. The loop runs from index `0` to `length() - 1`.

```java
String word = "Hello";

for (int i = 0; i < word.length(); i++) {
    System.out.println(i + ": " + word.charAt(i));
}
// 0: H
// 1: e
// 2: l
// 3: l
// 4: o
```

### Counting Characters

```java
String sentence = "The quick brown fox";
int vowelCount = 0;

for (int i = 0; i < sentence.length(); i++) {
    char c = sentence.charAt(i);
    if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u') {
        vowelCount++;
    }
}
System.out.println("Vowels: " + vowelCount);   // 5
```

### `toCharArray()` — `for-each` Style

`toCharArray()` converts the String into a `char[]`, which allows a `for-each` loop when the index is not needed:

```java
for (char c : "Hello".toCharArray()) {
    System.out.print(c + " ");
}
// H e l l o
```

Use the indexed `for` loop when position matters — comparing adjacent characters, building a modified copy, or stopping early. Use `toCharArray()` when you only need each character's value.

---

## Exercise

1. Declare a `String` containing your full name.
2. Print the number of characters in it.
3. Print your name in all uppercase.
4. Print only your first name using `substring()` and `indexOf()` to find the space.
5. Declare a `String` like `"Boston,12,88.5"` and parse it into a city name (`String`), a number (`int`), and a score (`double`). Print each on its own line.
6. Demonstrate the `==` trap: create two `String` variables with the same text using `new String(...)`, compare them with `==` and `.equals()`, and print both results.
7. Use a `StringBuilder` to build the string `"0, 1, 2, 3, 4"` by appending each number and a comma-space separator inside a loop (do not hardcode the result — the loop must work for any count). Print the result using `toString()`.
