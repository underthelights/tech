---
layout: article
title: Graph
tags: alg
category: alg
picture_frame: shadow
use_math: true
---

Algorithm Design & Analysis  
**[Sub6] Graph**

<!--more-->

# [주제 6] Graph Algorithms

## 0. Basic Things to Know about Graph as a CSE Undergraduate

- Definitions and representations
- Graph traversal algorithms
  - Depth-first search
  - Breadth-first search
- Connectivity
  - Simple connectivity
  - Strong connectivity
  - Biconnectivity
  - Transitive closure
- Biconnected component algorithms
- Shortest path algorithm
  - All-pairs shortest path algorithm
  - Single-source shortest path algorithm
- Minimum spanning tree algorithm
  - Prim’s minimum spanning tree algorithm
  - Kruskal’s minimum spanning tree algorithm

## 1. Definitions

- An (*undirected*, *simple*) *graph* *G* is defined to be a pair of (*V*, *E*) , where *V* is a non-empty finite set of elements called *vertices*, and *E* is a finite set of unordered pairs of distinct elements of *V* called edges.
  - *G* = (*V*, *E*) = (*V*(*G*), *E*(*G*))
  - Graphs that allow loops and multiple edges are often called a *general graphs*.
- A (*simple*) *digraph* *D* is defined to be a pair (*V*, *A*), where *V* is a non-empty finite set of elements called *vertices*, and *A* is a finite set of ordered pairs of distinct elements of *V* called (*directed*) *edges* or (*directed*) *arcs*.
- A *weighted graph* is a graph in which a number, called the *weight*, is assigned to each edge.

$V = \{ 1, 2, 3, 4, 5, 6 \}$
$E = \{ (1, 2), (1, 5), (2, 3), (2, 5), (3, 4), (4, 5), (4, 6) \}$

From Wikipedia

- A *subgraph* of a graph *G* is simply a graph, all of whose vertices belong to *V*(*G*) and all of whose edges belong to *E*(*G*).

- Adjacency and incidence

  - Two vertices *v* and *w* of a graph *G* are said to be *adjacent* if there is an edge joining them.
  - Two distinct edges of *G* are *adjacent* if they have at least one vertex in common.
  - The vertices *v* and *w* are then said to be *incident* to such an edge.
  - The *degree* of a vertex *v* of *G* is the number of edges incident to *v*.

- **Walk, trail, circuit, path, and cycle**

  - A *walk* (or *edge-sequence*) is an alternating sequence of vertices and edges, starting and ending at a vertex, in which each edge is adjacent in the sequence to its two endpoints.

  - The *length* of a walk is the number of edges in it.

  - A *trail* is a walk in which all the edges are distinct from one another.

  - A walk is *closed* if it starts and ends at the same vertex.

  - A *circuit* is a trail that is closed.

  - A *path* is a walk in which all the vertices are distinct from one another.

  - A *cycle* is a path containing at least one edge with an exception that the first and last vertices coincide.

    > The definitions differ by various textbooks!!!

  - An Eulerian trail is a trail that visits every edge exactly once.

  - An Eulerian circuit is an Eulerian trail that starts and ends on the same vertex.

  - A Hamiltonian path is a path that visits each vertex exactly once. 

  - A Hamiltonian cycle is a Hamiltonian path that is a cycle.

    -  An Eulerian circuit exists in a connected graph G if the degree of every vertex is even, and can be found in O(|E|) time.
    -  Determining whether such paths and cycles exist in graphs is the Hamiltonian path problem, which is NP-complete.

  

|                   | Walk        | Trail      | Circuit    | Path       | Cycle      |
| ----------------- | ----------- | ---------- | ---------- | ---------- | ---------- |
| Openness          | Open/Closed | Open       | Closed     | Open       | Closed     |
| Vertex repetition | Allowed     | Allowed    | Allowed    | Disallowed | Disallowed |
| Edge repetition   | Allowed     | Disallowed | Disallowed | Disallowed | Disallowed |

- Examples of graphs
  - A *null graph* is a graph whose edge-set is empty.
  - A *regular graph* is a graph in which each vertex has the same degree.
  - A *complete graph* is a graph in which each pair of vertices is joined by an edge.
  - A *bipartite graph* is a graph in which its vertex set can be partitioned into two sets *V*1 and *V*2, in such a way that every edge of the graph joins a vertex of *V*1 to a vertex of *V*2.
  - A *connected graph* is an undirected graph, in which, given any pair of vertices *v* and *w*, there is a path from *v* to *w*.
    • An arbitrary graph can split up into disjoint connected subgraphs called *connected* *components*.
  - A *tree* is a connected graph with no cycles.
  - A *forest* is a graph with no cycles.

- Graph isomorphism
  - Two graphs *G*1 and *G*2 are *isomorphic* if there is a one-to-one correspondence between the vertices of *G*1 and those of *G*2 with the property that the number of edges joining any two vertices of *G*1 is equal to the number of edges joining the corresponding vertices of *G*2.

### Graph Representation

#### Graph Representation 1: Adjacency List

In mathematics

#### Graph Representation 2: Adjacency Matrix

In mathematics

```c++
for int A[3][5];
  A[i][j]
= *(A[i] + j)
= (*(A + i))[j]
= *((*(A + i)) + j)
= *(&A[0][0] + 5*i + j)
```

- In C

- Various Costs for a Graph G = (V, E) 

