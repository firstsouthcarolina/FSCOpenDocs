# Graph Traversals

This module builds directly on [Module 43](../43-graphs/index.md) — you need to understand graph representations before traversing one. Both traversals here use the adjacency list format and rely on data structures you have already built: BFS uses a **queue** ([Module 39](../39-queues/index.md)) and DFS uses either recursion ([Module 35](../35-recursion/index.md)) or an explicit **stack** ([Module 38](../38-stacks/index.md)).

---

## Breadth-First Search (BFS)

BFS explores vertices **level by level** from a starting vertex — all neighbors first, then their neighbors, and so on. It uses a **queue** (FIFO) to process vertices in the order they are discovered.

```java
import java.util.ArrayDeque;
import java.util.Queue;

public static void bfs(ArrayList<ArrayList<Integer>> graph, int start) {
    boolean[] visited = new boolean[graph.size()];
    Queue<Integer> queue = new ArrayDeque<>();

    visited[start] = true;
    queue.offer(start);

    while (!queue.isEmpty()) {
        int vertex = queue.poll();
        System.out.print(vertex + " ");

        for (int neighbor : graph.get(vertex)) {
            if (!visited[neighbor]) {
                visited[neighbor] = true;
                queue.offer(neighbor);
            }
        }
    }
    System.out.println();
}
```

BFS on the example graph starting at 0: `0 1 2 3 4`

BFS always finds the **shortest path** (in terms of number of edges) from the start vertex to any reachable vertex.

---

## Depth-First Search (DFS)

DFS explores as **deep as possible** along each path before backtracking. It uses a **stack** (LIFO) — either explicitly or via recursion.

### Recursive DFS

```java
public static void dfs(ArrayList<ArrayList<Integer>> graph, int vertex, boolean[] visited) {
    visited[vertex] = true;
    System.out.print(vertex + " ");

    for (int neighbor : graph.get(vertex)) {
        if (!visited[neighbor]) {
            dfs(graph, neighbor, visited);
        }
    }
}

// Call:
boolean[] visited = new boolean[graph.size()];
dfs(graph, 0, visited);
```

### Iterative DFS (using an explicit stack)

```java
public static void dfsIterative(ArrayList<ArrayList<Integer>> graph, int start) {
    boolean[] visited = new boolean[graph.size()];
    java.util.Deque<Integer> stack = new ArrayDeque<>();

    stack.push(start);

    while (!stack.isEmpty()) {
        int vertex = stack.pop();
        if (visited[vertex]) continue;
        visited[vertex] = true;
        System.out.print(vertex + " ");

        for (int neighbor : graph.get(vertex)) {
            if (!visited[neighbor]) {
                stack.push(neighbor);
            }
        }
    }
    System.out.println();
}
```

---

## BFS vs DFS

| | BFS | DFS |
|--|-----|-----|
| Data structure | Queue | Stack (or recursion) |
| Order | Level by level | As deep as possible |
| Finds shortest path? | Yes (unweighted) | No |
| Memory | O(width of graph) | O(depth of graph) |
| Use cases | Shortest path, level traversal | Cycle detection, topological sort, maze solving |

---

## Detecting Whether a Graph Is Connected

Use BFS or DFS from any starting vertex and count how many vertices are visited. If all vertices are visited, the graph is connected.

```java
public static boolean isConnected(ArrayList<ArrayList<Integer>> graph) {
    boolean[] visited = new boolean[graph.size()];
    dfs(graph, 0, visited);
    for (boolean v : visited) {
        if (!v) return false;
    }
    return true;
}
```

---

## Full Example

```java
import java.util.ArrayList;
import java.util.ArrayDeque;
import java.util.Queue;

public class GraphDemo {
    public static void main(String[] args) {
        int n = 6;
        ArrayList<ArrayList<Integer>> graph = new ArrayList<>();
        for (int i = 0; i < n; i++) graph.add(new ArrayList<>());

        // Undirected edges
        addEdge(graph, 0, 1);
        addEdge(graph, 0, 2);
        addEdge(graph, 1, 3);
        addEdge(graph, 2, 4);
        addEdge(graph, 3, 5);
        addEdge(graph, 4, 5);

        System.out.print("BFS from 0: ");
        bfs(graph, 0);       // 0 1 2 3 4 5

        System.out.print("DFS from 0: ");
        boolean[] visited = new boolean[n];
        dfs(graph, 0, visited);   // 0 1 3 5 4 2  (order depends on adjacency list)
    }

    static void addEdge(ArrayList<ArrayList<Integer>> g, int u, int v) {
        g.get(u).add(v);
        g.get(v).add(u);
    }

    static void bfs(ArrayList<ArrayList<Integer>> graph, int start) {
        boolean[] visited = new boolean[graph.size()];
        Queue<Integer> queue = new ArrayDeque<>();
        visited[start] = true;
        queue.offer(start);
        while (!queue.isEmpty()) {
            int v = queue.poll();
            System.out.print(v + " ");
            for (int nb : graph.get(v))
                if (!visited[nb]) { visited[nb] = true; queue.offer(nb); }
        }
        System.out.println();
    }

    static void dfs(ArrayList<ArrayList<Integer>> graph, int v, boolean[] visited) {
        visited[v] = true;
        System.out.print(v + " ");
        for (int nb : graph.get(v))
            if (!visited[nb]) dfs(graph, nb, visited);
    }
}
```

---

## Real-World Applications

**Pathfinding** — Robot autonomous navigation models the field as a graph. BFS finds the shortest path in terms of steps; weighted graph algorithms (Dijkstra's, A\*) find the shortest path by distance.

**Dependency resolution** — Build systems use directed graphs to determine the order in which files must be compiled (topological sort).

**Social networks** — "Friend of a friend" analysis uses BFS to find all users within a certain number of connections.

**Network routing** — Internet routers use graph algorithms to find the fastest path for packets.

---

## Exercise

1. Build an undirected adjacency-list graph with 7 vertices representing a small map. Add edges connecting them into a realistic network. Run BFS and DFS from vertex 0 and compare the order in which vertices are visited.

2. Write a method `shortestPathLength(graph, start, end)` that uses BFS to return the number of edges in the shortest path from `start` to `end`, or `-1` if no path exists. (Hint: store a `distance[]` array alongside `visited[]`.)

3. Write a method `countComponents(graph)` that returns the number of connected components in an undirected graph. (A graph with disconnected parts has more than one component — start a new BFS/DFS for every unvisited vertex.)
