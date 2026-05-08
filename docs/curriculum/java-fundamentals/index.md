# Java Fundamentals

A self-contained, sequential Java curriculum designed for FIRST Robotics students with no prior programming experience. Modules 00–34 cover Java fundamentals and the standard library (sourced from the FSC spec, CSCE 145, and Java 8+ language additions). Modules 35–44 cover data structures and algorithms (sourced from CSCE 146). Each module builds directly on the previous one and ends with a hands-on exercise.

---

## Module Overview

| # | Module | Description |
|---|--------|-------------|
| 00 | [Installation and Setup](00-installation-and-notes/index.md) | Installing the JDK and IDE, creating a first project, understanding the compile-run cycle |
| 01 | [Hello World](01-hello-world/index.md) | Full line-by-line breakdown of the first Java program, comments, `print` vs `println` |
| 02 | [Primitive Data Types](02-primitive-types/index.md) | All eight primitives, ranges, defaults, and the integer division trap |
| 03 | [Operators, Expressions, and Type Casting](03-operators-and-casting/index.md) | Arithmetic, modulo, compound assignment, increment/decrement, casting, and the `Math` class |
| 04 | [Strings and Simple Object Types](04-strings/index.md) | `String` methods, the `==` vs `.equals()` trap, concatenation, escape characters, `split()`, and character-by-character traversal with `charAt` |
| 05 | [User Input with Scanner](05-user-input/index.md) | Reading keyboard input with `Scanner`, all read methods, and the `nextLine()` after `nextInt()` gotcha |
| 06 | [Conditional Logic](06-conditionals/index.md) | `if`/`else if`/`else`, comparison and logical operators, short-circuit evaluation, De Morgan's Laws, ternary, and `switch` |
| 07 | [Propositional Logic and Rules of Inference](07-propositional-logic/index.md) | *(Optional theory)* propositions, implication, contrapositive/converse/inverse, biconditional, equivalences, tautologies, and the four classic inference rules |
| 08 | [Looping Logic](08-loops/index.md) | `while`, `do-while`, `for`, enhanced `for-each`, and `break`/`continue` |
| 09 | [Enums](09-enums/index.md) | Named constant sets, type safety, enums in `switch`, `.values()`, and `MotorType.kBrushless` explained |
| 10 | [Methods and Functions](10-methods/index.md) | Writing and calling methods, return types, parameters, overloading, scope, and returning early |
| 11 | [Arrays](11-arrays/index.md) | 1D arrays, common traversal patterns, element insert/remove, 2D arrays, diagonal and neighbor patterns |
| 12 | [Quadratic Sorting Algorithms](12-quadratic-sorts/index.md) | Bubble, selection, and insertion sort on `int[]`; sorting properties (stable/in-place/adaptive); when each O(n²) sort is the right choice |
| 13 | [Divide-and-Conquer Sorts](13-divide-and-conquer-sorts/index.md) | Merge sort and quicksort; recursion preview; partition step; pivot selection and worst-case behaviour |
| 14 | [`Arrays.sort` for Primitives](14-arrays-sort-primitives/index.md) | Algorithm comparison; decision guide; dual-pivot quicksort deep-dive; `Arrays.parallelSort` |
| 15 | [Simple Classes](15-simple-classes/index.md) | Class vs. object, fields, constructors, `this`, getters/setters, `toString`, `equals`, the tester pattern, constructor chaining |
| 16 | [Javadoc and Documentation](16-javadoc/index.md) | Method-level and class-level Javadoc tags, inline tags, preconditions/postconditions, a fully documented `Point` class, IDE tooltips, and generating HTML docs |
| 17 | [Reference Semantics](17-reference-semantics/index.md) | Object aliasing, pass-by-value with primitives vs. objects, `null` and `NullPointerException`, reading a stack trace |
| 18 | [Field Modifiers and Composition](18-field-modifiers-composition/index.md) | `private final` fields, `static` fields and constants, composition (has-a relationships) |
| 19 | [Enums with Fields and Methods](19-enums-with-fields/index.md) | Enum constants carrying data via constructors, enum methods, abstract methods per constant, when to use enums as state machines |
| 20 | [Encapsulation](20-encapsulation/index.md) | Hiding internal state, the access modifier table (`private`, `protected`, `public`, package-private), validation patterns, encapsulation + `private final` |
| 21 | [Inheritance](21-inheritance/index.md) | `extends`, `super(...)`, `super.method()`, `@Override`, the Animal/Dog/Cat hierarchy, single inheritance rule, method overriding vs. method overloading |
| 22 | [Static Nested Classes](22-static-nested-classes/index.md) | Declaring a class inside another class, why `static`, when to nest vs. extract, nested records |
| 23 | [Polymorphism and Abstract Classes](23-polymorphism-abstract/index.md) | Polymorphism, dynamic dispatch, abstract classes, abstract methods, and the Array of Shapes example |
| 24 | [Interfaces](24-interfaces/index.md) | The `interface` keyword, `implements`, `default` and `static` interface methods, and common standard library interfaces |
| 25 | [Records](25-records/index.md) | Immutable data carrier classes — auto-generated constructor, accessors, `equals`, `hashCode`, and `toString`; compact constructors; implementing interfaces with records |
| 26 | [Inherited and Polymorphic Object Arrays](26-object-arrays/index.md) | Polymorphic arrays vs. inherited arrays with `instanceof` and downcasting; records in polymorphic arrays |
| 27 | [Sorting Object Arrays](27-object-sorting/index.md) | `Comparable<T>` natural order, `Comparator<T>` with lambdas, stability, Timsort deep-dive, and `Arrays.parallelSort` |
| 28 | [Exception Handling](28-exceptions/index.md) | `try`/`catch`/`finally`, checked vs. unchecked exceptions, and try-with-resources |
| 29 | [File I/O — CSV and TXT](29-file-io/index.md) | Reading and writing plain text and CSV files, parsing with `split()`, and round-trip object serialization |
| 30 | [Lambdas, Functional Interfaces, and Method References](30-lambdas/index.md) | Lambda syntax, `java.util.function` types, capturing variables, and the `::` method reference operator |
| 31 | [Generics](31-generics/index.md) | Type parameters `<T>`, generic classes and methods, bounded type parameters, type erasure, and why generics eliminate unsafe casts |
| 32 | [ArrayList](32-arraylist/index.md) | Dynamic resizable lists, autoboxing, safe removal during iteration, sorting with `Collections`, and lambdas with ArrayList |
| 33 | [Maps and the Collections Framework](33-maps/index.md) | `HashMap`, `TreeMap`, `Map.Entry`, `Set`, the full collections hierarchy, and `Collections` utilities |
| 34 | [Streams and Optional](34-streams/index.md) | Stream pipelines, intermediate and terminal operations, `Collectors`, and `Optional<T>` for null-safe values |
| 35 | [Recursion](35-recursion/index.md) | Base case and recursive case, call stack visualization, factorial/fibonacci/binary search, and pitfalls |
| 36 | [Algorithm Complexity and Big O](36-big-o/index.md) | O(1)/O(log n)/O(n)/O(n log n)/O(n²), growth comparison table, time vs space, and reading code complexity |
| 37 | [Linked Lists](37-linked-lists/index.md) | Node class, singly linked list, insert/delete at head/tail/position, and array vs linked list tradeoffs |
| 38 | [Stacks](38-stacks/index.md) | LIFO structure, array-backed and linked-list-backed implementations, `push`/`pop`/`peek`, and use cases |
| 39 | [Queues](39-queues/index.md) | FIFO structure, circular array and linked-list implementations, `java.util.Queue`, and use cases |
| 40 | [Searching Algorithms](40-searching/index.md) | Linear search, binary search (iterative and recursive), overflow-safe midpoint, and `Arrays.binarySearch` |
| 41 | [Binary Search Trees](41-binary-search-trees/index.md) | Node structure, BST property, insert/search, in-order/pre-order/post-order traversals, and degeneration risk |
| 42 | [Heaps and Priority Queues](42-heaps/index.md) | Min-heap, array representation, heapify up/down, `java.util.PriorityQueue`, and heap sort |
| 43 | [Graphs](43-graphs/index.md) | Terminology, adjacency matrix, adjacency list, and matrix vs list tradeoffs |
| 44 | [Graph Traversals](44-graph-traversals/index.md) | BFS with queue, DFS with recursion/stack, shortest path, and connected components |