| --                        | Adjacency list | Adjacency matrix       |
| ------------------------- | -------------- | ---------------------- |
| Space                     | $O(|V|+|E|)$   | $O(|V|^2)$             |
| Initialize empty          | $O(|V|)$       | $O(|V|^2)$             |
| Copy after initialization | $O(|E|)$       | $O(|V|^2)$             |
| Destroy                   | $O(|E|)$       | $O(|V|)$ or $O(1)$     |
| Insert vertex u           | $O(1)$         | $O(|V|)$ or $O(|V|^2)$ |
| Insert Edge (u,v)         | $O(1)$         | $O(1)$                 |
| Remove vertex u           | $O(|E|)$       | $O(|V|^2)$             |
| Find/Remove edge (u,v)    | $O(|V|)$       | $O(1)$                 |
| Are u, v adjacent?        | $O(|V|)$       | $O(1)$                 |

|                         | Adjacency list | Adjacency matrix |
| ----------------------- | -------------- | ---------------- |
| Is v isolated?          | $O(1)$         | $O(|V|)$         |
| find a path from u to v | $O(|V|+|E|)$   | $O(|V|^2)$       |



### Some Problems Related to Graph Search

- Cycle detection
  - Does a given graph have any cycle?
- Simple path
  - Given two vertices, is there a path in the graph that connects them?
- Simple connectivity
  - Is a graph connected?
- Two-way Euler tour
  - Find a path that uses all the edges in a graph exactly twice – once in each direction.
- Spanning tree
  - Given a connected graph with n vertices, find a set of n-1 edges that connects the vertices.
- Vertex search
  - How many vertices are in the same connected component as a given vertex?
- Two-colorability, bipartiteness, odd cycle
  - Is there a way to assign one of two colors to each vertex of a graph such that no edge connects two vertices of the same color? 
  - Is a given graph bipartite?
  - Does a given graph have a cycle of odd length?
- st-connectivity
  - What is the minimum number of edges whose removal will separate two given vertices s and t in a graph?
- General connectivity
  - Is a graph k-connected?
  - Is a given graph k-edge connected?
  - What is the edge connectivity and the vertex connectivity of a given graph?
- Shortest path
  - Find a shortest path in the graph from v to w.
- Single-source shortest paths
  - Find shortest paths connecting a given vertex v with each other vertex in the graph.

## 2. Graph Search

### 1) Graph Search 1: Depth-First Search (DFS)

- 자료구조 과목에서 배웠음

#### Depth-First Search: Review 

- A graph structure definition

```c++
typedef struct _edgenode
{
    int y;               /* adjancency info */
    int weight;          /* edge weight, if any */
    struct _edgenode *next; /* next edge in list */
} edgenode;

typedef struct _graph{
    // The vertices are numbered starting from 1 not 0. edgenode *edges[MAXV + 1];
  /* adjacency info */
    int degree[MAXV + 1]; 
  /* outdegree of each vertex */
    int nvertices;        
  /* number of vertices in the graph */
    int nedges;           
  /* number of edges in the graph */
    int directed;         
  /* is the graph directed? */
} graph;
```

- A Recursive implementation in C

```c++
void dfs(graph *g, int v)
{
    edgenode *p; /* temporary pointer */
    int y;       /* successor vertex */
    entry_time[v] = ++time;
    PROCESS_VERTEX_EARLY(v);
    discovered[v] = TRUE;
    p = g->edges[v];
    while (p != NULL)
    {
        y = p->y;
        if (discovered[y] == FALSE)
        {
            parent[y] = v;
            PROCESS_EDGE(v, y, g);
            dfs(g, y);
        }
        else
            PROCESS_EDGE(v, y, g);
        p = p->next;
    }
    exit_time[v] = ++time;
    PROCESS_VERTEX_LATE(v);
    processed[v] = TRUE;
}
```

- parent = predecessor
- entry time = discovery time 
- exit time = finish time

#### An Abstract Implementation Using a Stack

- 편의상 connected graph로 가정 (아닐 경우에는?)
- 어떤 연산이 전체 탐색을 dominate하는가?
- 각 꼭지점은 unvisited 상태에서 스택에 몇 번 push되는가? 
- 전체적으로 각 edge는 몇 번 access되는가?

```c++
DFS(G, s)
{ // s is the vertex where the DFS starts. Initialize a stack S to be empty;
visited[v] = F for all vertices in G;
Push(S, s);
while (S is not empty)  
  do{  
      v = Pop(S);
      if (visited[v] = F){
        visited[v] = T;
        for (each vertex u that is adjacent to v)
          if (visited[u] = F)
            Push(S, u);
        }
    }
}
```

- Time complexity
  - Adjacency list: $O(|V|+|E|)$ 
  - Adjacency matrix: $O(|V|2)$
- 기존에 배운 recursion 기반 구현과 비교할 것

### 3) Graph Search 2: Breadth-First Search (BFS)

- 자료구조 과목에서 배웠음

#### An Abstract Implementation Using a Queue

- 편의상 connected graph로 가정 (아닐 경우에는?)
- 어떤 연산이 전체 탐색을 dominate하는가?
- 각 꼭지점은 unvisited 상태에서 스택에 몇 번 push되는가? 
- 전체적으로 각 edge는 몇 번 access되는가?

```c++
void BFS(G, s)
{ // s is the vertex where the DFS starts. Initialize a queue Q to be empty;
visited[v] = F for all vertices in G;
visited[s] = T;
Insert(Q, s);
while (Q is not empty){
    v = delete (Q);
    for (each vertex u that is adjacent to v)    {
        if (visited[u] = F){
            visited[u] = T;
            Insert(Q, u);
        }
    }
}
```

