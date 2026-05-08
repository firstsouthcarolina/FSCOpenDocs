# Propositional Logic and Rules of Inference

!!! info "Optional — For the Mathematically Inclined"
    This module is theoretical background, not Java syntax. The `&&`, `||`, and `!` operators from [Module 06](../06-conditionals/index.md) already work without it. The material here gives you the formal vocabulary used in discrete math, the AP CSA exam, and CSCE 145 — useful for analyzing complex conditions, spotting dead code, and reasoning about correctness, but not required to write working programs. If you only need the practical syntax, skip ahead to [Module 08 — Looping Logic](../08-loops/index.md).

The `&&`, `||`, and `!` operators you have been using are Java's implementation of **propositional logic** — a branch of discrete mathematics that gives formal rules for reasoning about true/false statements.

---

## Propositional Logic

### Propositions

A **proposition** is any statement that is unambiguously true or false. In Java, a `boolean` expression is a proposition.

| Statement | Proposition? |
|-----------|-------------|
| `score >= 90` (score is known) | Yes |
| `"Turn left."` (command, not statement) | No |
| `x > 5` (x is unknown) | No — a **predicate** (becomes a proposition once x is given) |

Propositional variables are written P, Q, R. In Java these are your `boolean` variables.

---

### Implication (If–Then)

The **implication** P → Q ("if P then Q") is the logical form of an `if` statement. P is the **antecedent** (hypothesis); Q is the **consequent** (conclusion).

| P | Q | P → Q |
|---|---|-------|
| T | T | T |
| T | F | **F** |
| F | T | T |
| F | F | T |

The implication is **false only when P is true but Q is false** — a promise was made but not kept. When P is false, the implication is vacuously true: a condition that never fires can never be violated.

```java
// "If score >= 90, then grade is A"
if (score >= 90) {
    grade = 'A';   // Q follows whenever P holds
}
```

---

### Contrapositive, Converse, and Inverse

Given P → Q, three related forms exist:

| Form | Statement | Equivalent to P → Q? |
|------|-----------|----------------------|
| **Contrapositive** | ¬Q → ¬P | **Yes — always** |
| Converse | Q → P | No |
| Inverse | ¬P → ¬Q | No |

The contrapositive is the only one guaranteed to be equivalent. This means proving "if not Q then not P" is just as good as proving "if P then Q."

```
"If the motor is running, the encoder is spinning."
≡  "If the encoder is not spinning, the motor is not running."
```

---

### Biconditional (If and Only If)

P ↔ Q is true when P and Q have the **same** truth value — both true or both false.

| P | Q | P ↔ Q |
|---|---|-------|
| T | T | T |
| T | F | F |
| F | T | F |
| F | F | T |

```java
// "The motor is enabled if and only if the button is held"
motorEnabled = buttonHeld;   // P ↔ Q — they stay in lockstep
```

---

### Logical Equivalences

Two expressions are **logically equivalent** (≡) when they have identical truth tables for every input. De Morgan's Laws (covered in Module 06) are equivalences. Here are the most useful ones:

| Law | Form |
|-----|------|
| Double Negation | `!!p` ≡ `p` |
| Identity | `p && true` ≡ `p` &nbsp;&nbsp; `p \|\| false` ≡ `p` |
| Domination | `p && false` ≡ `false` &nbsp;&nbsp; `p \|\| true` ≡ `true` |
| Absorption | `p && (p \|\| q)` ≡ `p` &nbsp;&nbsp; `p \|\| (p && q)` ≡ `p` |
| Contrapositive | `p → q` ≡ `!q → !p` |
| De Morgan's | `!(p && q)` ≡ `!p \|\| !q` &nbsp;&nbsp; `!(p \|\| q)` ≡ `!p && !q` |

The Domination law explains why the Java compiler warns about unreachable code — `if (x && false)` can never be true.

---

### Tautologies and Contradictions

A **tautology** is an expression that is always true. A **contradiction** is always false.

