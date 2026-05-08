---
icon: lucide/file-input
---

# File IO

File IO lets programs read and write data outside memory. Robot teams use files for logs, configuration, generated paths, and test data.

## Paths

Use `Path` to represent file locations.

```java
import java.nio.file.Path;

Path path = Path.of("data", "values.txt");
```

`Path.of(...)` avoids hard-coding operating-system-specific separators.

## Reading a File

```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;

public static List<String> readLines(Path path) throws IOException {
    return Files.readAllLines(path);
}
```

File operations can fail because the file is missing, locked, or unreadable. Java reports many of these failures with checked exceptions.

## Writing a File

```java
public static void writeLines(Path path, List<String> lines) throws IOException {
    Files.write(path, lines);
}
```

This replaces the file contents. Be careful before writing over data you need.

## Try-With-Resources

Use try-with-resources for objects that must be closed.

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    String line = reader.readLine();
    System.out.println(line);
}
```

## Practice

Write a program that reads a text file of voltage readings, parses each line as a `double`, and prints the average.
