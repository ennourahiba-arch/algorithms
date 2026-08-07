# Lecture 7 — Interval Partitioning

## Table of Contents
1. [What is the problem?](#1-what-is-the-problem)
2. [What are resources?](#2-what-are-resources)
3. [Overlap and depth](#3-overlap-and-depth)
4. [Why depth matters](#4-why-depth-matters)
5. [Greedy interval partitioning algorithm](#5-greedy-interval-partitioning-algorithm)
6. [Pseudocode](#6-pseudocode)
7. [Proofs of correctness](#7-proofs-of-correctness)
   - [Lemma 1: OPT(I) ≥ DEPTH(I)](#lemma-1-opti--depthi)
   - [Lemma 2: The algorithm never fails](#lemma-2-the-algorithm-never-fails)
   - [Lemma 3: The algorithm returns a valid assignment](#lemma-3-the-algorithm-returns-a-valid-assignment)
   - [Theorem: The algorithm is optimal](#theorem-the-algorithm-is-optimal)
8. [Why sorting by finishing time fails](#8-why-sorting-by-finishing-time-fails)
9. [How to compute depth efficiently](#9-how-to-compute-depth-efficiently)
10. [Main takeaway](#10-main-takeaway)

---

## 1) What is the problem?

We are given a set of intervals, and we want to place them into the smallest number of resources so that overlapping intervals are never on the same resource.

Think of a resource as a:
- room
- machine
- track
- worker
- slot

---

## 2) What are resources?

A resource is just something that can hold **one interval at a time**.

If two intervals overlap, they cannot use the same resource.

### Example
```text
time →
A: [------]
B:   [------]
```

These two overlap, so they need **2 resources**.

---

## 3) Overlap and depth

The **depth** is the maximum number of intervals active at the same time.

### Example
```text
time →
A: [--------]
B:   [--------]
C:      [--------]
```

At some moment in the middle, all 3 intervals are active together.

So:

**depth = 3**

That means we need at least 3 resources.

---

## 4) Why depth matters

If at some time there are 4 intervals active, then all 4 need different resources at that moment.

So the depth tells us the minimum number of resources we must have.

### Important fact
**OPT(I) ≥ depth(I)**

This means the optimal number of resources can never be smaller than the depth.

---

## 5) Greedy interval partitioning algorithm

The lecture’s algorithm works like this:

1. Sort intervals by **starting time**
2. Process them one by one
3. Put each interval into a resource that is free
4. If no resource is free, open a new one

### Visual idea

Suppose we have intervals like this:

```text
time →
A: [--------]
B:   [--------]
C:      [--------]
```

One possible assignment is:

```text
Resource 1: A --------
Resource 2:   B --------
Resource 3:      C --------
```

At the busiest time, we need 3 resources.

The greedy algorithm always tries to reuse a resource if possible.  
If not, it creates a new one.

That is what makes it efficient and correct.

---

## 6) Pseudocode

Here is the pseudocode in clean form:

```text
ALG(I):
    let d = depth(I)
    sort the intervals increasingly by their starting time
    let I = {(s1, f1), (s2, f2), ..., (sn, fn)} with s1 ≤ s2 ≤ ... ≤ sn

    for j = 1 to n:
        let R = {1, 2, ..., d}
        for i = 1 to j - 1:
            if (si, fi) is incompatible with (sj, fj):
                remove r(i) from R

        if |R| ≥ 1:
            choose some r in R
            assign (sj, fj) to resource r
            set r(j) = r
        else:
            fail

    return r(1), r(2), ..., r(n)
```

### Meaning
- `r(j)` is the resource assigned to interval `j`
- the algorithm tries to reuse a resource that is not conflicting
- if none is available, it would fail, but we will prove that never happens

---

## 7) Proofs of correctness

Now we prove the algorithm works.

---

### Lemma 1: `OPT(I) ≥ DEPTH(I)`

This is the lower bound.

#### Proof
Take a time \( t \) where the maximum number of intervals overlap.

At that moment, `DEPTH(I)` intervals are active simultaneously.

Since overlapping intervals cannot share a resource, each of those intervals must use a different resource.

So any valid solution must use at least `DEPTH(I)` resources.

Therefore:

**OPT(I) ≥ DEPTH(I)**

---

### Lemma 2: The algorithm never fails

We prove that when the algorithm processes interval `j`, there is always at least one available resource.

#### Proof
Let \( d = DEPTH(I) \).

Consider the set \( S_j \) of intervals that were already considered before interval \( (s_j, f_j) \) and that are still active at time \( s_j \).

These are exactly the intervals that conflict with the current interval.

The lecture proves:

**|S_j| ≤ d - 1**

#### Why?
If there were `d` or more such intervals, then together with interval `j` there would be at least `d + 1` intervals active at time `s_j`.

That would contradict the definition of depth, since `d = DEPTH(I)`.

So at most `d - 1` resources are blocked, which means at least one of the `d` resources is free.

Therefore, the algorithm never fails.

---

### Lemma 3: The algorithm returns a valid assignment

#### Proof
Whenever an interval is assigned to a resource, the algorithm removes all resources that would conflict with it.

So the chosen resource is free at the time the interval is scheduled.

Therefore, no two overlapping intervals are assigned to the same resource.

So the assignment is valid.

---

### Theorem: The algorithm is optimal

#### Proof
From Lemma 1:

**OPT(I) ≥ DEPTH(I)**

From Lemma 2 and Lemma 3, the greedy algorithm successfully produces a valid assignment using at most `DEPTH(I)` resources.

So:

**OPT(I) ≤ DEPTH(I)**

Combining both inequalities:

**OPT(I) = DEPTH(I)**

Therefore, the greedy algorithm is optimal.

---

## 8) Why sorting by finishing time fails

The lecture also shows that sorting intervals by **finishing time** does **not** work for interval partitioning.

So:
- **start-time order works**
- **finish-time order does not**

This is different from some other interval problems where finish-time order is useful.

---

## 9) How to compute depth efficiently

The lecture also gives a way to compute depth using a greedy scan.

### Idea
Keep track of the last time each resource was used.

For each new interval:
- if some resource is already free, reuse it
- otherwise create a new resource

This allows us to compute how many resources are needed.

### Complexity
The notes mention a running time around:

- `O(n · depth(I))`

and in the worst case:

- `O(n^2)`

---

## 10) Main takeaway

- We are looking at intervals on a timeline
- We want the minimum number of resources
- A resource is like a room or machine
- The key concept is **depth**
- Depth = maximum number of overlapping intervals at one time
- The greedy algorithm using **start-time order** is optimal
- Sorting by **finish time** does **not** work