```java
// Tautology — law of excluded middle: P || !P
System.out.println(x > 5 || !(x > 5));   // always true

// Contradiction: P && !P — impossible
System.out.println(x > 5 && !(x > 5));   // always false

if (isEnabled && !isEnabled) {
    // dead code — this block can never execute
}
```

---

## Rules of Inference

A **valid argument** is one where the conclusion *must* be true whenever all premises are true, regardless of what the specific values are. The following four rules are the workhorses of logical reasoning.

### Modus Ponens — Affirming the Antecedent

```
Premise 1:  P → Q
Premise 2:  P
─────────────────
Conclusion: Q
```

"If the sensor detects an obstacle, the robot stops. The sensor detects an obstacle. Therefore, the robot stops."

```java
boolean obstacleDetected = true;

if (obstacleDetected) {       // P is true
    robot.stop();             // Q follows
}
```

This is the inference rule Java's `if` statement executes every time it runs — P is true, so enter the block (Q).

---

### Modus Tollens — Denying the Consequent

```
Premise 1:  P → Q
Premise 2:  ¬Q
─────────────────
Conclusion: ¬P
```

"If the motor is running, the current draw is above zero. The current draw is zero. Therefore, the motor is not running."

```java
boolean currentAboveZero = false;   // ¬Q

// Since P → Q and ¬Q, we conclude ¬P:
// motorRunning must be false
if (!currentAboveZero) {
    System.out.println("Motor is not running.");   // ¬P
}
```

Modus Tollens is the contrapositive applied as inference. It is how you diagnose bugs: "if my code were correct, the test would pass — the test failed, so my code is not correct."

---

### Hypothetical Syllogism — Chain Rule

```
Premise 1:  P → Q
Premise 2:  Q → R
─────────────────
Conclusion: P → R
```

"If the button is pressed, the command runs. If the command runs, the motor spins. Therefore, if the button is pressed, the motor spins."

```java
boolean buttonPressed = true;
boolean commandRuns   = buttonPressed;   // P → Q
boolean motorSpins    = commandRuns;     // Q → R
// By transitivity: motorSpins follows from buttonPressed  (P → R)
```

Hypothetical Syllogism describes how function call chains work — each method's postcondition becomes the next method's precondition.

---

### Disjunctive Syllogism — Process of Elimination

```
Premise 1:  P ∨ Q
Premise 2:  ¬P
─────────────────
Conclusion: Q
```

"The robot is in autonomous mode or teleop mode. It is not in autonomous mode. Therefore, it is in teleop mode."

```java
boolean isAutonomous = false;

if (!isAutonomous) {         // ¬P
    // P ∨ Q was true, ¬P eliminates P, so Q must hold:
    System.out.println("Must be in teleop mode.");
}
```

---

## Exercise

1. For each inference rule below, identify which rule applies and write the Java `if` statement that represents it:
    - "If `speed > 50`, the alarm triggers. The alarm is not triggering. What can you conclude about `speed`?"
    - "If `isCharging` is true, `batteryLevel` is increasing. `isCharging` is true. What follows?"
    - "If `sensorA` fires, `sensorB` fires. If `sensorB` fires, `motorStop` is called. Both premises hold — what happens when `sensorA` fires?"
    - "Either `mode` is `AUTO` or `mode` is `TELEOP`. `mode` is not `AUTO`. What do we know?"

2. Build a truth table for `(p && q) || (!p && !q)`. Identify the simpler equivalent expression. (Hint: look at the rows where the result is true — what do `p` and `q` have in common in those rows?)

3. Use logical equivalences to simplify each expression. Show your work step by step:
    - `!(a && !b)`
    - `(a || b) && (a || !b)`
    - `!(a || (a && b))`

4. Write a Java `boolean` expression that is a tautology using two variables `p` and `q`. Then write one that is a contradiction. Run them in a small program — confirm one always prints `true` and the other always prints `false`, regardless of the values you assign.

5. Given the implication `if (door.isLocked()) door.unlock()`, write its contrapositive in plain English. Then describe a scenario where applying the contrapositive helps you debug a problem (a door that ended up unlocked when it should have stayed locked).
