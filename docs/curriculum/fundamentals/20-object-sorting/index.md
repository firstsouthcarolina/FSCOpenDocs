---
icon: lucide/list-ordered
---

# Object Sorting

Objects need a rule before Java can sort them. That rule usually comes from `Comparable` or `Comparator`.

## Comparable

Use `Comparable<T>` when a class has one natural order.

```java
public record Team(int number, String name) implements Comparable<Team> {
    @Override
    public int compareTo(Team other) {
        return Integer.compare(number, other.number);
    }
}
```

```java
Team[] teams = {
    new Team(281, "GreenVillains"),
    new Team(4451, "Robotz Garage")
};

Arrays.sort(teams);
```

## Comparator

Use `Comparator<T>` when you want to sort the same objects in different ways.

```java
Arrays.sort(teams, Comparator.comparing(Team::name));
```

You need imports:

```java
import java.util.Arrays;
import java.util.Comparator;
```

## Compare Correctly

Prefer helper methods like `Integer.compare` and `Double.compare` instead of subtracting values. Subtraction can overflow or lose detail.

## Practice

Create a `MatchScore` record with team number and score. Sort an array by score from highest to lowest.
