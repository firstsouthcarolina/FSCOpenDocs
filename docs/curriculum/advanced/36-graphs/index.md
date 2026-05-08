---
icon: lucide/share-2
---

# Graphs

A graph stores relationships between nodes. Nodes are also called vertices, and connections are called edges.

## Examples

Graphs can model:

- Field waypoints connected by paths.
- Robot actions with dependencies.
- Network devices.
- Social connections between teams.

## Adjacency List

An adjacency list maps each node to its neighbors.

```java
Map<String, List<String>> graph = new HashMap<>();
graph.put("Start", List.of("A", "B"));
graph.put("A", List.of("Score"));
graph.put("B", List.of("Score"));
graph.put("Score", List.of());
```

## Breadth-First Search

BFS explores by distance from the start node.

```java
public static boolean bfs(Map<String, List<String>> graph, String start, String target) {
    Queue<String> queue = new ArrayDeque<>();
    Set<String> visited = new HashSet<>();

    queue.add(start);
    visited.add(start);

    while (!queue.isEmpty()) {
        String current = queue.remove();

        if (current.equals(target)) {
            return true;
        }

        for (String neighbor : graph.getOrDefault(current, List.of())) {
            if (visited.add(neighbor)) {
                queue.add(neighbor);
            }
        }
    }

    return false;
}
```

## Depth-First Search

DFS explores one path deeply before backing up. It can be written recursively or with a stack.

## Practice

Model five field locations as a graph. Use BFS to check whether the robot can travel from the starting location to a scoring location.
