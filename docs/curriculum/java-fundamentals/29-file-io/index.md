# File I/O — CSV and TXT

Reading from and writing to files lets your program persist data between runs. This module covers plain text (`.txt`) and comma-separated values (`.csv`) files only.

---

## Imports You Need

```java
import java.io.File;
import java.io.FileWriter;
import java.io.PrintWriter;
import java.io.IOException;
import java.util.Scanner;
```

Or use the wildcard `import java.io.*;` to bring in all `java.io` classes at once.

---

## Writing a TXT File

```java
try (PrintWriter writer = new PrintWriter(new FileWriter("output.txt"))) {
    writer.println("Hello, File!");
    writer.println("Second line.");
    writer.println("Third line.");
} catch (IOException e) {
    System.out.println("Could not write file: " + e.getMessage());
}
```

- `new FileWriter("output.txt")` creates or **overwrites** the file at that path
- `PrintWriter` wraps `FileWriter` and provides `println()`, `print()`, and `printf()`
- Try-with-resources closes the file automatically when the block exits

The file path is **relative to the project root** when running from an IDE. `"output.txt"` creates `output.txt` directly in your project folder.

### What the File Contains

```
Hello, File!
Second line.
Third line.
```

---

## Appending to a TXT File

Pass `true` as the second argument to `FileWriter` to enable **append mode** — new content is added after whatever is already in the file instead of overwriting it.

```java
try (PrintWriter writer = new PrintWriter(new FileWriter("output.txt", true))) {
    writer.println("This line is appended.");
} catch (IOException e) {
    System.out.println("Could not append to file: " + e.getMessage());
}
```

---

## Reading a TXT File

```java
try (Scanner sc = new Scanner(new File("output.txt"))) {
    while (sc.hasNextLine()) {
        String line = sc.nextLine();
        System.out.println(line);
    }
} catch (IOException e) {
    System.out.println("Could not read file: " + e.getMessage());
}
```

- `new File("output.txt")` creates a `File` object pointing to the path
- `Scanner` on a `File` reads the file line by line
- `sc.hasNextLine()` returns `false` when there are no more lines

---

## Writing a CSV File

A CSV (comma-separated values) file stores tabular data. Each line is a row; commas separate the columns.

```java
try (PrintWriter writer = new PrintWriter(new FileWriter("students.csv"))) {
    writer.println("Name,Age,Score");       // header row
    writer.println("Alice,20,95.5");
    writer.println("Bob,22,87.3");
    writer.println("Charlie,19,91.0");
} catch (IOException e) {
    System.out.println("Could not write CSV: " + e.getMessage());
}
```

### What the File Contains

```
Name,Age,Score
Alice,20,95.5
Bob,22,87.3
Charlie,19,91.0
```

This file opens correctly in Excel, Google Sheets, or any spreadsheet application.

---

## Reading and Parsing a CSV File

Use `String.split(",")` to break each line into its individual fields:

```java
try (Scanner sc = new Scanner(new File("students.csv"))) {
    sc.nextLine();   // skip the header row

    while (sc.hasNextLine()) {
        String line = sc.nextLine();
        String[] fields = line.split(",");

        String name  = fields[0];
        int age      = Integer.parseInt(fields[1]);
        double score = Double.parseDouble(fields[2]);

        System.out.printf("%-10s | Age: %2d | Score: %.1f%n", name, age, score);
    }
} catch (IOException e) {
    System.out.println("Could not read CSV: " + e.getMessage());
}
```

```
Alice      | Age: 20 | Score: 95.5
Bob        | Age: 22 | Score: 87.3
Charlie    | Age: 19 | Score: 91.0
```

- `fields[0]` is the first column, `fields[1]` the second, and so on
- `Integer.parseInt()` and `Double.parseDouble()` convert the string fields to numbers
- `String.format` / `printf` can help align printed output neatly

---

## `printf` and `String.format` for Formatted Output

```java
// %-10s — left-aligned string, padded to 10 characters
// %2d    — integer, minimum 2 digits wide
// %.1f   — float with 1 decimal place
// %n     — platform-independent newline

System.out.printf("%-10s | %2d | %.1f%n", "Alice", 20, 95.5);
// Alice      | 20 | 95.5
```

---

## Full Round-Trip Example — Write Then Read

```java
import java.io.*;
import java.util.Scanner;

public class FileRoundTrip {
    public static void main(String[] args) {
        String filename = "robots.csv";

        // Write
        try (PrintWriter writer = new PrintWriter(new FileWriter(filename))) {
            writer.println("Name,Team,MaxSpeed");
            writer.println("Titan,1234,4.5");
            writer.println("Atlas,5678,3.8");
            writer.println("Nexus,9012,5.1");
        } catch (IOException e) {
            System.out.println("Write failed: " + e.getMessage());
            return;
        }

        // Read back
        try (Scanner sc = new Scanner(new File(filename))) {
            sc.nextLine();  // skip header
            while (sc.hasNextLine()) {
                String[] f = sc.nextLine().split(",");
                String name   = f[0];
                int    team   = Integer.parseInt(f[1]);
                double speed  = Double.parseDouble(f[2]);
                System.out.printf("Robot: %-8s  Team: %4d  Speed: %.1f m/s%n",
                    name, team, speed);
            }
        } catch (IOException e) {
            System.out.println("Read failed: " + e.getMessage());
        }
    }
}
```

```
Robot: Titan     Team: 1234  Speed: 4.5 m/s
Robot: Atlas     Team: 5678  Speed: 3.8 m/s
Robot: Nexus     Team: 9012  Speed: 5.1 m/s
```

---

## Connecting File I/O to Your Objects

Reading a CSV into an array of objects is a common real-world pattern:

```java
// Assumes Robot class from Module 15 exists
Robot[] robots = new Robot[3];
int index = 0;

try (Scanner sc = new Scanner(new File("robots.csv"))) {
    sc.nextLine();  // skip header
    while (sc.hasNextLine() && index < robots.length) {
        String[] f = sc.nextLine().split(",");
        robots[index] = new Robot(f[0], Integer.parseInt(f[1]),
                                  Double.parseDouble(f[2]));
        index++;
    }
} catch (IOException e) {
    System.out.println("Could not load robots: " + e.getMessage());
}

for (Robot r : robots) {
    if (r != null) System.out.println(r);
}
```

---

## Common Pitfalls

| Problem | Cause | Fix |
|---------|-------|-----|
| `FileNotFoundException` | Wrong path or file does not exist | Check the path; print the absolute path with `new File("name").getAbsolutePath()` |
| Empty line at end of file causing `split()` errors | Trailing newline in file | Check `line.isEmpty()` before splitting |
| Numbers not parsing | Extra whitespace in CSV | Call `.trim()` on fields before parsing |
| File is empty after writing | Writer not closed before reading | Always close via try-with-resources before reading |

---

## Exercise

1. Write a program that asks the user to enter five student names and their scores (use a loop). Store them in parallel arrays. Write them to a CSV file called `grades.csv` with a header row `Name,Score`. Then read the file back and print each record, flagging any score below 60 with `FAIL`.

2. Modify the program so that if `grades.csv` already exists, new records are **appended** rather than overwriting the file. (Hint: check for the file with `new File("grades.csv").exists()`.)

3. Write a method `loadRobots(String filename)` that reads a CSV file and returns a `Robot[]`. Write a corresponding `saveRobots(Robot[] robots, String filename)` method. Demonstrate both from `main`.