---

## Module Details

### Module 00 — Installation and Setup

- What the JDK is and why it is needed (compiler + JVM)
- Installing Java 25 LTS
- IDE setup — Eclipse or IntelliJ IDEA Community
- Creating a first project and running a program
- The compile-run cycle: `.java` → `.class` → JVM

- VSCode run options: **Run** CodeLens above `main`, `F5` (with debugger), `Ctrl+F5` (without debugger), or `javac` + `java` from the integrated terminal
- Useful shortcuts: `Ctrl+Shift+P` (Command Palette), `Ctrl+Space` (IntelliSense), `Shift+Alt+F` (format file)

---

### Module 01 — Hello World

- Every token of `public class HelloWorld { public static void main(String[] args) { ... } }` explained individually
- `public`, `class`, `static`, `void`, `main`, `String[] args` — each one defined
- `System.out.println` vs `System.out.print`
- Semicolons, curly brace pairing, and the filename-must-match-classname rule
- Single-line (`//`) and multi-line (`/* */`) comments
- The compile-run cycle from the student's perspective

---

### Module 02 — Primitive Data Types

- All eight types: `byte`, `short`, `int`, `long`, `float`, `double`, `boolean`, `char`
- Size, range, default value, and a literal example for each
- Literal suffixes: `L` for `long`, `f` for `float`
- Constants with `final` and `ALL_CAPS` naming convention
- **The integer division trap** — `7 / 2 == 3`, not `3.5` — and when to default to `double` instead of `int`

---

### Module 03 — Operators, Expressions, and Type Casting

!!! note "From CSCE 145 — Basic Computation lectures"
    Not in the original spec. Casting and modulo are prerequisite knowledge for the integer division content in Module 02 and for the conditional and loop logic that follows.

