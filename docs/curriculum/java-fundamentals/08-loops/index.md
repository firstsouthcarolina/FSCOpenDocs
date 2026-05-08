# Looping Logic

Loops let your program repeat a block of code — either a set number of times, or until a condition changes.

---

## `while` Loop

Executes the body **as long as the condition is true**. The condition is checked before each iteration.

```java
int count = 0;
while (count < 5) {
    System.out.println("Count: " + count);
    count++;
}
// Count: 0
// Count: 1
// Count: 2
// Count: 3
// Count: 4
```

If the condition is `false` when the loop is first reached, the body never runs.

### Infinite Loop with `break` (Sentinel Pattern)

A common pattern for menus and input loops:

```java
Scanner sc = new Scanner(System.in);

while (true) {
    System.out.print("Enter a number (0 to quit): ");
    int n = sc.nextInt();

    if (n == 0) {
        System.out.println("Exiting.");
        break;   // exits the loop immediately
    }

    System.out.println("You entered: " + n);
}
```

`while (true)` creates a loop that would run forever — `break` is the only way out.

---

## `do-while` Loop

The body always runs **at least once**. The condition is checked at the **end** of each iteration.

```java
int n;
Scanner sc = new Scanner(System.in);

do {
    System.out.print("Enter a positive number: ");
    n = sc.nextInt();
    if (n <= 0) System.out.println("Must be positive. Try again.");
} while (n <= 0);

System.out.println("You entered: " + n);
```

Use `do-while` when the body must execute once before it makes sense to check the condition — input validation is the classic example.

| | `while` | `do-while` |
|---|---------|-----------|
| Condition checked | Before first iteration | After first iteration |
| Minimum iterations | 0 | 1 |
| Best use case | General looping | Input validation, run-at-least-once logic |

---

## `for` Loop

The `for` loop is the most common loop for a **known number of iterations**.

```java
for (int i = 0; i < 5; i++) {
    System.out.println("i = " + i);
}
// i = 0
// i = 1
// i = 2
// i = 3
// i = 4
```

Anatomy of the `for` header:

```
for ( initializer ; condition ; update )
```

| Part | When it runs | Example |
|------|-------------|---------|
| Initializer | Once, before the loop starts | `int i = 0` |
| Condition | Before each iteration — loop continues while true | `i < 5` |
| Update | After each iteration | `i++` |

### Counting Down

```java
for (int i = 10; i > 0; i--) {
    System.out.print(i + " ");
}
System.out.println("Liftoff!");
// 10 9 8 7 6 5 4 3 2 1 Liftoff!
```

### Stepping by More Than One

```java
for (int i = 0; i <= 100; i += 10) {
    System.out.print(i + " ");
}
// 0 10 20 30 40 50 60 70 80 90 100
```

---

## Enhanced `for-each` Loop

Designed specifically for iterating over arrays and collections. Cleaner syntax when you do not need the index.

```java
int[] scores = {88, 72, 95, 61, 84};

for (int score : scores) {
    System.out.println(score);
}
```

Read `for (int score : scores)` as "for each `int` named `score` in `scores`."

**When to use `for-each` vs. regular `for`:**

| Need | Use |
|------|-----|
| Iterate every element, index not needed | `for-each` |
| Need the index (`i`) during iteration | Regular `for` |
| Iterate only part of the array | Regular `for` |
| Modify elements in place | Regular `for` |

!!! note
    `for-each` gives you a copy of each element for primitives. Writing `score = 0` inside the loop does not change the original array.

---

## Nested Loops

A loop inside another loop. The inner loop completes all its iterations for each single iteration of the outer loop.

```java
for (int row = 1; row <= 3; row++) {
    for (int col = 1; col <= 4; col++) {
        System.out.print("[" + row + "," + col + "] ");
    }
    System.out.println();
}
// [1,1] [1,2] [1,3] [1,4]
// [2,1] [2,2] [2,3] [2,4]
// [3,1] [3,2] [3,3] [3,4]
```

Nested loops are used heavily with 2D arrays (covered in the Arrays module).

---

## `break` and `continue`

### `break` — exit the loop immediately

```java
for (int i = 0; i < 10; i++) {
    if (i == 5) break;
    System.out.print(i + " ");
}
// 0 1 2 3 4
```

### `continue` — skip the rest of this iteration and go to the next

```java
for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;   // skip even numbers
    System.out.print(i + " ");
}
// 1 3 5 7 9
```

In a `while` loop, `continue` jumps back to the condition check. Make sure the update still happens or you will create an infinite loop:

```java
int i = 0;
while (i < 10) {
    i++;                          // update happens before continue
    if (i % 2 == 0) continue;
    System.out.print(i + " ");
}
// 1 3 5 7 9
```

---

## Choosing the Right Loop

| Situation | Best choice |
|-----------|-------------|
| Known number of iterations | `for` |
| Condition-based, may not run at all | `while` |
| Must run at least once (e.g., input validation) | `do-while` |
| Iterating all elements of an array, no index needed | `for-each` |
| Menu or event loop that runs until exit | `while (true)` with `break` |

---

## Exercise

1. Use a `for` loop to print the multiplication table for 7 (7×1 through 7×12).
2. Use a `while` loop to keep asking the user to enter a password until they enter `"1234"`. Print how many attempts it took.
3. Use a `do-while` loop to ask the user for a number between 1 and 10. Keep asking until the input is valid.
4. Use nested `for` loops to print a 5×5 grid of asterisks:
   ```
   * * * * *
   * * * * *
   * * * * *
   * * * * *
   * * * * *
   ```
5. Using a `for` loop and `continue`, print all numbers from 1 to 50 that are divisible by 3 but not by 9.
