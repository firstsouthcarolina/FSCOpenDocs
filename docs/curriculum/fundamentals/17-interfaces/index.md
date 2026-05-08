---
icon: lucide/unplug
---

# Interfaces

An interface defines behavior a class promises to provide. It is a contract, not a complete object design.

```java
public interface RunnableAction {
    void run();
}
```

Any class that implements the interface must provide the method.

```java
public class PrintAction implements RunnableAction {
    @Override
    public void run() {
        System.out.println("Running action");
    }
}
```

## Interface Variables

```java
RunnableAction action = new PrintAction();
action.run();
```

This is useful when code only cares what an object can do, not exactly what class it is.

## Built-In Interfaces

`Comparable<T>` lets objects define their natural sort order.

```java
public class Team implements Comparable<Team> {
    private final int number;

    public Team(int number) {
        this.number = number;
    }

    @Override
    public int compareTo(Team other) {
        return Integer.compare(number, other.number);
    }
}
```

## Practice

Create an interface named `Scorable` with `int getScore()`. Implement it in two different classes and write a method that prints any `Scorable`.