- Arithmetic: `+`, `-`, `*`, `/`, `%` — modulo use cases (even/odd, wrapping, divisibility)
- Compound assignment: `+=`, `-=`, `*=`, `/=`, `%=`
- Increment/decrement: prefix vs. postfix `++`/`--`
- Order of operations and when to use parentheses
- Explicit casting — widening (safe, automatic) vs. narrowing (explicit, may lose data)
- The `String + int` concatenation vs. addition trap
- `Math` class: `abs`, `min`, `max`, `pow`, `sqrt`, `floor`, `ceil`, `random`, `PI`

---

### Module 04 — Strings and Simple Object Types

- `String` as a reference type, not a primitive
- Common methods: `length`, `charAt`, `indexOf`, `contains`, `toUpperCase`, `toLowerCase`, `trim`, `replace`, `substring`, `equals`, `equalsIgnoreCase`, `isEmpty`, `split`
- **The `==` trap** — `==` compares memory addresses; `.equals()` compares content
- Escape characters: `\n`, `\t`, `\\`, `\"`
- String immutability — methods return new strings; the original is unchanged
- **`StringBuilder`** — mutable text buffer; `append()` + `toString()`; when to prefer it over `+` in a loop
- Converting between `String` and numeric types: `Integer.parseInt`, `Double.parseDouble`, `String.valueOf`
- `split(",")` preview — returns a `String[]` used directly in the File I/O module
- **String traversal** — indexed `for` with `charAt(i)` from `0` to `length()-1`; counting characters; `toCharArray()` for `for-each` style when index is not needed

---

### Module 05 — User Input with Scanner

!!! note "From CSCE 145 — Lab 00 onward"
    Not in the original spec. CSCE 145 introduces `Scanner` in the very first lab and uses it in every subsequent assignment.

- `import java.util.Scanner` and `new Scanner(System.in)`
- Read methods: `nextInt`, `nextDouble`, `nextLong`, `next`, `nextLine`
- **The `nextLine()` after `nextInt()` gotcha** — the leftover newline and the throwaway-read fix
- Input validation with a `while` loop
- `System.out.print` (not `println`) for prompts
- `sc.close()`

---

### Module 06 — Conditional Logic

- `if` / `else if` / `else` — top-to-bottom evaluation, first match wins
- Comparison operators: `==`, `!=`, `<`, `>`, `<=`, `>=`
- Logical operators: `&&`, `||`, `!` — short-circuit evaluation
- **De Morgan's Laws** — distributing `!` across `&&` and `||`
- Nested `if` statements
- Ternary: `condition ? valueIfTrue : valueIfFalse`
- `switch` statements — `break`, fall-through, `default`, grouping cases
- `switch` works with `int`, `char`, `String`, and `enum`
- When to use `if/else` vs `switch` vs ternary

---

### Module 07 — Propositional Logic and Rules of Inference

!!! info "Optional theory"
    Not required to write Java code — the practical syntax is in Module 06. This module gives the formal vocabulary used in discrete math, the AP CSA exam, and CSCE 145 for analyzing complex conditions and reasoning about correctness.

- Propositions and predicates
- Implication (P → Q) — truth table and the "vacuously true" case
- Contrapositive, converse, and inverse — only the contrapositive is equivalent
- Biconditional (P ↔ Q)
- Logical equivalences — Double Negation, Identity, Domination, Absorption, Contrapositive, De Morgan's
- Tautologies and contradictions — always-true and always-false expressions; how the compiler uses these to flag dead code
- **Rules of inference** — Modus Ponens, Modus Tollens, Hypothetical Syllogism, Disjunctive Syllogism, each with a Java example

---

### Module 08 — Looping Logic

- `while` — condition checked before each iteration; body may never run
- `while (true)` with `break` — the sentinel/menu pattern
- `do-while` — body always runs at least once; classic use: input validation
- `for` — anatomy: initializer, condition, update; counting up/down/by step
- **Enhanced `for-each`** — cleaner iteration when the index is not needed

!!! note "From CSCE 145"
    The `for-each` loop is not in the original spec but is the idiomatic Java way to traverse arrays and collections.

- `break` — exits the loop immediately
- `continue` — skips the rest of the current iteration
- Nested loops — inner loop completes fully for each outer iteration

---

### Module 09 — Enums

- What enums are and the problem they solve (vs `int` or `String` constants)
- Declaring an enum in its own file — `ALL_CAPS` naming convention
- Using enum values: `name()`, `ordinal()`, safe `==` comparison
- Enums in `switch` — compiler warns on missing cases
- Iterating all values with `.values()`
- `MotorType.kBrushless` — a real-world enum from REVLib explained
- `public static final` constants vs. enums — comparison table

---

### Module 10 — Methods and Functions

!!! note "From CSCE 145 — Methods Part 01"
    Not in the original spec and the most significant gap. Constructors, getters, setters, and `toString` are all methods. Students who reach Module 15 without understanding methods will not be able to follow the class design explanations.

- Method anatomy: access modifier, `static`, return type, name, parameters, `return`
- `void` vs. typed return
- Parameters vs. arguments
- Methods calling other methods
- Method overloading — same name, different parameter list
- Scope — local variables do not exist outside the method that declares them
- Why `static` here, and the preview of what changes in Module 15
- Returning early with guard clauses

---

### Module 11 — Arrays

