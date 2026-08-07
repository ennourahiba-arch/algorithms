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
