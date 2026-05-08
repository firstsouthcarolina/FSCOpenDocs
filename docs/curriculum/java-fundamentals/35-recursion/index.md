# Recursion

A **recursive** method is one that calls itself. Every recursive solution has two parts: a **base case** that stops the recursion, and a **recursive case** that breaks the problem into a smaller version of itself.

---

## The Two Rules of Recursion

1. **Base case** — a condition under which the method returns immediately without calling itself. Without a base case, the method calls itself forever.
2. **Recursive case** — the method calls itself with a **simpler or smaller** input, moving toward the base case.

If either rule is violated — no base case, or recursive calls that don't shrink the problem — you get a `StackOverflowError`.

---

## Factorial

The factorial of `n` (written `n!`) is `n × (n-1) × (n-2) × … × 1`. By definition, `0! = 1`.

**Iterative version:**
```java
public static int factorialIterative(int n) {
    int result = 1;
    for (int i = 2; i <= n; i++) {
        result *= i;
    }
    return result;
}
```

**Recursive version:**
```java
public static int factorial(int n) {
    if (n == 0) return 1;         // base case
    return n * factorial(n - 1);  // recursive case
}
```

Tracing `factorial(4)`:
```
factorial(4)
  → 4 * factorial(3)
        → 3 * factorial(2)
                → 2 * factorial(1)
                        → 1 * factorial(0)
                                → 1          ← base case
                        → 1 * 1 = 1
                → 2 * 1 = 2
        → 3 * 2 = 6
  → 4 * 6 = 24
```

Each call waits for the one below it to return. The chain unwinds from the bottom up.

---

## The Call Stack

Each method call creates a **stack frame** in the JVM's call stack, storing the local variables and return address for that call. Recursive calls stack up frames:

```
[factorial(0)] ← top of stack
[factorial(1)]
[factorial(2)]
[factorial(3)]
[factorial(4)]
[main]         ← bottom
```

When `factorial(0)` returns `1`, its frame is popped. Then `factorial(1)` can finish and pop, and so on down to `main`.

If the recursion never hits the base case, the stack fills until the JVM throws `StackOverflowError`.

---

## Fibonacci

The Fibonacci sequence: `0, 1, 1, 2, 3, 5, 8, 13, …` — each number is the sum of the two before it.

```java
public static int fib(int n) {
    if (n == 0) return 0;          // base case 1
    if (n == 1) return 1;          // base case 2
    return fib(n - 1) + fib(n - 2); // recursive case
}
```

!!! warning "Exponential growth"
    `fib(n)` calls itself twice per invocation. `fib(40)` makes over a billion calls. This naive version is O(2ⁿ). For large inputs, use a loop or memoization instead.

---

## Sum of an Array

```java
public static int sum(int[] arr, int index) {
    if (index == arr.length) return 0;          // base case: past the end
    return arr[index] + sum(arr, index + 1);    // add current element + sum of rest
}

// Call:
int total = sum(new int[]{1, 2, 3, 4, 5}, 0);  // 15
```

---

## Recursive Binary Search

Binary search on a sorted array: compare the target to the middle element. If the target is smaller, search the left half; if larger, search the right half.

```java
public static int binarySearch(int[] arr, int target, int low, int high) {
    if (low > high) return -1;              // base case: not found

    int mid = (low + high) / 2;
    if (arr[mid] == target) return mid;     // base case: found
    if (target < arr[mid]) return binarySearch(arr, target, low, mid - 1);
    else                   return binarySearch(arr, target, mid + 1, high);
}

// Call:
int[] sorted = {2, 5, 8, 12, 16, 23, 38, 56};
int index = binarySearch(sorted, 23, 0, sorted.length - 1);  // 5
```

---

!!! note
    This section uses binary search to illustrate recursion. The full treatment — iterative and recursive versions side by side, tracing, overflow-safe midpoint, and `Arrays.binarySearch` — is in [Module 40](../40-searching/index.md).

---

## Reverse a String

```java
public static String reverse(String s) {
    if (s.isEmpty()) return s;                  // base case
    return reverse(s.substring(1)) + s.charAt(0); // last char first
}

System.out.println(reverse("hello"));  // olleh
```

---

## Recursion vs Iteration

Every recursive solution can be rewritten iteratively, and vice versa. Choose based on clarity.

| Scenario | Prefer |
|----------|--------|
| Tree or graph traversal | Recursion — the structure is naturally hierarchical |
| Simple loops (sum, max, search) | Iteration — cleaner and no stack overhead |
| Divide-and-conquer algorithms (merge sort, binary search) | Either — both common |
| Deep recursion on large inputs | Iteration — avoids `StackOverflowError` |

---

## Common Pitfalls

**Missing base case:**
```java
public static int bad(int n) {
    return n * bad(n - 1);   // no base case → StackOverflowError
}
```

**Base case never reached:**
```java
public static int bad(int n) {
    if (n == 0) return 1;
    return n * bad(n + 1);   // n increases, never reaches 0
}
```

**Wrong base case value:**
```java
public static int factorial(int n) {
    if (n == 1) return 1;    // misses n == 0, crashes on factorial(0)
    return n * factorial(n - 1);
}
```

---

## Exercise

1. Write a recursive method `power(int base, int exp)` that returns `base` raised to the power `exp`. Base case: `exp == 0` returns 1.
2. Write a recursive method `countDown(int n)` that prints the numbers from `n` down to 1, then prints `"Go!"`. No loops allowed.
3. Write a recursive method `isPalindrome(String s)` that returns `true` if `s` reads the same forwards and backwards. Base cases: empty string and single-character string are both palindromes.
4. Trace `factorial(3)` by hand, drawing the call stack as it builds up and unwinds. Write out each frame as it is pushed and popped.