- Declaration: `new int[n]` and `{a, b, c}` initializer syntax
- Zero-based indexing, `.length` property, `ArrayIndexOutOfBoundsException`
- Traversal with `for` and `for-each`
- Common patterns: sum/average, min/max, linear search, copy
- **Arrays of objects** — every slot starts `null`; `NullPointerException` if used before individual construction
- **Insert at a position** — shift elements right (backward loop) to make room; write new value
- **Remove at a position** — shift elements left (forward loop) to fill the gap; zero the last slot
- Passing arrays to methods — arrays are objects, passed by reference

**2D arrays:**

!!! note "From CSCE 145 — Lab 04: Matrix Addition"
    The original spec says "Arrays" without specifying dimension. CSCE 145 Lab 04 uses 2D arrays for matrix addition.

- `int[][] grid = new int[rows][cols]`
- `grid[row][col]` access, `grid.length` for rows, `grid[row].length` for columns
- Nested `for` loop traversal
- **Main diagonal** — `matrix[i][i]` single-loop pattern
- **Neighbor access** — direction arrays `dRow`/`dCol`; bounds check before accessing

---

### Module 12 — Quadratic Sorting Algorithms

!!! note "From CSCE 145 — Homework 03: Sorting Rectangles"
    CSCE 145 requires students to implement sorting from scratch. The three classic O(n²) algorithms are covered here, immediately after arrays.

- **Sorting properties** — time, space, stability, in-place, adaptive
- **Bubble sort** — compare adjacent pairs, swap; O(n²); stable; O(n) best with early-exit flag
- **Selection sort** — find min, swap to front; O(n²); few swaps; not stable; useful when writes are expensive
- **Insertion sort** — shift elements right, insert key; O(n) best (nearly sorted); stable; adaptive; the basis for hybrid sorts
- Comparison among the three; when each one is the right choice

---

### Module 13 — Divide-and-Conquer Sorts

- Divide-and-conquer concept — split, solve sub-problems, combine
- Recursion preview — note that both algorithms call themselves; full recursion treatment in Module 35
- **Merge sort** — recursive split, merge two sorted halves with two pointers; O(n log n) always; O(n) space; stable
- **Quicksort** — pivot, partition, recurse; O(n log n) average, O(n²) worst; in-place
- Worked partition trace; visualizing the recursion tree
- Median-of-three pivot selection to avoid the worst case
- Merge sort vs. quicksort — comparison table and when to choose each

---

### Module 14 — `Arrays.sort` for Primitives

- Full algorithm comparison across all five hand-rolled sorts plus dual-pivot quicksort
- Decision guide — when each algorithm is the right choice
- **Dual-pivot quicksort** — Vladimir Yaroslavskiy's algorithm (Java 7+); two pivots create three partitions; better cache locality; ~`n log₃ n` comparisons
- Falls back to insertion sort for sub-arrays under ~47 elements
- All primitive overloads — `int[]`, `long[]`, `double[]`, etc.
- Range sort — `[fromIndex, toIndex)`
- Sorting descending: sort ascending then reverse, or use streams
- `Arrays.parallelSort` — parallel merge sort for large arrays

---

### Module 15 — Simple Classes

- Class vs. object — blueprint vs. instance
- A complete worked `Dog` class
- `private` instance variables (preview of encapsulation in Module 20)
- Default and parameterized constructors
- `this` keyword — distinguishing fields from parameters
- **Constructor chaining with `this()`**
- Getters (accessors) and setters (mutators) with validation
- `@Override toString()` — called automatically in `println` and concatenation
- `@Override equals()` — content comparison vs. memory address comparison
- The tester class pattern

!!! note "From CSCE 145 — Lab 07: Bicycle Builder"
    Composition (covered in Module 18) is not in the original spec. CSCE 145 Lab 07 builds a `Bicycle` from `Wheel` and `Frame` objects. Students encounter composition constantly in WPILib robot code.

---

### Module 16 — Javadoc and Documentation

!!! note "From CSCE 145 — Methods Part 01 lecture (documentation practices)"
    Javadoc is the standard contract documentation system; preconditions/postconditions are taught in CSCE 145 alongside methods. Placed here so the worked example can document a complete class.

- Preconditions, postconditions, and class invariants — what each one means and where they live
- **Javadoc format** — `/** */` double-star block comment; first sentence is the summary sentence
- **Method-level tags** — `@param`, `@return`, `@throws`
- **Class-level tags** — `@author`, `@version`, `@since`, `@deprecated`, `@see`
- **Inline tags** — `{@code}`, `{@link}`, `{@literal}`
- A fully documented `Point` class — class-level block plus per-method blocks for constructor and methods
- Preconditions and `@throws` in Javadoc — combining contract documentation with the tag system
- IDE integration — hover tooltips drawn from Javadoc source
- Generating HTML with the `javadoc` CLI tool
- What deserves a Javadoc comment — decision table by item type

---

### Module 17 — Reference Semantics

