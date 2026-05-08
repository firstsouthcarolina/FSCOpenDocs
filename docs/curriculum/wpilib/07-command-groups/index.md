---
icon: lucide/workflow
---

# Command Groups

Command groups combine smaller commands into larger routines.

## Sequential

Sequential groups run one command after another.

```java
Commands.sequence(
    intake.runCommand(0.8).withTimeout(1.0),
    new InstantCommand(() -> intake.stop(), intake)
);
```

## Parallel

Parallel groups run commands at the same time.

```java
Commands.parallel(
    shooter.spinUpCommand(),
    intake.feedCommand()
);
```

The group finishes based on the exact parallel type. Read the method name carefully.

## Race and Deadline

- **Race** ends when any command ends.
- **Deadline** ends when the deadline command ends and interrupts the others.

```java
Commands.deadline(
    drivetrain.driveForwardCommand().withTimeout(2.0),
    shooter.spinUpCommand()
);
```

## Timeouts

Timeouts are useful safety limits.

```java
command.withTimeout(3.0);
```

Do not rely on timeouts as the only stop condition for mechanisms that can jam or hit hard stops.

## Practice

Create a command group that:

1. Spins up a shooter.
2. Waits half a second.
3. Runs the feeder for one second.
4. Stops shooter and feeder.