- Time complexity
- Adjacency list: $O(|V|+|E|) $
- Adjacency matrix: $O(|V|^2)$

## 3. Shortest Path Algorithm

### Floyd-Warshall All-Pairs Shortest Path Algorithm 

- Problem

  - Given a weighted graph *G* = (*V*, *E*) with cost function *cost*[*i*][*j*], find the shortest paths between all pairs of vertices. (*V* = {*v*0*, v*1*, v*2*, ..., v**n*-1} with |*V*| = *n*)
  - In general, the cost(weight) may be negative, but there must not exist a negative cycle in the graph.

- A dynamic programming approach

  - Let $A^k [i][j]$ be the cost of the shortest path from *i* to *j*, using only those intermediate vertices with an index ≤ *k*.

  - The goal is to compute $A^{n-1} [i][j]$ $\forall i,j = 0,1,2,...,n-1$

  - Optimal substructure for computing $A^k [i][j]$ from $A^{k-1} [i][j]$

    1. If the shortest path from *i* to *j* going through no vertex with index greater than *k* does not go through the vertex with index *k* 

       $A^k [i][j]$ = $A^{k-1} [i][j]$

    2. If the shortest path from *i* to *j* going through no vertex with index greater than *k* does go through the vertex with index *k* 

       $A^k [i][j]$ = $A^{k-1} [i][k] + A^{k-1} [k][j] $

- The table computation

  - Initialization / Table traversal order 
  - Example: $k = 4 (A_k[i][j] A_{k-1}[i][j])$
  - $O(n^3)$ time
  - An in-place implementation is possible.