- **Object aliasing** — two variables referencing the same object; `==` returns `true`; mutation through one is visible through the other
- `==` vs. `.equals()` on objects — reference equality vs. content equality
- **Pass-by-value** — primitives copy the value; objects copy the reference (field mutations visible, parameter reassignment not)
- Memory diagrams showing what actually happens during a method call
- **`null` and `NullPointerException`** — `null` means "points to nothing"; NPE thrown when calling a method on `null`
- Common causes of NPE — unchecked return value, uninitialized array slots, uninitialized field, chained calls
- Guarding against NPE — `if (x != null)`, literal-first `.equals()`, filling object arrays before use
- Reading a stack trace — top frame, line number, helpful NPE messages (Java 14+)

---

### Module 18 — Field Modifiers and Composition

- **`private final` fields** — assigned once at construction; prevents accidental reassignment; ideal for hardware ports, IDs, injected dependencies
- `final` doesn't make objects immutable — locks the reference, not the contents
- **Static fields** — class-level; shared across all instances; counters, shared configuration, constants
- When NOT to use static — global mutable state pitfalls
- Static vs instance — side-by-side comparison
- **Composition** — fields of another class type (has-a relationship)
- Composition vs inheritance comparison table — "has-a" vs "is-a"
- Composition + `private final` — the WPILib subsystem pattern

---

### Module 19 — Enums with Fields and Methods

- The problem with simple enums — constants without data
- **Enum fields and constructors** — each constant carries data via a private constructor
- `DriveMode` example — `ARCADE`, `TANK`, `SLOW` each with display name and stick-count
- **Enum methods** — `Planet` example with `surfaceGravity` and `surfaceWeight` computed from stored data
- **Abstract methods per constant** — `Operation` enum with `ADD`, `SUBTRACT`, `MULTIPLY` each providing its own `apply` body
- Enum fields vs. `public static final` constants — comparison table

---

### Module 20 — Encapsulation

- Why encapsulation matters — public fields cannot enforce invariants
- The validation pattern — silent reject vs. throwing an exception
- **Access modifier table** — `private`, `protected`, `public`, package-private
- Choosing the right modifier — start `private`, open up only with reason
- Encapsulation + `private final` — the strongest possible protection
- Why not just make everything public? — invariants, refactoring, validation, debugging

---

### Module 21 — Inheritance

- The `Animal` / `Dog` / `Cat` worked example
- **Key keywords** — `extends`, `super(...)`, `super.method()`, `@Override`
- `super(...)` — calling the parent constructor (must be the first line)
- `@Override` — compiler-checked overriding catches misspellings
- Rules — single inheritance, inherited members, no inherited constructors, the `Object` chain
- `protected` vs. `private` — design tradeoffs for subclass access
- **Method overriding vs. method overloading** — comparison table; runtime vs compile-time resolution
- Hierarchy design tips — start from the data, prefer composition for "has-a"

---

### Module 22 — Static Nested Classes

- Why nested classes exist — connecting `private` encapsulation and composition to type-level visibility
- Declaring a class inside another class with `static`
- Why `static` — no implicit reference to the enclosing object
- Reference syntax: `Outer.Nested`
- When to nest vs. extract to a separate file
- Static nested vs. top-level — comparison table
- `private static` nested classes — implementation details hidden from outside
- Nested records as a more concise alternative for pure data bundles

---

### Module 23 — Polymorphism and Abstract Classes

- **Polymorphism** — a parent-type reference holds a child object; correct method chosen at runtime (dynamic dispatch)
- Why polymorphism matters — comparing code with and without a shared parent type
- **Abstract classes** — cannot be instantiated; abstract methods are contracts subclasses must fulfill
- `abstract` methods — declared but not implemented; all concrete subclasses must provide a body
- When to use abstract vs. concrete parent
- **Array of Shapes** — `abstract Shape` with `Circle`, `Rectangle`, `Triangle`; one loop, no type checking needed

---

### Module 24 — Interfaces

- What an interface is — a contract without implementation
- Declaring an interface: method signatures, implicit `public abstract`
- `implements` — a class satisfying the contract; compiler enforces every method
- Multiple interfaces on one class
- Interface as a type — polymorphism without inheritance
- `default` methods — shared implementation in the interface itself (Java 8+)
- `static` interface methods — utility methods that belong to the interface
- Common standard library interfaces: `Comparable<T>`, `Comparator<T>`, `Iterable<T>`, `Runnable`, `AutoCloseable`
- **`Comparable<T>` worked example** — `Student.compareTo` returning negative/zero/positive
- Interface vs. abstract class — comparison table; when to use each

---

### Module 25 — Records

- What a record is — an immutable data carrier; auto-generates constructor, accessors, `equals`, `hashCode`, `toString`
- Record declaration syntax: `record Name(Type field, ...) { }`
- Accessor naming: `x()` not `getX()`; all fields are `private final`
- **Compact constructors** — validation and normalization before field assignment
- Adding custom methods to a record (no new fields allowed)
- **Records and interfaces** — a record can `implement` any interface; connects to Module 24
- `Comparable<T>` example — sorting a record array with `Arrays.sort`
- Records vs. classes — comparison table
- When to use a record vs. a full class

---

### Module 26 — Inherited and Polymorphic Object Arrays

- **Polymorphic array** — uniform behaviour; no `instanceof` needed; highly extensible
- **Inherited array with `instanceof`** — access parent methods polymorphically + call subclass-specific methods via downcast
- `instanceof` checks the runtime type before casting
- `ClassCastException` and how to avoid it
- Side-by-side comparison table
- Why heavy `instanceof` use is a design smell
- **Records in polymorphic arrays** — a record implementing an interface fits into any array declared as that interface type
- Pattern variable `instanceof` syntax (Java 16+)

