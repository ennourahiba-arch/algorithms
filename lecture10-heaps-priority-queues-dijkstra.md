# Lecture 10: Heaps, Priority Queues, and Dijkstra’s Algorithm

## Table of Contents
- [Heaps and priority queues](#heaps-and-priority-queues)
- [Heap with keys and positions](#heap-with-keys-and-positions)
- [Priority queue operations and runtime](#priority-queue-operations-and-runtime)
- [Dijkstra’s algorithm with a priority queue](#dijkstras-algorithm-with-a-priority-queue)
- [Pseudocode](#pseudocode)
- [Adjacency matrix vs adjacency list](#adjacency-matrix-vs-adjacency-list)
- [Runtime proof and edge-count lemma](#runtime-proof-and-edge-count-lemma)
- [Why Fibonacci heaps improve the runtime](#why-fibonacci-heaps-improve-the-runtime)
- [Big picture summary](#big-picture-summary)

---

## Heaps and priority queues

A **heap** is a data structure that stores `N` items while keeping access to the smallest item fast.

From the notes:

- the **smallest item** can be accessed in `O(1)` time
- deleting the item in position `i` takes `O(log n)` time
- inserting a new item takes `O(log n)` time

The key property of a heap is not that all values are sorted. Instead, it only guarantees that the minimum element is at the top. That is why it is useful for **priority queues**.

A **priority queue** is a structure where each item has a priority, and the item with smallest priority is always the one you want to get next.

---

## Heap with keys and positions

The notes mention that heaps usually store items made of a single value, but sometimes items have:

- a **value**
- a **key**

For example:

- value = the thing we care about
- key = the label or identifier for that item

This matters because if you want to support efficient updates, you need to know where a particular key currently is inside the heap.

### Position array

The notes introduce an auxiliary array called `position`.

If `H[j].key = k`, then:

- `position[k] = j`

So `position[k]` tells you where the item with key `k` currently sits in the heap.

This is useful because heap operations such as:

- insert
- remove
- swap

can change the physical location of items, so the `position` array must be updated each time.

The notes emphasize that each of these operations causes at most 2 position updates, and those updates can be done in `O(1)` time. This means maintaining `position` does not change the asymptotic runtime of the heap operations.

---

## Priority queue operations and runtime

The resulting structure is called a **priority queue**.

It supports these operations:

- `Add(H, x)`  
  inserts item `x` into heap `H`
- `FindMin(H)`  
  returns the item with smallest key/value
- `Remove(H, i)`  
  removes the item in heap position `i`
- `ExtractMin(H)`  
  removes and returns the minimum item
- `RemoveK(H, key)`  
  removes the item with a given key, if it exists
- `UpdateValue(H, key, newValue)`  
  changes the value associated with a key

### Runtime of the operations

From the notes:

- `Add(H, x)` takes `O(log n)`
- `FindMin(H)` takes `O(1)`
- `Remove(H, i)` takes `O(log n)`
- `ExtractMin(H)` takes `O(log n)`
- `RemoveK(H, key)` takes `O(log n)`
- `UpdateValue(H, key, newValue)` takes `O(log n)`

The important idea is:

- `FindMin` is fast because the minimum is stored at the root
- everything else that changes the structure may require heap reorganization, which costs `O(log n)`

---

## Dijkstra’s algorithm with a priority queue

The notes then apply the priority queue to **Dijkstra’s algorithm**.

Dijkstra computes shortest paths from a source vertex `v` to all other vertices in a weighted graph, assuming nonnegative edge weights.

### What the algorithm is doing

The notes show a typical structure:

1. Initialize a priority queue `H` with size `N`
2. Add the source vertex with distance `0`
3. Create an array `d` where `d[i]` is the current best known distance from the source to vertex `i`
4. Repeatedly extract the minimum-distance vertex
5. For each out-neighbor, try to improve its distance
6. If a better distance is found, either insert it or update it in the priority queue
7. Return the distance array `d`

### Key behavior

For each vertex `v`, the algorithm:

- extracts the next closest unprocessed vertex
- relaxes all outgoing edges from that vertex

The relaxation step is where the priority queue matters, because vertices may need to be inserted or updated many times.

---

## Pseudocode

Here is the pseudocode for the version shown in the notes:

```text
DIJKSTRA(G(V, E), s):
    for each vertex v in V:
        d[v] ← ∞
    d[s] ← 0

    H ← empty priority queue
    Add(H, (s, 0))

    while H is not empty:
        u ← ExtractMin(H)
        for each out-neighbor w of u:
            if d[w] > d[u] + weight(u, w):
                d[w] ← d[u] + weight(u, w)
                if w is already in H:
                    UpdateValue(H, w, d[w])
                else:
                    Add(H, (w, d[w]))

    return d
```

### Same idea in simpler form

```text
Initialize all distances to infinity
Set distance of source to 0
Put source in the priority queue

While the queue is not empty:
    remove the vertex with minimum distance
    for each neighbor:
        if we found a better path:
            update the distance
            update the priority queue
```

### What `UpdateValue` does here

When the algorithm discovers a better path to a vertex, it either:

- decreases its key in the heap, or
- inserts it if it is not already present

That is exactly why the position array is useful: it lets the heap locate an item quickly.

---

## Adjacency matrix vs adjacency list

The notes show both representations of a graph.

### Adjacency matrix

An adjacency matrix is a table where entry `(i, j)` tells you whether there is an edge from `i` to `j`, or stores its weight.

Example idea:

- fast to check whether an edge exists
- uses `O(N^2)` space

### Adjacency list

An adjacency list stores, for each vertex, the list of its outgoing neighbors.

Example idea:

- more space-efficient for sparse graphs
- total size is proportional to number of vertices plus edges
- ideal for algorithms like Dijkstra when the graph is sparse

The drawings in the notes show that the same graph can be represented either way, but the adjacency list is usually better for traversing neighbors efficiently.

---

## Runtime proof and edge-count lemma

This is the key math part in the image you sent.

### Total runtime expression

The notes write the total runtime as:

`O(N) + O(N log N) + O((log N) · Σ deg⁺(v))`

Interpretation:

- `O(N)` for initialization
- `O(N log N)` for repeated extract-min operations
- `O((log N) · Σ deg⁺(v))` for processing edges and updates

This becomes:

`O(N log N) + O(log N · Σ deg⁺(v))`

and then:

`O((N + Σ deg⁺(v)) log N)`

### Worst-case bound

The notes then say:

`Σ deg⁺(v) ≤ N²`

So a pessimistic upper bound is:

`O(N² log N)`

That is the worst-case runtime if you only use the crude bound on the total out-degree.

---

## The directed graph lemma

The notes prove a useful fact:

For a directed graph `G(V, E)`,

`Σ deg⁺(v) = |E| = M`

where:

- `deg⁺(v)` is the out-degree of vertex `v`
- `M` is the number of edges

### Why this is true

For each vertex `w`, define:

- `E_w` = the set of all edges leaving `w`

Then:

- `|E_w| = deg⁺(w)`
- different vertices have disjoint outgoing edge sets
- the union of all these sets is the whole edge set `E`

So:

`E = ⋃_{v∈V} E_v`

and therefore:

`|E| = Σ |E_v| = Σ deg⁺(v)`

This is a standard counting argument: every directed edge has exactly one source vertex, so counting out-degrees counts every edge exactly once.

### Why it matters

This turns the runtime into:

`O((N + M) log N)`

instead of the looser `O(N² log N)`.

---

## Why Fibonacci heaps improve the runtime

At the bottom, the notes say:

> using Fibonacci heaps, the runtime becomes `O(M + N log N)`

Here is the idea:

- With a normal binary heap, decrease-key / update operations cost `O(log N)`
- Fibonacci heaps support decrease-key more efficiently, amortized `O(1)`
- Then the total cost of processing all edges becomes linear in `M`

So Dijkstra with a Fibonacci heap runs in:

`O(M + N log N)`

This is better for sparse graphs.

---

## Big picture summary

Here is the full story in plain words:

- A heap is used to store items so the smallest one is always easy to get.
- By adding a `position` array, you can efficiently update or remove arbitrary items by key.
- This creates a priority queue with efficient insert, extract-min, remove, and update operations.
- Dijkstra’s algorithm uses this priority queue to repeatedly pick the nearest vertex and relax its outgoing edges.
- If the graph is stored as an adjacency list, the total work over all edges is proportional to the number of edges.
- The runtime analysis depends on the heap implementation:
  - binary heap: about `O((N + M) log N)`
  - Fibonacci heap: `O(M + N log N)`

If you want, I can also make this into a **neater exam handout** or add a **worked example of the pseudocode on a small graph**.