- Path reconstruction

  - [ref](https://en.wikipedia.org/wiki/Floyd%E2%80%93Warshall_algorithm)



## 4. Minimum Spanning Trees

- Tree
  - A tree is a connected graph T that contains no cycle. 
  - Other equivalent statements (T = (V, E) where |V| = n)
  - T contains no cycles, and has n-1 edges.
  - T is connected, and has n-1 edges.
  - Any two vertices of T are connected by exactly one path.
  - T contains no cycles, but the addition of any new edge creates exactly one cycle.

- Forest
  - A forest is a graph with no cycles.

- references
  - https://en.wikipedia.org/tree
  - https://www.mathreference.org/

- Buy-Two-Get-One-Free Theorem
  - For a graph G = (V, E) with n vertices, any two of the following three properties imply the third one:
    1. G is connected. 
    2. G is acyclic.
    3. G has n-1 edges.

- Minimum spanning tree
  - [wiki](https://en.wikipedia.org/wiki/Minimum_spanning_tree)
  - A spanning tree for a graph G = (V, E) is a tree that contains all the vertices of G.
  - The cost of a spanning tree of a weighted graph G = (V, E) is the sum of the weights of the edges in the spanning tree.
  - A minimum spanning tree for a weighted graph G = (V, E) is a spanning tree of least cost.

- Problem
  - Given a weighted graph G = (V, E), find a minimum spanning tree of G.

- A naïve approach
  - Examine all the spanning trees of G,and take one having least cost.
  - There are nn-2 spanning trees in Kn!

#### Kruskal’s Algorithm vs Prim’s Algorithm (Greedy!)

- > ref. Courtesy of T. Cormen et al.

- Kruskal’s algorithm

  - In each step, find and add an edge of the least possible weight that connects any two trees in the (current) forest.

- Prim’s algorithm

  - In each step, find and add an edge of the least possible weight that connects the (current) tree to a non-tree vertex.

#### Generic MST Algorithm and its Correctness

- Generic algorithm for a graph G = (V, E) with a weight function w
  - For an edge set A that is a subset of some MST, an edge (u, v) is called a safe edge for A if A  {(u, v)} is also a subset of some MST.
  - Loop invariant for a set of edges A
    • Prior to each iteration, A is a subset of some minimum spanning tree.

```c++
Generic-MST(G) {
    A := empty; 
    // A: a set of edges of G 
    While (A does not form a spanning tree) {
        Find and edge (u, v) that is safe for A;
        A := A  {(u, v)}; 
    }
}
```

- Some definitions

  > Courtesy of T. Cormen et al.
  >
  > -  A cut (S, V-S) of G is a partition of V.

  - An edge (u, v) of G crosses a cut (S, V-S) if u  S and v  V-S→cut-set.
  - A cut respects a set A of edges if no edge in A crosses the cut.
  - An edge is a light edge crossing a cut if its weight is the minimum of any edge crossing the cut.

#### Cut Property

- For any cut C of the graph, if the weight of an edge e in the cut-set of C is strictly smaller than the weights of all other edges of the cut-set of C, then this edge belongs to all MSTs of the graph.

- Proof: 
  - Assume that there is an MST $T$ that does not contain e. 
  - Adding $e$ to $T$ will produce a cycle, that crosses the cut once at e and crosses back at another edge $e'$. 
  - Deleting e' we get a spanning tree $T∖{e'}∪{e}$ of strictly smaller weight than $T$. This contradicts the assumption that T was a MST.
- By a similar argument, if more than one edge is of minimum weight across a cut, then each such edge is contained in some minimum spanning tree.

```c++
Generic-MST(G) {
	A := empty; 
	// A: a set of edges of G 
  While (A does not form a spanning tree) {
    Find and edge (u, v) that is safe for A;
    A := A ∪  { (u, v) }; 
  }
}
```

- Loop invariant for the set A
  - Prior to each iteration, A is a subset of some minimum spanning tree.

- Theorem
  - Let G = (V, E) be a connected, undirected graph with a real-valued weight function w defined on E. Let A be a set of E that is included in some minimum spanning tree for G, let (S, V-S) be any cut of G that respects A, and let (u, v) be a light edge crossing (S, V-S). Then, edge (u, v) is safe for A.

#### Selection of Next Edge: Kruskal’s Algorithm

- In each step, find and add an edge of the least possible weight that connects any two trees in the (current) forest.

```c++
Generic-MST(G) {
  A := empty; 
  // A: a set of edges of G 
  While (A does not form a spanning tree) {
    Find and edge (u, v) that is safe for A;
    A := A ∪ { (u, v) }; 
  }
}
```

- Theorem
  - Let G = (V, E) be a connected, undirected graph with a real-valued weight function w defined on E. Let A be a set of E that is included in some minimum spanning tree for G, let (S, V-S) be any cut of G that respects A, and let (u, v) be a light edge crossing (S, V-S). Then, edge (u, v) is safe for A.

#### Selection of Next Edge: Prim’s Algorithm

- In each step, find and add an edge of the least possible weight that connects the (current) tree to a non-tree vertex.

```c++
Generic-MST(G) {
  A := empty; // A: a set of edges of G 
  While (A does not form a spanning tree) {
    Find and edge (u, v) that is safe for A;
    A := A ∪ { (u, v) }; }
}
```

- Theorem
  - Let G = (V, E) be a connected, undirected graph with a real-valued weight function w defined on E. Let A be a set of E that is included in some minimum spanning tree for G, let (S, V-S) be any cut of G that respects A, and let (u, v) be a light edge crossing (S, V-S). Then, edge (u, v) is safe for A.

(2020.12.01 화요일)

### [주제 6] Graph Algorithms

#### Minimum Spanning Tree

#### Kruskal’s Algorithm vs Prim’s Algorithm (Greedy!)

- > ref. Courtesy of T. Cormen et al.

- Kruskal’s algorithm

  - In each step, find and add an edge of the least possible weight that connects any two trees in the (current) forest.

- Prim’s algorithm

  - In each step, find and add an edge of the least possible weight that connects the (current) tree to a non-tree vertex.

#### Generic MST Algorithm and its Correctness

- Generic algorithm for a graph G = (V, E) with a weight function w
  - For an edge set A that is a subset of some MST, an edge (u, v) is called a safe edge for A if A  {(u, v)} is also a subset of some MST.
  - Loop invariant for a set of edges A
    • Prior to each iteration, A is a subset of some minimum spanning tree.

```c++
Generic-MST(G) {
    A := empty; 
    // A: a set of edges of G 
    While (A does not form a spanning tree) {
        Find and edge (u, v) that is safe for A;
        A := A  {(u, v)}; 
    }
}
```

- Some definitions

  > Courtesy of T. Cormen et al.

  -  A cut (S, V-S) of G is a partition of V.
  -  An edge (u, v) of G crosses a cut (S, V-S) if u  S and v  V-S→cut-set.
  -  A cut respects a set A of edges if no edge in A crosses the cut.
  -  An edge is a light edge crossing a cut if its weight is the minimum of any edge crossing the cut.

#### Cut Property

- For any cut C of the graph, if the weight of an edge e in the cut-set of C is strictly smaller than the weights of all other edges of the cut-set of C, then this edge belongs to all MSTs of the graph.

- Proof: 
  - Assume that there is an MST $T$ that does not contain e. 
  - Adding $e$ to $T$ will produce a cycle, that crosses the cut once at e and crosses back at another edge $e'$. 
  - Deleting e' we get a spanning tree $T∖{e'}∪{e}$ of strictly smaller weight than $T$. This contradicts the assumption that T was a MST.
- By a similar argument, if more than one edge is of minimum weight across a cut, then each such edge is contained in some minimum spanning tree.

```c++
Generic-MST(G) {
	A := empty; 
	// A: a set of edges of G 
  While (A does not form a spanning tree) {
    Find and edge (u, v) that is safe for A;
    A := A ∪  { (u, v) }; 
  }
}
```

- Loop invariant for the set A
  - Prior to each iteration, A is a subset of some minimum spanning tree.

- Theorem
  - Let G = (V, E) be a connected, undirected graph with a real-valued weight function w defined on E. Let A be a set of E that is included in some minimum spanning tree for G, let (S, V-S) be any cut of G that respects A, and let (u, v) be a light edge crossing (S, V-S). Then, edge (u, v) is safe for A.

#### Selection of Next Edge: Kruskal’s Algorithm

- In each step, find and add an edge of the least possible weight that connects any two trees in the (current) forest.

```c++
Generic-MST(G) {
  A := empty; 
  // A: a set of edges of G 
  While (A does not form a spanning tree) {
    Find and edge (u, v) that is safe for A;
    A := A ∪ { (u, v) }; 
  }
}
```

- Theorem
  - Let G = (V, E) be a connected, undirected graph with a real-valued weight function w defined on E. Let A be a set of E that is included in some minimum spanning tree for G, let (S, V-S) be any cut of G that respects A, and let (u, v) be a light edge crossing (S, V-S). Then, edge (u, v) is safe for A.

#### Selection of Next Edge: Prim’s Algorithm

- In each step, find and add an edge of the least possible weight that connects the (current) tree to a non-tree vertex.