---

### Module 27 — Sorting Object Arrays

- Why object sorting differs from primitive sorting — equality and order must be defined explicitly
- **`Comparable<T>`** — natural ordering built into the class; `compareTo` contract; `Double.compare` for floating-point
- **`Comparator<T>`** — external ordering; lambdas as comparators; `Comparator.comparing` / `thenComparing`
- `Arrays.sort(Object[])` and `Arrays.sort(T[], Comparator<T>)`
- **Stability** — equal elements keep original relative order; why objects require stable sort but primitives do not
- **Timsort** — natural runs, binary insertion sort, merge with galloping mode; O(n) best case on sorted data
- `Arrays.parallelSort`
- All `Arrays.sort` overloads — primitive vs object, full vs range

---

### Module 28 — Exception Handling

!!! note "From CSCE 145 — Exceptions lecture"
    Not in the original spec but a hard prerequisite for Module 29. `IOException` is a checked exception — the Java compiler refuses to compile File I/O code that does not handle or declare it.

- What exceptions are and what a stack trace tells you
- `try` / `catch` / `finally` — structure and execution order
- Checked (`IOException`) vs. unchecked (`NullPointerException`, `ArrayIndexOutOfBoundsException`) exceptions
- Common exception types and their typical causes
- Catching multiple exception types — order matters (most specific first)
- `e.getMessage()` and `e.printStackTrace()`
- `throws` declaration — delegating handling to the caller
- **Try-with-resources** — the correct pattern for anything involving a file or stream

---

### Module 29 — File I/O — CSV and TXT

- Writing to a TXT file with `PrintWriter` + `FileWriter`
- Append mode: `new FileWriter(path, true)`
- Reading a TXT file line by line with `Scanner(new File(path))`
- Writing a CSV file — header row + data rows
- Reading and parsing a CSV file with `split(",")`, `Integer.parseInt`, `Double.parseDouble`
- `printf` / `String.format` for aligned output
- Full round-trip example — write objects to CSV, read them back
- Loading a CSV directly into an object array
- Common pitfalls: wrong path, trailing newlines, whitespace in fields, not closing before reading

!!! warning "No other file types"
    This module covers TXT and CSV only. JSON, XML, binary, and other formats are out of scope.

---

### Module 30 — Lambdas, Functional Interfaces, and Method References

- The problem: anonymous classes are verbose for single-method behaviour
- Functional interface — exactly one abstract method; `@FunctionalInterface` annotation
- Lambda syntax: `(params) -> expression` and `(params) -> { body; }`
- Zero, one, and multiple parameter forms; type inference
- `java.util.function` package: `Runnable`, `Supplier<T>`, `Consumer<T>`, `Function<T,R>`, `Predicate<T>`, `BiFunction<T,U,R>`, primitive variants
- Capturing variables — effectively final rule
- Lambdas with arrays: `Arrays.sort(names, (a, b) -> a.compareTo(b))`
- Method references: static, bound instance, unbound instance, constructor (`ClassName::new`)
- Storing and passing lambdas as values

---

### Module 31 — Generics

- The problem without generics — raw `Object` types require casts and lose compiler safety
- **Type parameters** — `<T>` as a placeholder resolved at the call site
- Generic class: `Box<T>`
- Type parameter naming conventions: `T`, `E`, `K`/`V`, `N`, `R`
- **Generic methods** — type parameter declared before the return type; can infer `T` from arguments
- **Bounded type parameters** — `<T extends Comparable<T>>` constrains which types are accepted
- `Pair<A, B>` — generic class with two type parameters
- Records with type parameters
- **Type erasure** — generics exist only at compile time; runtime sees raw types

---

### Module 32 — ArrayList

!!! note "From CSCE 146 — Java Review"
    Not in the original FSC spec, which restricted Module 11 to fixed-size arrays. Introduced here as a bridge to the CSCE 146 data structures content.

- `ArrayList<T>` — dynamic resizable list backed by an array
- Core methods: `add`, `get`, `set`, `remove`, `size`, `contains`, `indexOf`, `clear`, `isEmpty`
- Traversal with `for-each` and indexed `for`
- Autoboxing and unboxing — `int` ↔ `Integer`
- **Safe removal during iteration** — forward-skip bug; backward iteration; decrement after remove
- **`Collections.sort()`** — natural order; uses `Comparable<T>` automatically
- **Lambdas with ArrayList** — `list.sort((a, b) -> ...)`, `list.forEach(...)`
- `ArrayList` vs array — comparison
- Converting between array and `ArrayList`

---

### Module 33 — Maps and the Collections Framework

- `Map<K, V>` — key-value storage; O(1) average lookup
- `HashMap<K, V>` — most common implementation; no guaranteed order
- Core operations: `put`, `get`, `getOrDefault`, `containsKey`, `remove`, `size`, `isEmpty`
- Iterating: `keySet()`, `values()`, `entrySet()`, `Map.Entry<K,V>`
- **`equals` and `hashCode` contract** — how `HashMap` uses both; why overriding one without the other breaks key lookup; `Objects.hash(...)` idiom
- `Map.of(...)` factory methods
- `LinkedHashMap` (insertion order) and `TreeMap` (sorted key order)
- `Set<T>` and `HashSet<T>` — no duplicates, no index
- The full collections hierarchy: `List`, `Set`, `Queue`, `Map`
- `Collections` utility class

