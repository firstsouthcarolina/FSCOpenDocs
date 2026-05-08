# Command Groups and Composition

Individual commands handle single actions. Command groups combine them into sequences, parallel operations, or timed steps. The KitBot's `LaunchSequence` is a command group: spin up the launcher, wait for it to reach speed, then feed game pieces through.

---

## Why Command Groups?

Without command groups, you would need to manually track state inside commands — "has the launcher been spinning for 1 second yet?" — or write a single large command that mixes unrelated concerns. Command groups keep each action isolated and composable.

---

## SequentialCommandGroup

Runs commands **one after another**. The next command starts only after the previous one finishes.

```java
import edu.wpi.first.wpilibj2.command.SequentialCommandGroup;

new SequentialCommandGroup(
    new CommandA(),
    new CommandB(),
    new CommandC()
);
```

```
time ──▶
[CommandA runs] [CommandB runs] [CommandC runs]
```

A command "finishes" when its `isFinished()` returns `true`. If a command never returns `true`, the sequence stalls there.

---

## withTimeout()

Since many commands run indefinitely (`isFinished()` returns `false`), you need a way to end them after a set time. `.withTimeout(seconds)` wraps a command and cancels it after the specified duration:

```java
new SpinUp(fuelSubsystem).withTimeout(1.0)
```

This creates a new command that runs `SpinUp` for exactly 1 second, then stops it and moves to the next command in the sequence.

---

## LaunchSequence.java — Full File

```java
package frc.robot.commands;

import edu.wpi.first.wpilibj2.command.SequentialCommandGroup;
import frc.robot.subsystems.CANFuelSubsystem;

public class LaunchSequence extends SequentialCommandGroup {

    public LaunchSequence(CANFuelSubsystem fuelSubsystem) {
        addCommands(
            new SpinUp(fuelSubsystem).withTimeout(1.0),
            new Launch(fuelSubsystem)
        );
    }
}
```

**Step 1 — `SpinUp` for 1 second:**  
Runs the launcher motor at full speed while holding the feeder back. After 1 second, the launcher wheel is up to speed and the command ends.

**Step 2 — `Launch` indefinitely:**  
Runs both motors at launch voltages. The feeder now pushes game pieces into the spinning launcher. This command runs until the operator releases the right trigger, which cancels `LaunchSequence` entirely.

!!! note "The commented SpinUp"
    The KitBot repository has `SpinUp.withTimeout(1.0)` commented out in `LaunchSequence`. The version here restores it as intended — uncomment it to enable the spin-up delay.

---

## Inline Command Composition

You do not always need a named class for a command group. WPILib provides fluent methods directly on `Command`:

```java
// Sequential: A then B
commandA.andThen(commandB)

// Sequential with timeout on A: A (max 1s) then B
commandA.withTimeout(1.0).andThen(commandB)

// Parallel: A and B simultaneously, group ends when both finish
commandA.alongWith(commandB)

// Race: A and B simultaneously, group ends when either finishes
commandA.raceWith(commandB)

// Sequential using only inline methods — equivalent to LaunchSequence:
new SpinUp(fuelSubsystem).withTimeout(1.0).andThen(new Launch(fuelSubsystem))
```

The KitBot uses a named `LaunchSequence` class rather than inline composition. Both are valid; the named class is easier to read and reuse in multiple places.

---

## ParallelCommandGroup

Runs commands **simultaneously**. The group ends when all commands finish.

```java
import edu.wpi.first.wpilibj2.command.ParallelCommandGroup;

new ParallelCommandGroup(
    new CommandA(),
    new CommandB()
);
// Ends when both A and B have finished
```

```
time ──▶
[CommandA runs ──────────]
[CommandB runs ──]
                          ← group ends when last finishes (CommandA)
```

---

## ParallelRaceGroup

Runs commands simultaneously. The group ends when **any one** command finishes — the rest are cancelled.

```java
import edu.wpi.first.wpilibj2.command.ParallelRaceGroup;

new ParallelRaceGroup(
    new CommandA(),
    new CommandB()
);
// Ends when either A or B finishes first
```

```
time ──▶
[CommandA runs ──────────] ← cancelled
[CommandB runs ──]
                  ← group ends when first finishes (CommandB)
```

`.raceWith()` is the inline equivalent of `ParallelRaceGroup`.

---

## Requirements in Command Groups

Command groups inherit all requirements from their member commands. `LaunchSequence` requires `fuelSubsystem` because `SpinUp` and `Launch` both require it. You never call `addRequirements` on the group itself — it collects them automatically.

Two commands in the same parallel group cannot share a requirement. WPILib will throw an error at construction time if you try.

---

## ExampleAuto Preview

The autonomous routine in the next module is also a `SequentialCommandGroup`:

```java
new SequentialCommandGroup(
    new AutoDrive(driveSubsystem, 0.5, 0).withTimeout(0.25),   // drive forward 0.25s
    new Launch(fuelSubsystem).withTimeout(10.0)                  // launch for 10s
)
```

Two commands, two timeouts, all chained in sequence. No state variables, no manual timers — the framework handles timing.

---

## Choosing the Right Composition

| Goal | Use |
|------|-----|
| Run A, then B | `SequentialCommandGroup` or `.andThen()` |
| Run A and B simultaneously; wait for both | `ParallelCommandGroup` or `.alongWith()` |
| Run A and B simultaneously; stop when either ends | `ParallelRaceGroup` or `.raceWith()` |
| Run A for at most N seconds | `.withTimeout(N)` |
| Name the group for reuse | Extend `SequentialCommandGroup` or `ParallelCommandGroup` |
| Use once inline | Fluent composition (`.andThen()`, `.withTimeout()`) |

---

## Exercise

1. Write `LaunchSequence.java` with the `SpinUp` + timeout + `Launch` sequence. Build and test: hold the right trigger in simulation and verify that `SpinUp` runs first.
2. Change the `SpinUp` timeout from `1.0` to `0.1`. What effect does this have on a real robot? When would a longer spin-up time matter?
3. Write an inline command that drives the robot forward at 50% for 2 seconds, then stops. Use `.withTimeout()` and `.andThen()` on `AutoDrive` instances. Do not create a named class.