```c++
Generic-MST(G) {
  A := empty; // A: a set of edges of G 
  While (A does not form a spanning tree) {
    Find and edge (u, v) that is safe for A;
    A := A ∪ { (u, v) }; }
}
```

- Theorem
  - Let G = (V, E) be a connected, undirected graph with a real-valued weight function w defined on E. Let A be a set of E that is included in some minimum spanning tree for G, let (S, V-S) be any cut of G that respects A, and let (u, v) be a light edge crossing (S, V-S). Then, edge (u, v) is safe for A.

#### Kruskal’s Minimum Spanning Tree Algorithm 

- Idea
  - Finds an edge of the least possible weight that connects any two trees in the forest.

- Implementation using disjoint-set data structure
  - 매 단계 forest를 어떻게 관리할 것인가?
  - 두 tree를 어떻게 병합할 것인가?
  - 매 단계 (u, v)를 어떻게 선택할 것인가?

- Complexity
  - Sort the edges by weight: $O(E log E)$
  - Process the edges until a tree is built: $O(E log V)$
  - $O(E log E + E log V) = O(E log V)$
    - why?

##### An implementation of the Kruskal’s algorithm

- from http://www.geeksforgeeks.org/greedy-algorithms-set-2-kruskals-minimum-spanning-tree-mst/

```c++
// C++ program for Kruskal's algorithm
// to find Minimum Spanning Tree of a
// given connected, undirected and weighted
// graph
#include <bits/stdc++.h>
using namespace std;

// a structure to represent a
// weighted edge in graph
class Edge {
public:
	int src, dest, weight;
};

// a structure to represent a connected,
// undirected and weighted graph
class Graph {
public:
	
	// V-> Number of vertices, E-> Number of edges
	int V, E;

	// graph is represented as an array of edges.
	// Since the graph is undirected, the edge
	// from src to dest is also edge from dest
	// to src. Both are counted as 1 edge here.
	Edge* edge;
};

// Creates a graph with V vertices and E edges
Graph* createGraph(int V, int E)
{
	Graph* graph = new Graph;
	graph->V = V;
	graph->E = E;

	graph->edge = new Edge[E];

	return graph;
}

// A structure to represent a subset for union-find
class subset {
public:
	int parent;
	int rank;
};

// A utility function to find set of an element i
// (uses path compression technique)
int find(subset subsets[], int i)
{
	// find root and make root as parent of i
	// (path compression)
	if (subsets[i].parent != i)
		subsets[i].parent
			= find(subsets, subsets[i].parent);

	return subsets[i].parent;
}

// A function that does union of two sets of x and y
// (uses union by rank)
void Union(subset subsets[], int x, int y)
{
	int xroot = find(subsets, x);
	int yroot = find(subsets, y);

	// Attach smaller rank tree under root of high
	// rank tree (Union by Rank)
	if (subsets[xroot].rank < subsets[yroot].rank)
		subsets[xroot].parent = yroot;
	else if (subsets[xroot].rank > subsets[yroot].rank)
		subsets[yroot].parent = xroot;

	// If ranks are same, then make one as root and
	// increment its rank by one
	else {
		subsets[yroot].parent = xroot;
		subsets[xroot].rank++;
	}
}

// Compare two edges according to their weights.
// Used in qsort() for sorting an array of edges
int myComp(const void* a, const void* b)
{
	Edge* a1 = (Edge*)a;
	Edge* b1 = (Edge*)b;
	return a1->weight > b1->weight;
}

// The main function to construct MST using Kruskal's
// algorithm
void KruskalMST(Graph* graph)
{
	int V = graph->V;
	Edge result[V]; // Tnis will store the resultant MST
	int e = 0; // An index variable, used for result[]
	int i = 0; // An index variable, used for sorted edges

	// Step 1: Sort all the edges in non-decreasing
	// order of their weight. If we are not allowed to
	// change the given graph, we can create a copy of
	// array of edges
	qsort(graph->edge, graph->E, sizeof(graph->edge[0]),
		myComp);

	// Allocate memory for creating V ssubsets
	subset* subsets = new subset[(V * sizeof(subset))];

	// Create V subsets with single elements
	for (int v = 0; v < V; ++v)
	{
		subsets[v].parent = v;
		subsets[v].rank = 0;
	}

	// Number of edges to be taken is equal to V-1
	while (e < V - 1 && i < graph->E)
	{
		// Step 2: Pick the smallest edge. And increment
		// the index for next iteration
		Edge next_edge = graph->edge[i++];

		int x = find(subsets, next_edge.src);
		int y = find(subsets, next_edge.dest);

		// If including this edge does't cause cycle,
		// include it in result and increment the index
		// of result for next edge
		if (x != y) {
			result[e++] = next_edge;
			Union(subsets, x, y);
		}
		// Else discard the next_edge
	}

	// print the contents of result[] to display the
	// built MST
	cout << "Following are the edges in the constructed "
			"MST\n";
	int minimumCost = 0;
	for (i = 0; i < e; ++i)
	{
		cout << result[i].src << " -- " << result[i].dest
			<< " == " << result[i].weight << endl;
		minimumCost = minimumCost + result[i].weight;
	}
	// return;
	cout << "Minimum Cost Spanning Tree: " << minimumCost
		<< endl;
}

// Driver code
int main()
{
	/* Let us create following weighted graph
			10
		0--------1
		| \ |
	6| 5\ |15
		| \ |
		2--------3
			4 */
	int V = 4; // Number of vertices in graph
	int E = 5; // Number of edges in graph
	Graph* graph = createGraph(V, E);

	// add edge 0-1
	graph->edge[0].src = 0;
	graph->edge[0].dest = 1;
	graph->edge[0].weight = 10;

	// add edge 0-2
	graph->edge[1].src = 0;
	graph->edge[1].dest = 2;
	graph->edge[1].weight = 6;

	// add edge 0-3
	graph->edge[2].src = 0;
	graph->edge[2].dest = 3;
	graph->edge[2].weight = 5;

	// add edge 1-3
	graph->edge[3].src = 1;
	graph->edge[3].dest = 3;
	graph->edge[3].weight = 15;

	// add edge 2-3
	graph->edge[4].src = 2;
	graph->edge[4].dest = 3;
	graph->edge[4].weight = 4;


	// Function call
	KruskalMST(graph);

	return 0;
}

// This code is contributed by rathbhupendra

```

