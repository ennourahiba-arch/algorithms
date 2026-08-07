# lecture8 — Directed Graphs

## Table of Contents
- [lecture8 — Directed Graphs](#lecture8--directed-graphs)
- [1. Directed Graph: Definition](#1-directed-graph-definition)
- [2. Undirected vs Directed Graphs](#2-undirected-vs-directed-graphs)
- [3. Paths in Directed Graphs](#3-paths-in-directed-graphs)
- [4. Weighted Directed Graphs](#4-weighted-directed-graphs)
- [5. Path Length](#5-path-length)
- [6. Shortest Paths in Directed Graphs](#6-shortest-paths-in-directed-graphs)
- [7. Dijkstra Algorithm (Nonnegative Weights)](#7-dijkstra-algorithm-nonnegative-weights)
- [8. Dijkstra Correctness Invariant (Proof Idea)](#8-dijkstra-correctness-invariant-proof-idea)
- [9. Worked Example (from the notes)](#9-worked-example-from-the-notes)
- [10. Common Mistakes](#10-common-mistakes)
- [11. Quick Exam Recap](#11-quick-exam-recap)

---

## 1. Directed Graph: Definition

A **directed graph** (digraph) is \(G=(V,E)\), where:

- \(V\) is a set of vertices (nodes)
- \(E\) is a set of directed edges (arcs), each one is an ordered pair \((u,v)\)

\[
E \subseteq \{(u,v)\mid u\neq v,\ u,v\in V\}
\]

Because edges are ordered, \((u,v)\) and \((v,u)\) are not the same in general.

### Example
\[
V=\{1,2,3\},\quad E=\{(1,2),(2,3),(3,2)\}
\]

- Can we go from \(1\) to \(3\)? **Yes**: \(1\to2\to3\)
- Can we go from \(3\) to \(1\)? **No**

So in directed graphs, direction matters for reachability.

---

## 2. Undirected vs Directed Graphs

An **undirected graph** uses unordered edges \(\{u,v\}\), so movement is possible in both directions.

\[
E \subseteq \{\{u,v\}\mid u\neq v,\ u,v\in V\}
\]

### Key difference
- Directed: \(u\to v\) does not imply \(v\to u\)
- Undirected: \(\{u,v\}\) means both directions

### Example (undirected)
\[
V=\{1,2,3\},\quad E=\{\{1,2\},\{2,3\}\}
\]

- \(1\to3\): yes via 2
- \(3\to1\): yes via 2

---

## 3. Paths in Directed Graphs

A path from \(s\) to \(t\) is a sequence:
\[
\pi=(v_1,v_2,\dots,v_k),\quad v_1=s,\ v_k=t
\]
such that:
\[
(v_i,v_{i+1})\in E\ \text{for all } i=1,\dots,k-1
\]

Important: in a digraph, having a path \(s\leadsto t\) does not guarantee a path \(t\leadsto s\).

---

## 4. Weighted Directed Graphs

A **weighted directed graph** is \((G,\ell)\), where:

- \(G=(V,E)\) is directed
- \(\ell:E\to\mathbb{R}_{\ge0}\) assigns a nonnegative weight to each arc

The weight can represent distance, time, cost, etc.

---

## 5. Path Length

### Unweighted graph
Path length = number of edges/arcs.

### Weighted graph
For \(\pi=(v_1,\dots,v_k)\):
\[
L(\pi)=\sum_{i=1}^{k-1}\ell(v_i,v_{i+1})
\]

So shortest path means minimum total weight.

---

## 6. Shortest Paths in Directed Graphs

Given a source \(s\), we want for each vertex \(v\):

- shortest distance \(d(v)\) from \(s\) to \(v\)
- optionally one shortest path \(P_v\)

If \(v\) is unreachable from \(s\), then \(d(v)=+\infty\).

---

## 7. Dijkstra Algorithm (Nonnegative Weights)

Dijkstra computes single-source shortest paths for weighted directed graphs with nonnegative weights.

### Objects used
- \(d(v)\): best known distance from \(s\) to \(v\)
- \(P_v\): best known path from \(s\) to \(v\)
- \(S\): finalized set of vertices
- \(T\): frontier vertices (reachable from \(S\), but not in \(S\))

### Pseudocode

**Input:** \((G=(V,E),\ell,s)\)

**Initialization**
1. \(d(s)\leftarrow 0\)
2. \(P_s\leftarrow []\)
3. \(S\leftarrow \{s\}\)

(For \(v\neq s\), distance is conceptually \(+\infty\) until updated.)

**While \(S\neq V\):**

1. Build frontier:
\[
T \leftarrow \{\,w\in V\setminus S \mid \exists u\in S,\ (u,w)\in E\,\}
\]

2. For each \(w\in T\), compute tentative distance:
\[
d'(w)\leftarrow \min_{\substack{(u,w)\in E\\u\in S}}
\left(d(u)+\ell(u,w)\right)
\]

3. Choose \(v\in T\) with minimum \(d'(v)\).

4. Choose \(u\in S\) such that:
\[
d'(v)=d(u)+\ell(u,v)
\]

5. Finalize \(v\):
\[
d(v)\leftarrow d'(v),\quad
P_v\leftarrow P_u+[v],\quad
S\leftarrow S\cup\{v\}
\]

**Return** all \(d(v)\), and optionally all paths \(P_v\).

### Plain-language idea
At each iteration:
1. Look at nodes just outside \(S\)
2. Compute best way to reach each from \(S\)
3. Pick cheapest one
4. Mark it final
5. Repeat

### Validity condition
\[
\ell(e)\ge0\ \ \forall e\in E
\]
If negative edges exist, Dijkstra is not guaranteed correct.

---

## 8. Dijkstra Correctness Invariant (Proof Idea)

### Lemma
At any moment in execution, for every \(u\in S\), \(P_u\) is a shortest path from \(s\) to \(u\), and its length is \(d(u)\).

### Proof sketch (induction on \(|S|\))
- **Base case:** \(S=\{s\}\), true since \(d(s)=0\).
- **Induction hypothesis:** true for current \(S\).
- **Step:** add \(v\notin S\). Show \(P_v\) is shortest.

Take any other path \(P\) from \(s\) to \(v\):
- Let \(z\) be first node of \(P\) outside \(S\)
- Let \(y\in S\) be the node before \(z\)

At that iteration, \(z\) is also a frontier candidate.  
Since algorithm picked \(v\), we have tentative\((v)\le\) tentative\((z)\).  
So \(P\) cannot be shorter than \(P_v\). Therefore \(P_v\) is shortest.

Hence invariant remains true after each iteration.

---

## 9. Worked Example (from the notes)

From source \(s\), first tentative values include:
- \(d'(v_1)=1\)
- \(d'(v_2)=4\)
- \(d'(v_3)=2\)

So first selected node is \(v_1\).  
After updates and repeated selections, final distances in your notes are:

- \(d(v_1)=1\)
- \(d(v_2)=2\)
- \(d(v_3)=2\)
- \(d(v_4)=3\)
- \(d(v_5)=4\)

So the shortest distance from \(s\) to \(v_5\) is \(4\).

---

## 10. Common Mistakes

1. Applying Dijkstra when negative weights exist  
2. Mixing up directed vs undirected reachability  
3. In weighted graphs, counting edges instead of summing weights  
4. Not selecting the minimum tentative node each round  
5. Forgetting to update path/predecessor with better candidates

---

## 11. Quick Exam Recap

- Directed graph: edges are ordered \((u,v)\)
- Undirected graph: edges are unordered \(\{u,v\}\)
- Weighted path length: sum of arc weights
- Shortest path: minimum total weight
- Dijkstra requires nonnegative weights
- Once a node enters \(S\), its distance is final
