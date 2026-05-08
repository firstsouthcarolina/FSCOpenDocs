# Graphs

A graph is a collection of **vertices** (nodes) connected by **edges**. Unlike trees, graphs can have cycles, disconnected components, and edges that go in both directions — making them the most general structure in this curriculum.

This module covers how to represent a graph in code. [Module 44](../44-graph-traversals/index.md) covers how to traverse one.

---

## Terminology

| Term | Definition |
|------|-----------|
| **Vertex (node)** | A point in the graph |
| **Edge** | A connection between two vertices |
| **Directed graph** | Edges have a direction (A → B does not imply B → A) |
| **Undirected graph** | Edges go both ways (A — B means A → B and B → A) |
| **Weighted graph** | Each edge has a numeric cost or distance |
| **Adjacent** | Two vertices connected by an edge |
| **Path** | A sequence of vertices connected by edges |
| **Cycle** | A path that starts and ends at the same vertex |
| **Connected** | Every vertex can reach every other vertex |
| **Degree** | The number of edges a vertex has |

---

## Graph Representations

### Adjacency Matrix

A 2D array where `matrix[i][j] = 1` (or the weight) if there is an edge from vertex `i` to vertex `j`, and `0` otherwise.

```java
// 5 vertices (0–4)
int[][] matrix = new int[5][5];
matrix[0][1] = 1;   // edge from 0 to 1
matrix[0][2] = 1;   // edge from 0 to 2
matrix[1][3] = 1;
matrix[2][3] = 1;
matrix[3][4] = 1;
```

```
     0  1  2  3  4
  0 [0, 1, 1, 0, 0]
  1 [0, 0, 0, 1, 0]
  2 [0, 0, 0, 1, 0]
  3 [0, 0, 0, 0, 1]
  4 [0, 0, 0, 0, 0]
```

For an undirected graph, set both `matrix[i][j]` and `matrix[j][i]`.

**Pros:** O(1) edge lookup, simple to implement.  
**Cons:** Always uses O(V²) space even for sparse graphs with few edges.

### Adjacency List

An array (or `ArrayList`) where each index holds a list of that vertex's neighbors.

```java
import java.util.ArrayList;

int numVertices = 5;
ArrayList<ArrayList<Integer>> adjList = new ArrayList<>();
for (int i = 0; i < numVertices; i++) {
    adjList.add(new ArrayList<>());
}

// Add edges (undirected)
adjList.get(0).add(1);  adjList.get(1).add(0);
adjList.get(0).add(2);  adjList.get(2).add(0);
adjList.get(1).add(3);  adjList.get(3).add(1);
adjList.get(2).add(3);  adjList.get(3).add(2);
adjList.get(3).add(4);  adjList.get(4).add(3);
```

```
0: [1, 2]
1: [0, 3]
2: [0, 3]
3: [1, 2, 4]
4: [3]
```

**Pros:** O(V + E) space — efficient for sparse graphs.  
**Cons:** O(degree) edge lookup (must scan the list).

---

## Adjacency Matrix vs Adjacency List

| | Adjacency Matrix | Adjacency List |
|--|-----------------|---------------|
| Space | O(V²) | O(V + E) |
| Edge lookup (does A→B exist?) | O(1) | O(degree of A) |
| Find all neighbors of a vertex | O(V) | O(degree) |
| Best for | Dense graphs | Sparse graphs |

Most real-world graphs (road networks, social networks, robot maps) are **sparse** — adjacency lists are usually the right choice.

---

## Exercise

1. Build the same undirected graph with 6 vertices and edges `{0-1, 0-2, 1-3, 2-3, 3-4, 4-5}` as both an adjacency matrix and an adjacency list. Print each representation. Verify that both show the same connectivity.

2. Write helper methods `addEdge(int[][] matrix, int u, int v)` and `removeEdge(int[][] matrix, int u, int v)` for the adjacency matrix (undirected — set both `[u][v]` and `[v][u]`). Write equivalent helpers for the adjacency list version. Test adding and removing an edge on both.

3. Write a method `degree(ArrayList<ArrayList<Integer>> graph, int v)` that returns the number of neighbors of vertex `v`. Then write `maxDegreeVertex(ArrayList<ArrayList<Integer>> graph)` that returns the index of the vertex with the most edges. Test it on a graph where at least one vertex has three or more neighbors.
