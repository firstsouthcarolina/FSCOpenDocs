# Methods and Functions

Methods let you package a block of code under a name, call it multiple times, and pass data in and out. They are also the foundation that makes OOP work — constructors, getters, and setters are all methods.

---

## Your First Method

Up to now, all of your code lived directly inside `main`. Methods let you pull code out into named, reusable units.

```java
public class MethodExample {

    public static void greet(String name) {
        System.out.println("Hello, " + name + "!");
    }

    public static void main(String[] args) {
        greet("Alice");   // Hello, Alice!
        greet("Bob");     // Hello, Bob!
        greet("Charlie"); // Hello, Charlie!
    }
}
```

The same logic runs three times without being written three times.

---

## Method Anatomy

```
accessModifier  static  returnType  methodName ( parameterType parameterName, ... ) {
    // body
    return value;   // if returnType is not void
}
```

```java
public static double circleArea(double radius) {
    return Math.PI * radius * radius;
}
```

| Part | Example | Meaning |
|------|---------|---------|
| Access modifier | `public` | Who can call this method |
| `static` | `static` | Belongs to the class; no object needed to call it |
| Return type | `double` | What type of value the method hands back |
| Method name | `circleArea` | How you call it |
| Parameters | `double radius` | Input the method needs to do its work |
| `return` | `return Math.PI * ...` | Exits the method and hands the value back |

---

## `void` vs. Return Types

A method that **does not return a value** has return type `void`:

```java
public static void printDivider() {
    System.out.println("--------------------");
}
```

A method that **returns a value** declares its type and uses `return`:

```java
public static int add(int a, int b) {
    return a + b;
}

// Calling it:
int sum = add(3, 4);   // sum is 7
System.out.println(add(10, 20));   // prints 30
```

`return` ends the method immediately. Any code after `return` in the same block is unreachable.

---

## Parameters vs. Arguments

- **Parameters** are the variables declared in the method signature: `(int a, int b)`
- **Arguments** are the actual values passed when calling the method: `add(3, 4)`

```java
public static double scale(double value, double factor) {  // parameters
    return value * factor;
}

double result = scale(5.0, 1.5);   // arguments: 5.0 and 1.5
```

Parameters are local to the method — they do not exist outside it.

---

## Multiple Parameters and Return Types

```java
public static double average(double a, double b, double c) {
    return (a + b + c) / 3.0;
}

public static String classify(int score) {
    if (score >= 90) return "A";
    if (score >= 80) return "B";
    if (score >= 70) return "C";
    return "F";
}

public static boolean isEven(int n) {
    return n % 2 == 0;
}
```

---

## Methods Calling Other Methods

```java
public static double squareArea(double side) {
    return side * side;
}

public static double squarePerimeter(double side) {
    return 4 * side;
}

public static void printSquareInfo(double side) {
    System.out.println("Area: " + squareArea(side));
    System.out.println("Perimeter: " + squarePerimeter(side));
}

public static void main(String[] args) {
    printSquareInfo(5.0);
    // Area: 25.0
    // Perimeter: 20.0
}
```

---

## Method Overloading

Two methods can have the **same name** as long as their **parameter lists differ** (different number or types of parameters). Java decides which one to call based on the arguments you pass.

```java
public static double area(double radius) {
    return Math.PI * radius * radius;   // circle
}

public static double area(double length, double width) {
    return length * width;              // rectangle
}

public static double area(double base, double height, boolean isTriangle) {
    return 0.5 * base * height;         // triangle
}

// Java picks the right one automatically:
System.out.println(area(5.0));            // circle
System.out.println(area(4.0, 6.0));      // rectangle
System.out.println(area(3.0, 8.0, true));// triangle
```

---

## Scope

Variables declared inside a method are **local** to that method. They do not exist outside it, and they do not affect variables with the same name in other methods.

```java
public static void methodA() {
    int x = 10;
    System.out.println(x);   // 10
}

public static void methodB() {
    int x = 99;              // completely separate variable — same name, different scope
    System.out.println(x);   // 99
}
```

Parameters are also local to their method.

---

## Why `static` — And What Happens Without It

In these examples, every method has `static` because they all live in the same class as `main` and do not require an object instance to operate. When you move to writing classes (Module 15), instance methods drop the `static` keyword — they belong to a specific object. You will see this shift clearly in the OOP modules.

---

## Returning Early

You can `return` from anywhere in a method, not just the end. This is useful for guard clauses:

```java
public static double safeDivide(double a, double b) {
    if (b == 0) {
        System.out.println("Cannot divide by zero.");
        return 0;   // exit early
    }
    return a / b;
}
```

Documenting your methods with Javadoc — including preconditions and postconditions — is covered in [Module 16](../16-javadoc/index.md), once you have written your first classes too.

---

## Exercise

1. Write a method `max(int a, int b, int c)` that returns the largest of three integers.
2. Write an overloaded method `describe(int n)` that prints `"Positive"`, `"Negative"`, or `"Zero"`, and a second version `describe(double d)` that also prints `"Small"` if the absolute value is less than 0.01.
3. Write a method `repeatString(String s, int times)` that returns the string repeated `times` times. For example, `repeatString("ha", 3)` returns `"hahaha"`.
4. Write a `main` that calls all three methods with appropriate test values and prints the results.
