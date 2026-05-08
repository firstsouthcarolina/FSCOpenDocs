# Installation and Setup

Before writing any code, you need a working Java development environment. This module walks through installing the JDK and setting up VSCode.

---

## What You Need

Java programs require two things to run:

- **JDK (Java Development Kit)** — contains the compiler that turns your `.java` source files into bytecode, plus the JVM (Java Virtual Machine) that executes that bytecode
- **VSCode** — the editor you write code in, extended with Java support via the Extension Pack for Java

---

## Step 1 — Install the JDK

Download and install **Java 25** (the current Long-Term Support release):

1. Go to [https://adoptium.net](https://adoptium.net) (Eclipse Temurin is free and widely used)
2. Select **Java 25 (LTS)**, your operating system, and your architecture
3. Run the installer — accept the defaults, and make sure **"Add to PATH"** is checked if prompted
4. Verify the install by opening a terminal and running:

```
java -version
javac -version
```

Both should report a version number starting with `25`. If either command is not recognized, the JDK was not added to your PATH — re-run the installer and look for a PATH option.

---

## Step 2 — Install VSCode

1. Download VSCode from [https://code.visualstudio.com](https://code.visualstudio.com)
2. Run the installer
3. During installation on Windows, check **"Add to PATH"** and **"Register Code as an editor"**

---

## Step 3 — Install the Extension Pack for Java

VSCode does not support Java out of the box. The **Extension Pack for Java** by Microsoft adds everything you need.

1. Open VSCode
2. Click the **Extensions** icon in the left sidebar (or press `Ctrl+Shift+X`)
3. Search for `Extension Pack for Java`
4. Click **Install** on the result published by **Microsoft**

This installs six extensions at once:

| Extension | Purpose |
|-----------|---------|
| Language Support for Java | Syntax highlighting, IntelliSense, error detection |
| Debugger for Java | Breakpoints and step-through debugging |
| Test Runner for Java | Running JUnit tests |
| Maven for Java | Build tool support |
| Project Manager for Java | Project creation and management |
| IntelliCode | AI-assisted completions |

!!! tip
    After installing, VSCode may take a minute to index your Java installation. A small loading indicator appears in the bottom status bar while this happens. Wait for it to finish before creating a project.

---

## Step 4 — Create a Java Project

1. Press `Ctrl+Shift+P` to open the **Command Palette**
2. Type `Java: Create Java Project` and select it
3. Choose **No build tools**
4. Select a folder where the project will live (e.g., a `JavaFundamentals` folder on your desktop)
5. Give your project a name (e.g., `HelloWorld`)

VSCode creates this structure:

```
HelloWorld/
├── src/
│   └── App.java       ← your starting file
└── lib/
```

Open `src/App.java`. You will see a starter `main` method already written.

---

## Step 5 — Run Your First Program

VSCode adds a **Run | Debug** CodeLens link directly above any `main` method:

```java
public class App {
    public static void main(String[] args) throws Exception {  // (1)
        System.out.println("Hello, World!");
    }
}
```

1. Click **Run** above the method, or press `F5`
2. Output appears in the **Terminal** panel at the bottom of the screen

!!! note "The `throws Exception` in the starter file"
    VSCode's generated `App.java` includes `throws Exception` on `main`. This is harmless — you can leave it or remove it. The modules in this curriculum omit it for clarity.

---

## Running from the Integrated Terminal

You can also compile and run Java manually from VSCode's built-in terminal. This helps you understand what the IDE is doing behind the scenes.

Open the terminal with `` Ctrl+` `` (backtick), then navigate to the `src` folder:

```
cd path/to/HelloWorld/src
```

Compile:

```
javac App.java
```

This produces `App.class` in the same folder.

Run:

```
java App
```

```
Hello, World!
```

**The two-step compile-run cycle explained:**

```
App.java  →  [javac]  →  App.class  →  [java]  →  Hello, World!
 source         compiler    bytecode      JVM         output
```

When you click **Run** in VSCode, both steps happen automatically. Running from the terminal manually makes the process visible.

!!! tip "Cleaning up `.class` files"
    Compiled `.class` files are generated output — you do not need to commit them to source control. Add `*.class` to your `.gitignore` if you are using Git.

---

## Adding More Classes

As the curriculum progresses, you will create multiple `.java` files. Each new class goes in the `src/` folder.

To create a new class:

1. Right-click the `src/` folder in the Explorer sidebar
2. Select **New File**
3. Name it `ClassName.java` — the filename must match the class name exactly

Or use the Command Palette: `Ctrl+Shift+P` → `Java: New Java File`.

---

## Useful VSCode Shortcuts

| Shortcut | Action |
|----------|--------|
| `F5` | Run (with debugger) |
| `Ctrl+F5` | Run (without debugger — faster for simple programs) |
| `Ctrl+Shift+P` | Open Command Palette |
| `` Ctrl+` `` | Toggle integrated terminal |
| `Ctrl+Shift+X` | Open Extensions panel |
| `Ctrl+Space` | Trigger IntelliSense / autocomplete |
| `F2` | Rename a symbol everywhere |
| `Shift+Alt+F` | Auto-format the current file |

---

## How Java Runs Your Code

```
YourFile.java  →  [javac compiler]  →  YourFile.class  →  [JVM]  →  Output
 source code                           bytecode
```

You write `.java` source files. The compiler turns them into `.class` bytecode. The JVM executes the bytecode. This is why Java is cross-platform — the same `.class` file runs on Windows, macOS, and Linux as long as a JVM is installed.

---

## Notes for This Curriculum

- All code examples use **Java 25** syntax
- Every module builds on the previous one — complete them in order
- File names must match class names exactly, including capitalization (`HelloWorld.java` ↔ `public class HelloWorld`)
- Each module ends with a short exercise — write and run the code yourself; reading alone is not enough