#### Prim’s Minimum Spanning Tree Algorithm 

- Idea

  - In each step, find and add an edge of the least possible weight that connects the (current) tree to a non-tree vertex.

- Algorithm

  ```c++
  Given G = (V, E),
  Begin with a tree T0 = (V0, E0) where V0 = {v1} and E0 = {}. 
  repeat { 
    // Ti = (Vi, Ei)→Ti+1 = (Vi +1, Ei +1)
    Select a vertex v in V - Vi that is nearest to Vi. 
      // Let v is from the edge (u, v), where u in Vi.
      Update T in such a way that
      	Vi +1 = Vi + {v}, and Ei +1 = Ei + {(u, v)}.
  until (an MST is found)
  
  ```

- A key issue in implementation

  - Tree vertices와 non-tree vertices들을 어떻게 관리할 것인가?
  - Tree vertices와 non-tree vertices들 간의 최소 비용 edge를 어떻게 (효율적으 로) 찾을 것인가?

- From Prof. Kenji Ikeda's Home Page

#### Inductive Description of the Prim’s Algorithm

- $ V=\{ v_0,v_1,v_2,...,v_{n-1} \}$ with $|V| = n$
- $T^k →T^{k+1}$
  - 매번 가장 비용이 낮은 fringe edge를 선택하여 $T^k$ 로 옮긴 후 그에 따른 처리를 함.

#### An $O(n^2)$ Implementation: Adjacency Matrix 사용

- a = 0, b = 1, c = 2, d = 3, e = 4, f = 5, g = 6, h = 7, i = 8, j = 9
- st[i]: T로 선택된 vertex i의 parent vertex 번 호 저장
- fr[i]: NT에 있는 vertex i에서 T에 있는 vertex 중 가장 가까운 vertex의 번호
- wt[i]: NT에 있는 vertex i에 대해 그 vertex 에서 fr[i]까지의 거리

| **v** | **st[v]** | **fr[v]** | **wt[v]** |
| ----- | --------- | --------- | --------- |
| 0(a)  | 0         | 0         | maxWT     |
| 1(b)  | 3         | 3         | 13        |
| 2(c)  | 0         | 0         | 10        |
| 3(d)  | 2         | 2         | 23        |
| 4(e)  | 6         | 6         | 17        |
| 5(f)  | -1        | 2         | 24        |
| 6(g)  | 3         | 3         | 20        |
| 7(h)  | -1        | 4         | 35        |
| 8(i)  | -1        | 6         | 45        |
| 9(j)  | -1        | 6         | 42        |
| 10    |           |           | maxWT     |

n=| V|

```c++
static int fr[maxV];
#define P G->adj[v][w]
void GRAPHmstV(Graph G, int st[], double wt[])
{

    int v, w, min, n = G->V;
    for (v = 0; v < n; v++)
    {

        st[v] = -1;
        fr[v] = v;
        wt[v] = maxWT;
    }

    wt[**n * *] = **maxWT * *;

    // wt[n] : dummy vertex, maxWT: dummy weight
    // Check to see whether adding the new edge brought any nontree vertex closer to the tree. Find the next edge to add to the tree.
    for (min = 0; min != n;){            
      //언제 끝날까?
        v = min; //다음으로 선택된 vertex 번호
        st[min] = fr[min];
        for (w = 0, min = n; w < n; w++)
//아직 선택되지 않은 모든 vertex w에 대해, v = min이 선택된 것에 대한 update 수행
            if (st[w] == -1)
            {
                if (P < wt[w])
                {
                    wt[w] = P;
                    fr[w] = v;
                }
                if (wt[w] < wt[min])
                    min = w;
//wt[w]를 update하면서, 동시에 가장 작은 wt 값을 가지는 vertex 번호 min을 계산
            }
    }
}
```

- 모든 계산이 끝난 후 wt[i]는 어떤 정보를 가지고 있을까?

#### An O(e log n) Implementation: Adjacency List 사용

- Observations
  - The inner for-loop in the O(n2) implementation visits all the vertices to update wt[] array and to find the minimum.
  - An O(e log n) time implementation is possible. 
    - If the graph is dense, $n^2 logn$
    - If the graph is sparse, $n log n$
  - n = | V |, e = | E |

- We need to employ the priority queue that allows
  - to insert a new item (PQinsert(w)),
  - to delete the minimum item (w = PQdelmin()), and
  - to change the priority of an arbitrary specified item (PQdec(w)).

```c++
typedef struct node *link;
struct node
{
    int v;
    double wt, link next;
};

struct graph
{
    int V;
    int E;
    link *adj;
};
```