---

### Module 34 — Streams and Optional

- Streams are pipelines, not data structures — lazy evaluation, single-use
- Getting a stream: `collection.stream()`, `Stream.of(...)`, `IntStream.range(...)`
- Intermediate operations: `filter`, `map`, `mapToInt`, `sorted`, `distinct`, `limit`, `skip`, `peek`
- Terminal operations: `toList`, `collect`, `forEach`, `count`, `findFirst`, `anyMatch`, `allMatch`, `min`, `max`, `reduce`
- `Collectors`: `toList()`, `toMap()`, `groupingBy()`, `joining()`
- `reduce` — folding all elements into one value
- Streams vs. loops — when each is clearer
- `Optional<T>` — wrapping a possibly-absent value
- `Optional` creation: `of`, `ofNullable`, `empty`
- `Optional` consumption: `isPresent`, `isEmpty`, `get`, `orElse`, `orElseGet`, `orElseThrow`, `ifPresent`, `map`, `filter`

---

### Module 35 — Recursion

- Two rules: base case (stops recursion) and recursive case (smaller input)
- Call stack — each call pushes a frame; `StackOverflowError` when it overflows
- Factorial — trace showing frames building up and unwinding
- Fibonacci — two base cases; warning about O(2ⁿ) exponential growth
- Sum of an array recursively
- Recursive binary search — same algorithm as Module 40 (iterative), expressed recursively
- Reverse a string recursively
- Recursion vs iteration — when each is cleaner; when iteration is safer
- Common pitfalls: missing base case, base case never reached, wrong base case value

---

### Module 36 — Algorithm Complexity and Big O

- Why input size matters — motivating example with different sizes
- Big O notation — growth rate in the worst case, ignoring constants
- O(1), O(log n), O(n), O(n log n), O(n²), O(2ⁿ) — definitions and examples
- Growth comparison table — O(1) to O(n²) across n = 10, 100, 1000, 10000, 1000000
- Dropping constants and lower-order terms
- Best, average, and worst case — linear search as the example
- Space complexity — O(1) vs O(n) auxiliary space
- Identifying complexity from code patterns
- Practical thresholds — rule-of-thumb table for each complexity class

---

### Module 37 — Linked Lists

- `Node` class: `data` field and `next` reference
- Singly linked list with `head` pointer and `size`
- Traversal using a `current` variable
- Insert at head — O(1); insert at tail — O(n); insert at position
- Remove at head — O(1); remove by value
- Search (`contains`) and indexed access (`get`)
- `java.util.LinkedList` as the production version
- Array vs linked list tradeoff table

---

### Module 38 — Stacks

- LIFO (Last In, First Out) principle
- Array-backed stack — `top` index, push/pop, overflow check
- Linked-list-backed stack — no fixed capacity; head is the top; both O(1)
- `java.util.Deque` / `ArrayDeque` as the preferred production stack
- Use cases: undo, balanced bracket checking, JVM call stack, expression evaluation
- Balanced brackets example

---

### Module 39 — Queues

- FIFO (First In, First Out) principle
- Linked-list queue — `front` and `back` references; both enqueue and dequeue O(1)
- Circular array queue — `(index + 1) % capacity` wrapping
- `java.util.Queue` interface — `offer`/`poll`/`peek` (null-safe) vs `add`/`remove`/`element`
- `ArrayDeque` as the preferred implementation
- Use cases: task scheduling, BFS graph traversal, robot command queues, keyboard buffers
- Stack vs Queue comparison

---

### Module 40 — Searching Algorithms

- **Linear search** — scan every element; O(n); no precondition
- **Binary search** — halve the search space; O(log n); array must be sorted
- Iterative binary search — `low + (high - low) / 2` overflow-safe midpoint
- Recursive binary search — full treatment of the algorithm introduced as a recursion example in Module 35
- Tracing binary search step by step
- `Arrays.binarySearch` — negative return on not-found; must sort first
- Binary search on `String[]` using natural order
- Linear vs binary search tradeoff table

---

### Module 41 — Binary Search Trees

- BST property — left subtree < node < right subtree
- `BSTNode` class with `left`, `right`, `data`
- Recursive `insert` — returns the updated subtree root
- Recursive `contains` — eliminates half the tree at each step
- **In-order traversal** — produces elements in sorted order
- **Pre-order traversal** — root before children
- **Post-order traversal** — children before root
- `min()` and `max()` — leftmost and rightmost nodes
- Complexity: O(log n) for balanced, O(n) for degenerate (sorted insertion)
- Preview of self-balancing trees and `java.util.TreeMap`/`TreeSet`

---

### Module 42 — Heaps and Priority Queues

