# Algorithms — Lecture 1 Complete Notes

Based on Prof. Cherichetti's notes (intro algorithms + stable matching).

---

## 1) Find the maximum value in an array

### Problem
Given an array `V`, return its maximum element.

### Pseudocode
```python
def find_max(V):
    M = V[0]
    for i in range(1, len(V)):
        if V[i] > M:
            M = V[i]
    return M
```

### Why it is correct (intuition)
`M` stores the largest value seen so far.  
After scanning all elements, `M` is the largest in the whole array.

### Complexity
- One loop over `n = len(V)` elements
- Constant work per iteration  
=> **O(n)**

---

## 2) Search for a value `x` in an array

### Problem
Given array `V` and value `x`, determine whether `x` appears in `V`.

### Pseudocode
```python
def find(V, x):
    for y in V:
        if y == x:
            return True
    return False
```

### Why it is correct
- If `x` is in `V`, eventually some `y == x`, so returns `True`.
- If loop ends with no match, `x` is not present, so returns `False`.

### Complexity
Worst case checks all `n` elements  
=> **O(n)**

---

## 3) Missing integer problem

### Problem
Given an array `V` of size `n`, containing all integers from `0` to `n` except exactly one, find the missing integer.

---

### 3.1) Algorithm A(V): naive method

#### Idea
For each `i` from `0` to `n`, check if `i` is in `V` using linear search.

#### Pseudocode
```python
def A(V):
    n = len(V)
    for i in range(0, n + 1):
        if not find(V, i):   # find is O(n)
            return i
```

#### Complexity
- Outer loop: `n + 1` times
- Inner `find`: `O(n)`  
Total: `(n + 1) * O(n) = O(n^2)`

---

### 3.2) Algorithm B(V): sum trick (efficient)

#### Idea
Let:
- `S = 0 + 1 + ... + n`
- `T = sum(V)`

Then missing value is `S - T`.

#### Pseudocode
```python
def B(V):
    n = len(V)
    S = 0
    for i in range(0, n + 1):
        S += i

    T = 0
    for x in V:
        T += x

    return S - T
```

(Equivalent: `S = n*(n+1)//2`)

#### Why it works
If missing number is `m`, then:
- sum of full range = `S`
- sum of given array = `T = S - m`  
So `m = S - T`.

#### Complexity
Two linear passes (or one pass + formula):  
=> **O(n)**

---

## 4) Algorithm Design topics (course roadmap)

From notes / reference to Kleinberg & Tardos:

- Mathematical proofs
- Runtime bounds
- Correctness
- Algorithmic techniques:
  - Local search
  - Greedy
  - Dynamic programming
  - Divide and conquer (divide et impera)

---

## 5) Matching basics

Let `A` and `B` be disjoint sets with:

- `|A| = |B| = m`
- `A ∩ B = ∅`

A **perfect matching** is a set of pairs `(a, b)` with `a in A`, `b in B` such that:
- every `a` appears in exactly one pair
- every `b` appears in exactly one pair

Number of perfect matchings between two sets of size `m`:  
=> **m!**

---

## 6) Stable matching

Each participant has a preference order:

- each `a in A` ranks all `b in B`
- each `b in B` ranks all `a in A`

Given a perfect matching `M`, an **instability** (blocking pair pattern) exists if there are two matched pairs:
- `(a, b)` and `(a', b')` in `M`
such that:
- `a` prefers `b'` to `b`, and
- `b'` prefers `a` to `a'`.

A matching is **stable** if it contains **no instabilities**.

---

## 7) Questions raised in class

1. Does a stable matching always exist?
2. If yes, how do we find one efficiently?

Answered by Gale–Shapley algorithm.

---

## 8) Gale–Shapley algorithm (proposer side A)

### Initialization
All members of `A` and `B` are free.

### Loop
While there exists a free `a_i in A` who has not yet proposed to every `b in B`:

1. Let `b_j` be the highest-ranked member of `B` not yet proposed to by `a_i`.
2. If `b_j` is free: match `(a_i, b_j)`.
3. Else `b_j` is matched with `a_k`:
   - If `b_j` prefers `a_k` to `a_i`: reject `a_i`.
   - Else:
     - break `(a_k, b_j)`
     - match `(a_i, b_j)`
     - `a_k` becomes free.

Stop when no such free proposer remains.

---

## 9) Lemmas from class

### L1
Each `b in B` remains matched from the first time she receives a proposal until the end.  
Also, her partner can only improve over time (according to her preferences).

**Reason:** she accepts first proposal when free; later only switches to better proposer.

### L2
For each `a in A`, the sequence of proposals made by `a` gets worse over time (according to `a`’s preferences).

**Reason:** `a` proposes in descending preference order and never repeats.

---

## 10) Termination theorem and complexity

### Theorem
Gale–Shapley terminates after at most `m^2` proposals (when `|A|=|B|=m`).

### Proof idea
- No proposer proposes to the same receiver twice.
- Each of `m` proposers can propose to at most `m` receivers.
- Total proposals ≤ `m * m = m^2`.

Therefore runtime is **O(m^2)**.

---

## 11) How to quickly recognize O(n) in exercises

Checklist:

1. Define input size (`n`).
2. Count repetitions.
3. One full loop over input => typically `O(n)`.
4. Nested full loops => typically `O(n^2)`.
5. Drop constants and lower-order terms.

Examples:
- `find_max`: `O(n)`
- `find`: `O(n)`
- missing number (naive A): `O(n^2)`
- missing number (sum B): `O(n)`
- Gale–Shapley: `O(m^2)`

---

## 12) What to memorize for exam (from this lecture)

- Definitions: runtime, correctness, perfect matching, instability, stable matching
- Pseudocode and complexity of:
  - `find_max`
  - `find`
  - missing number `A` and `B`
  - Gale–Shapley
- Key lemmas L1 and L2
- Why Gale–Shapley terminates in `O(m^2)`