```c++
typedef struct graph *Graph;
#define GRAPHpfs GRAPHmst static int fr[maxV];
static double *priority;
// Put the priority queue codes here. #define P t->wt

void GRAPHpfs(Graph G, int st[], double wt[]){
    link t;
    int v, w;
    PQinit();
    priority = wt;

    for (v = 0; v < G->V; v++){
        st[v] = -1;
        fr[v] = -1;
    }
    fr[0] = 0;
    PQinsert(0 **) * *;
    while (!PQempty()){
      v = PQdelmin();
      st[v] = fr[v];
      for (t = G->adj[v]; t != NULL; t = t->next){
        if (fr[w = t->v] == -1){
          wt[w] = P;
          PQinsert (w);
          fr[w] = v;
        }
      	else if ((st[w] == -1) && (P < wt[w])){
          wt[w] = P;
          PQdec (w);
          fr[w] = v;
        }
      }
    }
}

```

15 - 1

2020.12.08 화요일

### [주제 6] Graph Algorithms

#### Shortest-Paths Problems

- https://datascience.lc/2019/10/26/shortest-path-dijkstra-algorithm/
- Single-source shortest-paths problem
  - Dijkstra’s algorithm
    - Only nonnegative-weight edges are present.
  - Bellman-Ford algorithm
    - Negative-weight edges may be present, but there are no negative-weight cycles.
- Single-destination shortest-paths problem
- Singe-pair shortest-path problem
- All-pairs shortest-paths problem 
  - Floyd-Warshall algorithm
    - Negative-weight edges may be present, but there are no negative-weight cycles.
  - Johnson’s algorithm for sparse graphs
    - Negative-weight edges may be present, but there are no negative-weight cycles.

- The optimal-substructure property of shortest paths 
  - Subpaths of shortest paths are shortest paths!

#### Single-Source Shortest Path 

- Problem
  - Given a weighted directed graph G = (V, E) with a weighting function w(e) (w(e) ≥ 0 for the edges in G), and a source vertex v0, find a shortest path from v0 to each of the remaining vertices of G.

- Note
  - The case of an undirected graph can be handled by simply replacing each undirected edge e = (u, v) of length w(e) by two directed edges (u, v) and (v, u), each of the same length.
  - Only the case of a directed graph whose weights are positive (or nonnegative) is handled by the Dijkstra’s algorithm. For a graph that allows a negative weight, the Bellman-Ford algorithm is one to be used.
  - Before learning the single-source shortest path algorithm that builds some tree, recall how the breadth first search tries to build a BFS tree.
  - A breadth first search tree
  - A tree built by the Dijkstra’s algorithm

#### Dijkstra’s Single-Source Shortest Path Algorithm

- A greedy approach
  - Generate the shortest paths in non-decreasing order of lengths!

1. $S^1=\{ v_0 \}$로 설정하고 시작.
2. ($i=k$일 때) $S^k$의 꼭지점들만 사용할 경우에 대한 $v_0$에서 $v$까지의 shortest path가 구해져 있음. ($v$는 $S^k$의 꼭지점)
3. $S^k$상황에서 가장 짧은 path에 대한 꼭지점 $v$를 $S^k$로 옮긴 후 적절한 처리를 수행 → $S^{k+1}$
4. ($i = k+1$일 때) $S^{k+1}$ 의 꼭지점들만 사용할 경우에 대한 $v_0$에서 $v$까지의 shortest path가 구해져 있음. ($v$는 $S^{k+1}$의 꼭지점)
   - 다시 2. 로 감

5. $S_n$ 이 다 구해졌을 경우

#### From Prof. Kenji Ikeda's Home Page

#### Dijkstra’s Algorithm 

- (from Introduction to Algorithms by T. Cormen)

- A directed graph with nonnegative weight $G(V, E)$ with $w: E→ [0,∞)$ and source $s$

- Two components for each vertex $v$

  - $v.d$: an upper bound on the weight of a shortest path from s to v (a shortest path estimate)

  - $v.π$: the predecessor of v in the (current) shortest path

    ```markdown
    Initialize-Single-Source(G,s)
    for each vertex v in G.V
    	v.d = infinite
    	v.pi = NIL
    s.d = 0
    ```

- Relaxation

  - The process of relaxing an edge (u, v) consists of testing whether we can improve the shortest path to v found so far by going through u and, if so, updating v.d and v.π.

    ```markdown
    Relax(u,v,w)
    if v.d > u.d + w(u,v)
    	v.d = u.d + w(u,v)
    	v.pi = u
    ```

    - 아직 shortest path를 찾지 못한 vertex v에 대해
    - 새롭게 선택된 vertex u에 adjacent한 vertex v에 대해

- Dijkstra’s algorithm

  - Maintains a set S of vertices whose final shortest-path weight from the source s have already been determined.

    1. Select repeatedly the vertex u in V-S with the minimum shortest-path estimate
    2. 2. adds u to S, and 3. relaxes all edges leaving u.

    ```markdown
    Dijkstra(G,w,s)
    Initialize-Single-Source(G,s)
    S = zero-set
    Q = G.V
    while Q != zero-set
    	u= extract-min(Q)
    	S= S U {u}
    	for each vertex v in G.Adj[u]
    		Relax(u,v,w)
    ```

  - When the algorithm adds a vertex u to the set S, u.d is the final shortest-path weight from s to u.

15-2

2020.12.10 목요일

### [주제 6] Graph Algorithms

#### Dijkstra’s Single-Source Shortest Path Algorithm 