- Min-heap properties: complete binary tree + parent ≤ children
- Array storage of heap — parent/child index formulas (`2i+1`, `2i+2`, `(i-1)/2`)
- **Insert with heapify-up** — add at end, bubble up by swapping with parent
- **Remove-min with heapify-down** — move last to root, push down to smaller child
- O(log n) insert and remove-min; O(1) peek
- `java.util.PriorityQueue` — min-heap by default; `Collections.reverseOrder()` for max-heap
- Custom comparator with lambda for domain objects
- **Heap sort** — build max-heap, extract max repeatedly; O(n log n), O(1) space
- Use cases: priority scheduling, Dijkstra's algorithm, event simulation, robot command queues

---

### Module 43 — Graphs

- Vertices, edges, directed vs undirected, weighted, path, cycle, degree, connected
- **Adjacency matrix** — 2D array; O(V²) space; O(1) edge lookup
- **Adjacency list** — `ArrayList` of `ArrayList`; O(V+E) space; efficient for sparse graphs
- Matrix vs list comparison table
- `addEdge` / `removeEdge` helpers for both representations
- `degree()` and `maxDegreeVertex()` using the adjacency list

---

### Module 44 — Graph Traversals

- **Breadth-First Search (BFS)** — queue-based; visits level by level; finds shortest unweighted path
- **Depth-First Search (DFS)** — recursive (call stack) and iterative (explicit stack)
- BFS vs DFS comparison table
- Detecting connectivity — run BFS/DFS, count visited vertices
- Robotics applications: pathfinding on a field map, network routing, dependency resolution

---

## Sources

### Primary — Student Specification

The core module sequence and all emphasis areas were defined by the FSC curriculum spec:

- Hello World with a full token-by-token explanation
- All eight primitive types with explicit coverage of the `double`-over-`int` default for division
- `String` as a simple object type
- Conditional logic (`if`/`else if`/`else`, ternary, `switch`)
- Looping logic (`while`, `do-while`, `for`)
- Arrays (no `List` or `ArrayList`)
- OOP across multiple dedicated modules — simple classes; encapsulation; inheritance; polymorphism and abstract classes; and inherited vs. polymorphic object arrays
- File I/O restricted to CSV and TXT only

### Supplementary — CSCE 145: Algorithmic Design I (University of South Carolina)

Several modules were added to fill prerequisite gaps identified by cross-referencing the [CSCE 145 course material](https://www.cse.sc.edu/~shephejj/csce145/) taught by Dr. J.J. Shepherd:

| Module | Source material | Gap filled |
|--------|----------------|------------|
| **03 — Operators, Expressions, and Type Casting** | Basic Computation lectures | Casting and modulo are required to understand integer division and write meaningful conditions |
| **05 — User Input with Scanner** | Lab 00 onward | Used in every CSCE 145 assignment; without it, all code examples are static demos |
| **07 — Propositional Logic and Rules of Inference** | Discrete math content used throughout the course | Optional theoretical foundation for boolean logic; appears in AP CSA exam preparation |
| **10 — Methods and Functions** | Methods Part 01 lecture | Constructors and getters/setters are methods — OOP is incomprehensible without this foundation |
| **16 — Javadoc and Documentation** | Methods Part 01 lecture (documentation practices) | Javadoc is the standard contract documentation system; preconditions/postconditions are taught alongside methods. Placed after Simple Classes so the worked example can document a complete class |
| **28 — Exception Handling** | Exceptions lecture | `IOException` is checked; the compiler requires it to be handled before File I/O will compile |

Additional content informed by specific CSCE 145 labs and homeworks:

- **2D arrays** (Module 11) — Lab 04: Matrix Addition
- **Quadratic sorting** (Module 12) — Homework 03: Sorting Rectangles
- **Composition** (Module 18) — Lab 07: Bicycle Builder
- **`instanceof` and downcasting** (Module 26) — Lab 08: Fleet of Vehicles and Lab 09: Bunch of Lines

### Supplementary — CSCE 146: Algorithmic Design II (University of South Carolina)

Modules 32, 35–43 are sourced from [CSCE 146](https://www.cse.sc.edu/~shephejj/csce146/) taught by Dr. J.J. Shepherd, the direct continuation of CSCE 145. The course covers data structures and algorithms that are directly applicable to FIRST Robotics programming. Modules 31 (Generics), 33–34 (Maps and Streams) are standard Java API topics added for completeness and are not from CSCE 146. Within the CSCE 146 block, Recursion (Module 35) and Algorithm Complexity (Module 36) are placed before the data structures so students have the analytical vocabulary before they need it.

| Modules | Source material |
|---------|----------------|
| **31 — Generics** | Standard Java type system (added to support ArrayList and data structure generics) |
| **32 — ArrayList** | Java review (bridge between CSCE 145 and 146) |
| **35 — Recursion** | Recursion |
| **36 — Algorithm Complexity and Big O** | Searching and sorting analysis |
| **37 — Linked Lists** | Linked Lists Part 01, 02, 03 |
| **38 — Stacks** | Stacks |
| **39 — Queues** | Queues |
| **40 — Searching Algorithms** | Searching and sorting |
| **41 — Binary Search Trees** | Binary search trees (two-part) |
| **42 — Heaps and Priority Queues** | Heaps (two-part) |
| **43 — Graphs** | Graph algorithms |
| **44 — Graph Traversals** | Graph algorithms |
