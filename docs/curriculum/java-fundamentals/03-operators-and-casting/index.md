# Operators, Expressions, and Type Casting

Java's full set of operators, how expressions are evaluated, and how to convert between numeric types intentionally.

---

## Arithmetic Operators

```java
int a = 10;
int b = 3;

System.out.println(a + b);   // 13 — addition
System.out.println(a - b);   // 7  — subtraction
System.out.println(a * b);   // 30 — multiplication
System.out.println(a / b);   // 3  — integer division (truncates)
System.out.println(a % b);   // 1  — modulo (remainder after division)
```

### Modulo `%`

Modulo gives you the **remainder** of division. It has more uses than it looks:

```java
// Check if a number is even or odd
int n = 7;
if (n % 2 == 0) {
    System.out.println("Even");
} else {
    System.out.println("Odd");   // prints "Odd"
}

// Wrap an index around an array (covered in the Arrays module)
int index = 13;
int arrayLength = 5;
System.out.println(index % arrayLength);   // 3 — wraps around

// Check divisibility
if (n % 3 == 0) {
    System.out.println("Divisible by 3");
}
```

---

## Compound Assignment Operators

These are shorthand for `variable = variable (operator) value`.

| Operator | Equivalent | Example | Result (if `x = 10`) |
|----------|------------|---------|----------------------|
| `+=` | `x = x + n` | `x += 3` | `13` |
| `-=` | `x = x - n` | `x -= 3` | `7` |
| `*=` | `x = x * n` | `x *= 2` | `20` |
| `/=` | `x = x / n` | `x /= 4` | `2` |
| `%=` | `x = x % n` | `x %= 3` | `1` |

```java
int score = 0;
score += 10;    // score is now 10
score += 5;     // score is now 15
score -= 3;     // score is now 12
```

---

## Increment and Decrement

`++` adds 1; `--` subtracts 1. They come in two forms:

```java
int x = 5;

// Prefix — increment first, then use the value
System.out.println(++x);   // 6 (x is now 6)

// Postfix — use the value first, then increment
System.out.println(x++);   // 6 (x becomes 7 after this line)
System.out.println(x);     // 7
```

In a `for` loop you will almost always see the postfix form (`i++`), and the distinction does not matter because the result is not used in the same expression:

```java
for (int i = 0; i < 5; i++) {   // i++ and ++i are equivalent here
    System.out.println(i);
}
```

---

## Order of Operations

Java follows standard mathematical precedence:

1. Parentheses `()`
2. Unary operators (`++`, `--`, `-` negative sign)
3. `*`, `/`, `%`
4. `+`, `-`

```java
System.out.println(2 + 3 * 4);      // 14, not 20 — multiplication first
System.out.println((2 + 3) * 4);    // 20 — parentheses override
System.out.println(10 - 4 / 2);     // 8, not 3 — division first
```

When in doubt, add parentheses. They make intent explicit and prevent bugs.

---

## Type Casting

A **cast** explicitly converts a value from one type to another. Write the target type in parentheses before the value.

### Widening (safe, automatic)

Going from a smaller type to a larger one. Java does this automatically.

```java
int i = 42;
double d = i;             // automatic — int widens to double
System.out.println(d);    // 42.0
```

### Narrowing (explicit, may lose data)

Going from a larger type to a smaller one. You must cast explicitly — Java forces you to acknowledge that data might be lost.

```java
double d = 9.99;
int i = (int) d;          // explicit cast required
System.out.println(i);    // 9 — the .99 is dropped, NOT rounded
```

!!! warning "Casting truncates, it does not round"
    `(int) 9.99` gives `9`, not `10`. If you want rounding, use `Math.round()` first.
    ```java
    long rounded = Math.round(9.99);   // 10
    int i = (int) rounded;             // 10
    ```

### Fixing integer division with a cast

```java
int total = 47;
int count = 5;

double average = (double) total / count;   // 9.4
// Without the cast: 47 / 5 = 9 (integer division)
```

You only need to cast **one** of the operands. Once one side is a `double`, Java promotes the other automatically.

### Casting `char` to `int` and back

```java
char c = 'A';
int code = (int) c;           // 65
char back = (char)(code + 1); // 'B'
```

---

## The `+` Operator and Strings

When `+` involves a `String`, Java performs **string concatenation** instead of addition — even if the other operand looks like a number. This catches new programmers off guard:

```java
System.out.println("Result: " + 3 + 4);    // "Result: 34"  — concatenation, not 7
System.out.println("Result: " + (3 + 4));  // "Result: 7"   — addition happens first in parens
System.out.println(3 + 4 + " is the sum"); // "7 is the sum" — 3+4 evaluated first, then concat
```

The rule: `+` evaluates left-to-right. As soon as a `String` appears, the rest is concatenation.

---

## The `Math` Class

Java's built-in `Math` class provides common mathematical operations. No import is needed.

```java
System.out.println(Math.abs(-15));        // 15  — absolute value
System.out.println(Math.min(4, 9));       // 4   — smaller of two values
System.out.println(Math.max(4, 9));       // 9   — larger of two values
System.out.println(Math.pow(2, 10));      // 1024.0 — 2 to the 10th power
System.out.println(Math.sqrt(144));       // 12.0 — square root
System.out.println(Math.PI);             // 3.141592653589793
System.out.println(Math.floor(4.9));      // 4.0 — round down
System.out.println(Math.ceil(4.1));       // 5.0 — round up
```

### `Math.random()`

Returns a random `double` in the range `[0.0, 1.0)` — including 0.0, excluding 1.0.

```java
double roll = Math.random();              // 0.0 to 0.9999...

// Random integer from 0 to 5 (inclusive)
int d6 = (int)(Math.random() * 6);       // 0, 1, 2, 3, 4, or 5

// Random integer from 1 to 6
int die = (int)(Math.random() * 6) + 1;
```

---

## Exercise

1. Calculate and print the area of a circle with radius `7`. Use `Math.PI` and `Math.pow`.
2. Write code that takes two `int` variables (`dividend` and `divisor`) and prints both the integer division result and the true decimal result.
3. Starting with `int score = 0`, use compound assignment operators to add 25, subtract 10, then multiply by 2. Print the final value.
4. Generate a random integer between 1 and 100 (inclusive) and print it.