- Dijkstra’s algorithm

  - Maintains a set S of vertices whose final shortest-path weight from the source s have already been determined.

  1. Select repeatedly the vertex u in V-S with the minimum shortest-path estimate
  2. adds u to S, and 3. relaxes all edges leaving u.

  ```markdown
  Dijkstra(G,w,s)
  Initialize-Single-Source(G,s)
  S = zero-set
  Q = G.V
  while Q != zero-set
  	u= extract-min(Q)
  	S= S U {u}
  	for each vertex v in G.Adj[u]
  		Relax(u,v,w)
  ```

  - When the algorithm adds a vertex u to the set S, u.d is the final shortest-path weight from s to u.
  - 계산과정 예

- Correctness of Dijkstra’s algorithm

  - Theorem
    - Dijkstra algorithm, run on a weighted, directed graph $ G=(V,E)$ with nonnegative weight function $w : E \rightarrow R$ and source $s$, terminates with $v.d = \delta(s,v)$ $\forall$ vertices $v \in V$
  - Loop invariant
    - At the start of each iteration of the while loop of lines 4-8, $v.d = \delta(s,v)$ for each vertex $ v \in S$
  - A key in the proof
    - to show that for each vertex $ u \in V$, we have $u.d = \delta(s,u)$ at the time when $u$ is added to set $S$
    - Suppose for contradiction that $u$ be the first vertex for which $u.d \neq \delta(s,u)$ when it is added to set $S$
  - s ---> x ->y가 shortest path이므로, x가 S에 add 되면서 x->y에 relaxation할 때, y.d에 δ(s, y)가 저장. 따라서 u가 S에 add가 될 때에도 y.d = δ(s, y).

#### An $O(n^2)$ Implementation : Adjacency Matrix 사용

- S: the set of vertices, including $v_0$, whose shortest paths have been found 
- distance[w]: the length of the shortest path starting from $v_0$, going through vertices only in S, and ending in w (w not in S)

- Observations
  - *When the shortest paths are generated in nondecreasing order of length,*

- If the next shortest path is to vertex *u*, then the path from *v*0 to *u* goes through only those vertices that are in *S*.
- Vertex *u* is chosen so that it has the minimum distance *distance*[*u*] among all the vertices not in *S*.
- Adding *u* to *S* may change the distance of shortest paths starting at *v*0 going through vertices only in the new *S*, and ending at a vertex *w* that is not currently in the new *S*. 
- $distance[w]  = min\{distance[w], distance[u] + length(<u, w>) \}$

- $V = \{ v_0, v_1, ..., v_{n-1}\}$ with $|V| = n$
- code in [Horowitz]

```c++
int choose(int distance[], int n, int found[])
{
    int i, min = INT_MAX,
           minpos = -1;
    for (i = 0; i < n; i++) // O(n)
        if (distance[i] < min && !found[i])
        {
            min = distance[i];
            minpos = i;
        }
    return minpos;
}
void shortestpath(int v,
                  int cost[][MAX_VERTICES],
                  int distance[], int n, int found[])
{
    int i, u, w, tmp;
    for (i = 0; i < n; i++)
    {
        found[i] = FALSE;
        distance[i] = cost[v][i];
    }
    found[v] = TRUE;
    distance[v] = 0;
    for (i = 0; i < n - 2; i++)
    { // O(n)
        // find the next u to be added to S
        u = choose(distance, n, found);
        found[u] = TRUE; // add u to S for (w = 0; w < n; w++)
        if (!found[w])   // for w not in S
        if ((tmp = distance[u] +
    }
}
cost[u][w]) < distance[w]) distance[w] = tmp;
```

- $distance[i]$ : the length of the SP from vertex v to i 
- $found[i]$ 
  - $FALSE$ if the SP from vertex i has not been found,
  - $TRUE$ otherwise
- $cost[i][j]$ : cost adjacency matrix

#### An O(e log n) Implementation: Adjacency List 사용

- 매 순간 $wt[w]$에는 항상 *S**k* 의 꼭지점들만 사용할 경우에 대한 *v**s*에서 *w*까지의 shortest path의 길이가 저장되어 있음.
- wt[]는 앞의 프로그램에서의 distance[]에 해당

```c++
typedef struct node \*link;
struct node
{
    int v;
    double wt;
    link next;
};
struct graph
{
    int V;
    int E;
    link *adj;
};
typedef struct graph *Graph;
```

```c++
#define GRAPHpfs GRAPHspt
#define P(wt[v] + t->wt) 
void GRAPHpfs(Graph G, int s, int st[],​ double wt[]){
    
  int v, w;
  link t;
  PQinit();
  priority = wt;
  for (v = 0; v < G->V; v++){
    st[v] = -1;
    wt[v] = maxWT;
    PQinsert(v);
  }
  wt[s] = 0.0;
  PQdec(s);
  while (!PQempty())// 언제 끝날까?
    if (wt[v = PQdelmin()] ! = maxWT) //어떤 경우인가?
      for (t = G->adj[v]; t != NULL; t = t->next)
        if (P < wt[w = t->v]){
          wt[w] = P;          
          PQdec(w); 
         //다 끝난 후 각 꼭지점에 대한 sp를 어떻게 출력할까?
          st[w] = v;  
        }
    }
}
```

- MO ̈HRING et at., Partitioning Graphs to Speedup Dijkstra’s Algorithm, ACM Journal of Experimental Algorithmics, 2006.
  - Standard Dijkstra’s Algorithm
  - Acceleration Algorithm

