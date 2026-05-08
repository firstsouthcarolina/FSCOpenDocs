---
icon: lucide/map
---

# Maps

A map stores key-value pairs. Use a map when you want to look up a value by a meaningful key.

```java
import java.util.HashMap;
import java.util.Map;

Map<String, Integer> canIds = new HashMap<>();
canIds.put("intake", 13);
canIds.put("shooter", 21);
```

## Reading Values

```java
int intakeId = canIds.get("intake");
```

`get` returns `null` if the key is missing for object values. For primitive wrappers, unboxing a missing value can crash with a `NullPointerException`.

## Safer Lookup

```java
if (canIds.containsKey("climber")) {
    System.out.println(canIds.get("climber"));
}
```

Or use a default:

```java
int climberId = canIds.getOrDefault("climber", -1);
```

## Iterating

```java
for (Map.Entry<String, Integer> entry : canIds.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

## HashMap vs TreeMap

- `HashMap` is usually faster and has no guaranteed order.
- `TreeMap` keeps keys sorted.

## Practice

Create a map from mechanism names to current draw. Print every mechanism drawing more than `30.0` amps.
