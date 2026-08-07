# 📚 Lecture 9: Heaps and Heap Sort — Complete Beginner’s Guide

## 📋 Table of Contents

- [Introduction: What is a Heap?](#introduction-what-is-a-heap)
- [Why Do We Need Heaps?](#why-do-we-need-heaps)
- [How Heaps Work: Array vs Sorted Array vs Heap](#how-heaps-work-array-vs-sorted-array-vs-heap)
- [Understanding the Heap Property](#understanding-the-heap-property)
- [Representing Heaps as Trees and Arrays](#representing-heaps-as-trees-and-arrays)
- [Core Operation 1: Heapify-Up (Bubble Up)](#core-operation-1-heapify-up-bubble-up)
- [Proof of Heapify-Up by Induction](#proof-of-heapify-up-by-induction)
- [Core Operation 2: Heapify-Down (Bubble Down)](#core-operation-2-heapify-down-bubble-down)
- [Proof of Heapify-Down by Reverse Induction](#proof-of-heapify-down-by-reverse-induction)
- [Adding Items to a Heap](#adding-items-to-a-heap)
- [Removing Items from a Heap](#removing-items-from-a-heap)
- [Sorting with Heaps: Heap Sort](#sorting-with-heaps-heap-sort)
- [Real-World Application: Interval Partitioning](#real-world-application-interval-partitioning)
- [Summary & Complexity Comparison](#summary--complexity-comparison)
- [Practice Exercises](#-practice-exercises)

---

## Introduction: What is a Heap?

### 🎯 The Core Idea
Imagine you’re running a hospital emergency room. Patients arrive with different urgency levels. You need:

- quick access to the most urgent patient
- quick insertion of new patients
- quick removal of treated patients

A **heap** is designed exactly for this.

### 📦 What is a Heap?
A heap is a data structure that:

- stores key-value pairs
- lets you find the minimum in **O(1)**
- lets you add an item in **O(log n)**
- lets you remove an item in **O(log n)**

> ✅ For a **min-heap**, the minimum is always at the root.

### 💡 Why Heaps Are Special
| Operation | Regular Array | Sorted Array | Heap |
|---|---:|---:|---:|
| Find minimum | O(n) | O(1) | O(1) |
| Add item | O(1) | O(n) | O(log n) |
| Remove item | O(n) | O(n) | O(log n) |

---

## Why Do We Need Heaps?

### The problem with other structures

For 1 million items:

- **Regular array**
  - ✅ Add: very fast
  - ❌ Find min: scan all elements
  - ❌ Remove arbitrary efficiently: hard/slow

- **Sorted array**
  - ✅ Find min: instant
  - ❌ Add: shift many elements
  - ❌ Remove: shift many elements

- **Heap**
  - ✅ Find min: instant
  - ✅ Add: O(log n)
  - ✅ Remove: O(log n)

---

## How Heaps Work: Array vs Sorted Array vs Heap

### Regular array
```text
Values: [5, 2, 8, 1, 9, 3, 7]
```

### Sorted array
```text
Values: [1, 2, 3, 5, 7, 8, 9]
```

### Heap (min-heap)
```text
        1
      /   \
     2     3
    / \   / \
   5   9 7   8
```

Stored as array:
```text
[1, 2, 3, 5, 9, 7, 8]
```

✅ Heap is **partially sorted**: parent ≤ children.

---

## Understanding the Heap Property

### 🏛️ Min-Heap Property
Every parent node is smaller than or equal to its children.

That alone guarantees:

- the root is the smallest value
- `find-min` is O(1)

### AHWL Property (helper concept)
“Almost Heap With Largest”: structure is almost valid except one potential violation point.

---

## Representing Heaps as Trees and Arrays

A heap is visualized as a tree but implemented as an array.

### Index relationships (1-based indexing)
For node at index `i`:

- left child: `2i`
- right child: `2i + 1`
- parent: `floor(i / 2)`

> If using 0-based indexing, formulas differ slightly.

---

## Core Operation 1: Heapify-Up (Bubble Up)

When inserting a new item at the end, bubble it up while it is smaller than its parent.

### Algorithm
```pseudo
HeapifyUp(H, i):
    if i > 1:
        p = floor(i / 2)
        if H[p] > H[i]:
            swap H[p], H[i]
            HeapifyUp(H, p)
```

### Complexity
- Best: O(1)
- Worst: O(log n)

---

## Proof of Heapify-Up by Induction

### Claim
If only node `i` violates heap order with its parent, then `HeapifyUp(H, i)` restores heap property.

### Proof idea
- **Base case**: root has no parent.
- **Step**:
  - If parent ≤ node: done.
  - Else swap with parent and recurse upward.
- Violation moves strictly toward root; height is finite (`O(log n)`).

---

## Core Operation 2: Heapify-Down (Bubble Down)

After removing root (or placing a too-large value at root), bubble down by swapping with smaller child.

### Algorithm
```pseudo
HeapifyDown(H, i):
    m = size(H)
    if 2*i > m:
        return

    if 2*i == m:
        j = 2*i
    else:
        if H[2*i] < H[2*i + 1]:
            j = 2*i
        else:
            j = 2*i + 1

    if H[j] < H[i]:
        swap H[i], H[j]
        HeapifyDown(H, j)
```

### Complexity
- Worst case: O(log n)

---

## Proof of Heapify-Down by Reverse Induction

### Claim
If only node `i` may violate heap property with valid descendants, `HeapifyDown(H, i)` restores the heap.

### Proof idea
- **Base**: leaf nodes (no children) are trivially valid.
- **Step**:
  - pick smaller child `j`
  - if `H[i] > H[j]`, swap and recurse on `j`
- Swapping with smaller child preserves parent relation at current node.

---

## Adding Items to a Heap

1. Insert at the end (maintains complete tree shape)
2. Heapify up

```pseudo
Add(H, value):
    append value to H
    HeapifyUp(H, last_index)
```

Time: **O(log n)**

---

## Removing Items from a Heap

General remove at position `i`:

1. swap `H[i]` with last
2. remove last
3. restore order:
   - if new `H[i]` < parent → heapify up
   - else → heapify down

```pseudo
Remove(H, i):
    swap H[i], H[last]
    delete H[last]

    if i > 1 and H[i] < H[parent(i)]:
        HeapifyUp(H, i)
    else:
        HeapifyDown(H, i)
```

Time: **O(log n)**

---

## Sorting with Heaps: Heap Sort

### Idea
- build heap
- repeatedly extract min (or max for max-heap variant)

```pseudo
HeapSort(V):
    H = empty heap
    for x in V:
        Add(H, x)

    for i in 0..len(V)-1:
        V[i] = H[1]
        Remove(H, 1)

    return V
```

### Complexity
| Phase | Time |
|---|---:|
| Build heap (by repeated insert) | O(n log n) |
| Extract all | O(n log n) |
| Total | O(n log n) |

---

## Real-World Application: Interval Partitioning

Goal: minimum number of rooms for overlapping intervals.

### Greedy strategy
1. Sort intervals by start time
2. Keep min-heap of room finish times
3. For each interval:
   - if earliest finish ≤ new start: reuse room
   - else allocate new room

Complexity: **O(n log n)**

---

## Summary & Complexity Comparison

### Heap operations
| Operation | Time |
|---|---:|
| Find min | O(1) |
| Add | O(log n) |
| Remove root | O(log n) |
| HeapifyUp | O(log n) |
| HeapifyDown | O(log n) |

### Key takeaways
- parent ≤ children is enough
- no full sort needed inside structure
- heaps are ideal for priority queues and scheduling

---

## 💡 Practice Exercises

### Easy
1. Build a min-heap from `[3, 1, 4, 1, 5, 9]`.
2. What is heap height for `n = 1000`?
3. Parent of index 5 (1-based)?

### Medium
4. Trace removing root from a 5-element heap.
5. Why swap with smaller child in HeapifyDown?
6. Draw tree for `[1, 2, 3, 5, 9, 7, 8]`.

### Hard
7. Prove HeapifyUp is O(log n).
8. Why is Heap Sort O(n log n)?
9. Remove maximum from a min-heap efficiently—design it.
