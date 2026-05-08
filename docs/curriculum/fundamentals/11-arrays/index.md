---
icon: lucide/brackets
---

# Arrays

An array stores a fixed number of values of the same type.

```java
int[] motorPorts = {1, 2, 3, 4};
double[] speeds = new double[4];
```

## Indexes

Arrays are zero-indexed.

```java
int firstPort = motorPorts[0];
int secondPort = motorPorts[1];
```

The last valid index is `array.length - 1`.

## Looping Through Arrays

```java
for (int i = 0; i < motorPorts.length; i++) {
    System.out.println(motorPorts[i]);
}
```

Use an enhanced `for` loop when you do not need the index.

```java
for (int port : motorPorts) {
    System.out.println(port);
}
```

## Arrays of Arrays

A two-dimensional array is an array whose elements are arrays.

```java
int[][] grid = {
    {1, 2, 3},
    {4, 5, 6}
};

System.out.println(grid[1][2]); // 6
```

## Practice

Create an array of five drivetrain speed samples. Print the average speed.
