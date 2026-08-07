# Lecture 5: Greedy Algorithms - Interval Scheduling

## Table of Contents
1. [Introduction](#introduction)
2. [Problem Definition](#problem-definition)
3. [Compatible Intervals](#compatible-intervals)
4. [Greedy Algorithm Strategy](#greedy-algorithm-strategy)
5. [Algorithm Implementation](#algorithm-implementation)
6. [Three Greedy Strategies](#three-greedy-strategies)
7. [Correctness Proof](#correctness-proof)
8. [Optimality Proof (Π³ Strategy)](#optimality-proof-π³-strategy)
9. [Examples](#examples)

---

## Introduction

**Greedy Algorithms** are a class of algorithms that make locally optimal choices at each step, hoping to find a global optimum. They are efficient but don't always guarantee the best solution.

However, for the **Interval Scheduling Problem**, we can prove that a specific greedy strategy yields an optimal solution—meaning it produces the best possible answer every time.

---

## Problem Definition

### The Interval Scheduling Problem

**Input**: A set of `n` intervals (or jobs) where each interval is represented as a pair:
```
I = {(s₁, f₁), (s₂, f₂), ..., (sₙ, fₙ)}
```
- `sᵢ` = start time of interval i
- `fᵢ` = finish (end) time of interval i

**Output**: A subset `S ⊆ I` that maximizes |S| (the number of selected intervals)

**Constraint**: All selected intervals must be **compatible** (non-overlapping)

**Goal**: Find the largest possible set of non-overlapping intervals that can be scheduled together.

### Example

```
Timeline visualization:

9:00          11:00
|————— Job 1 ————|

10:00         12:00
  |———— Job 2 ————|

11:30         1:30
      |———— Job 3 ————|

12:00         2:00
      |———— Job 4 ————|
```

---

## Compatible Intervals

### Definition

Two intervals `A = (sₐ, fₐ)` and `B = (sᵦ, fᵦ)` are **compatible** if and only if:

```
fₐ ≤ sᵦ  OR  fᵦ ≤ sₐ
```

In other words, one interval must end before or at the time the other begins.

### Key Points

- Intervals that touch at endpoints **ARE compatible** (end time = start time is allowed)
- Intervals that overlap **ARE NOT compatible**
- Compatibility is symmetric: if A is compatible with B, then B is compatible with A

### Example: Compatible vs. Incompatible

```
Compatible:
|———— A ————|
           |———— B ————|
(A ends when B starts)

Compatible:
|———— A ————|
        |———— B ————|
(A ends before B starts)

Incompatible:
|———— A ————|
      |———— B ————|
(Intervals overlap)
```

---

## Greedy Algorithm Strategy

### The Greedy Approach

At each iteration of the algorithm:
1. Select an interval according to some rule (heuristic)
2. Remove all intervals that are **incompatible** with the selected interval
3. Add the selected interval to the solution
4. Repeat until no intervals remain

### Why Greedy?

We make the "best" local choice at each step, trusting that this leads to a global optimum. This works well for interval scheduling because:
- Choosing an interval doesn't prevent us from choosing other compatible intervals
- Some strategies for choosing intervals guarantee optimality

---

## Algorithm Implementation

### Pseudocode: SELECT_n(I)

```
Algorithm: SELECT_n(I)
────────────────────────────────────

Input: I = set of intervals {(s₁, f₁), (s₂, f₂), ..., (sₙ, fₙ)}

S ← [] (empty list to store selected intervals)

WHILE I is not empty DO
    
    // Step 1: Pick an interval according to rule M
    Pick (sⱼ, fⱼ) ∈ I according to rule M
    
    // Step 2: Identify incompatible intervals
    N ← {(sₖ, fₖ) ∈ I : (sₖ, fₖ) and (sⱼ, fⱼ) are incompatible}
    
    // Step 3: Remove incompatible intervals from consideration
    I ← I \ N
    I ← I \ {(sⱼ, fⱼ)}  (remove the picked interval from I)
    
    // Step 4: Add picked interval to solution
    S.APPEND((sⱼ, fⱼ))

END WHILE

RETURN S (the set of selected intervals)
```

### Complexity Analysis

Let n = |I| (number of input intervals)

- **Time Complexity**: O(n log n) when combined with sorting
  - Sorting intervals by the chosen rule: O(n log n)
  - Main loop: O(n²) in worst case (but optimizable to O(n) with preprocessing)
  
- **Space Complexity**: O(n)
  - Storing the input and output sets

---

## Three Greedy Strategies

### Π¹: "Pick the Shortest Interval"

**Rule**: At each step, select the interval with the smallest duration (fⱼ - sⱼ)

```
Visualization:

All intervals:
|———————— Interval 1 ————————|
  |—— Interval 2 ——|              ← Shortest, pick this
    |——————— Interval 3 ——————|
    
After picking Interval 2:
Remove 1 and 3 (incompatible with 2)
Select new shortest from remaining
```

**Status**: ✓ **CORRECT** - Produces optimal solution

**Intuition**: Shorter intervals leave room for more intervals on either side.

---

### Π²: "Pick the Interval with Smallest Conflict Count"

**Rule**: At each step, select the interval that is incompatible with the fewest other intervals

```
Visualization:

Interval A: conflicts with 5 others
  |—————————— A ——————————|
  |—|  |—|  |—|  |—|  |—|

Interval B: conflicts with 1 other
  |——— B ———|
        |—|             ← B has fewer conflicts, pick this
```

**Status**: ✓ **CORRECT** - Produces optimal solution

**Intuition**: Intervals with fewer conflicts block fewer future options.

---

### Π³: "Pick the Interval with Earliest End Time"

**Rule**: At each step, select the interval with the earliest finish time (smallest fⱼ)

```
Visualization:

Pick the one that ends first:
|—— A ——|                  ← Ends at 11:00
      |—————— B ——————|     ← Ends at 13:00
         |————— C —————|    ← Ends at 12:30

Pick A (ends earliest)

After picking A:
        |—————— B ——————|   ← Ends at 13:00, compatible with A
           |————— C —————|  ← Ends at 12:30, now check compatibility
                   |— D —| ← Maybe compatible too
```

**Status**: ✓ **CORRECT** - Produces optimal solution

**Intuition**: The interval ending earliest leaves the maximum time window for remaining intervals.

---

## Correctness Proof

### Proof that Π³ Strategy Produces an Optimal Solution

We will prove by **mathematical induction** that the greedy algorithm using the "earliest end time" strategy (Π³) always produces an optimal solution.

### Theorem

**"The SELECT_n algorithm using rule Π³ (earliest finish time) returns an optimal solution to the interval scheduling problem."**

### Proof by Induction

#### Base Case: k = 1

When there is only one interval in the input:
- We select it (it has the earliest finish time among all intervals)
- The optimal solution is to select that single interval
- Our algorithm returns a set of size 1, which is optimal

✓ Base case holds.

---

#### Inductive Hypothesis

Assume that for some k ≥ 1:
- After k iterations of the algorithm using rule Π³
- We have selected k intervals: (s₁', f₁'), (s₂', f₂'), ..., (sₖ', fₖ')
- These form a **compatible set** (all pairwise compatible)
- **AND** there exists an optimal solution OPT of the original problem such that:
  - OPT contains all k intervals selected by our algorithm
  - OR can be extended to match OPT's size

---

#### Inductive Step: Proving for k + 1

**Claim**: After the (k+1)ᵗʰ iteration, we can still achieve the same property.

**Proof**:

1. **Selection in iteration k+1**:
   - Among all intervals remaining after removing incompatible intervals from iterations 1 through k
   - We select interval (sₖ₊₁', fₖ₊₁') with the earliest finish time

2. **Compatibility with previous selections**:
   - By the nature of the algorithm, (sₖ₊₁', fₖ₊₁') is compatible with all previously selected intervals
   - (Because we removed all incompatible intervals in previous iterations)
   - Therefore, S = {(s₁', f₁'), ..., (sₖ', fₖ'), (sₖ₊₁', fₖ₊₁')} is a compatible set

3. **Claim about optimality**:
   - Let OPT be an optimal solution to the original problem
   - If OPT contains (sₖ₊₁', fₖ₊₁'), we're done
   - If OPT does NOT contain (sₖ₊₁', fₖ₊₁'), we can show we can **replace** some interval in OPT with (sₖ₊₁', fₖ₊₁')

4. **Replacement argument**:
   - By inductive hypothesis, OPT contains the first k intervals selected by our algorithm
   - Let (sₓ, fₓ) be the next interval in OPT (the (k+1)ᵗʰ interval in OPT)
   - Since we chose (sₖ₊₁', fₖ₊₁') as the earliest finishing time among all remaining intervals
   - We have: **fₖ₊₁' ≤ fₓ**
   
5. **Why replacement works**:
   - Since fₖ₊₁' ≤ fₓ, interval (sₖ₊₁', fₖ₊₁') ends no later than (sₓ, fₓ)
   - Any interval compatible with (sₓ, fₓ) is also compatible with (sₖ₊₁', fₖ₊₁')
   - (Because if an interval starts after fₓ, it also starts after fₖ₊₁')
   - Therefore, we can replace (sₓ, fₓ) with (sₖ₊₁', fₖ₊₁') in OPT without violating compatibility
   - This gives us a new optimal solution OPT' that contains (sₖ₊₁', fₖ₊₁')

6. **Conclusion**:
   - The set S = {(s₁', f₁'), ..., (sₖ', fₖ'), (sₖ₊₁', fₖ₊₁')} selected by our algorithm
   - Is compatible
   - Can be extended to an optimal solution (OPT')
   - Therefore, our algorithm can still achieve optimality at iteration k+1

✓ Inductive step holds.

---

#### Conclusion of Proof

By mathematical induction:
- The algorithm is correct for k = 1
- If it's correct for k, it's correct for k+1
- Therefore, the algorithm is correct for all k ≥ 1

**∴ The greedy algorithm using rule Π³ (earliest finish time) always produces an optimal solution.** ∎

---

## Optimality Proof (Π³ Strategy)

### Alternative Proof: Exchange Argument

This provides additional intuition for why Π³ is optimal.

#### Exchange Argument Structure

**Lemma**: If we have an optimal solution that doesn't follow the greedy choice of Π³, we can transform it into one that does without losing optimality.

#### Proof

Let:
- `S` = solution produced by greedy algorithm (earliest finish time)
- `OPT` = some optimal solution where OPT ≠ S

**Step 1: Find first difference**
```
Let i be the first index where:
- Interval (sᵢ', fᵢ') ∈ S (selected by greedy)
- Interval (sᵢ, fᵢ) ∈ OPT (different from greedy choice)

AND both are the i-th interval in their respective solutions
```

**Step 2: Compare finish times**
```
Since (sᵢ', fᵢ') is the earliest finishing interval available 
at iteration i in the greedy algorithm:

fᵢ' ≤ fᵢ  (greedy choice ends no later than OPT's choice)
```

**Step 3: Exchange intervals**
```
Consider OPT' = OPT with (sᵢ, fᵢ) replaced by (sᵢ', fᵢ')

Since fᵢ' ≤ fᵢ:
- (sᵢ', fᵢ') is compatible with all intervals in OPT that come after (sᵢ, fᵢ)
  (If an interval B starts after fᵢ, it also starts after fᵢ')
  
- OPT' remains a valid solution
- |OPT'| = |OPT|, so OPT' is also optimal
```

**Step 4: Move closer to S**
```
Repeat this process for positions i+1, i+2, ..., until:
OPT is transformed into S while maintaining optimality
```

**Conclusion**: S is optimal because we can transform any optimal solution into S through exchanges, proving S has optimal value. ∎

---

### Key Insight: Why "Earliest Finish Time" Works

The critical property is:

**"By finishing earliest, we leave the maximum time window available for future intervals."**

This greedy choice is optimal because:
1. It doesn't waste time (no other choice at this step enables more future intervals)
2. Future choices remain valid (no additional constraints are introduced)
3. We can always achieve the same number of intervals as any other strategy

---

## Examples

### Example 1: Basic Case

**Input Intervals**:
```
Interval 1: (9:00, 11:00)
Interval 2: (10:00, 12:00)
Interval 3: (11:30, 13:30)
Interval 4: (12:00, 14:00)
```

**Applying Π³ (Earliest Finish Time)**:

```
Sorted by finish time:
1. (9:00, 11:00)     ← Earliest finish at 11:00
2. (10:00, 12:00)    ← Finish at 12:00
3. (11:30, 13:30)    ← Finish at 13:30
4. (12:00, 14:00)    ← Finish at 14:00

Step 1: Select (9:00, 11:00)
S = [(9:00, 11:00)]
Remove incompatible: (10:00, 12:00) is incompatible
Remaining: (11:30, 13:30), (12:00, 14:00)

Step 2: Select (11:30, 13:30) [earliest finish among remaining]
S = [(9:00, 11:00), (11:30, 13:30)]
Remove incompatible: (12:00, 14:00) is incompatible
Remaining: none

Final Solution:
S = [(9:00, 11:00), (11:30, 13:30)]
|S| = 2 intervals scheduled
```

**Verification**: Is this optimal?
- Can we select 3 intervals? No, because intervals 1-4 don't have 3 mutually compatible pairs
- Can we select 2? Yes, as shown
- Therefore |S| = 2 is optimal ✓

---

### Example 2: Comparing Strategies

**Input Intervals**:
```
A: (1, 3)    [duration: 2]
B: (2, 5)    [duration: 3]
C: (4, 6)    [duration: 2]
D: (5, 8)    [duration: 3]
```

**Strategy Π¹ (Shortest duration)**:
```
1. Pick A (1,3) - duration 2
2. Pick C (4,6) - duration 2, compatible with A
3. No more compatible intervals
Result: A, C → |S| = 2 ✓ OPTIMAL
```

**Strategy Π² (Fewest conflicts)**:
```
A conflicts with: B (2 conflicts)
B conflicts with: A, C (2 conflicts)
C conflicts with: B (2 conflicts)
D conflicts with: C (1 conflict) ← Pick this first

1. Pick D (5,8) - 1 conflict
2. Pick A (1,3) - compatible with D
3. No more compatible intervals
Result: A, D → |S| = 2 ✓ OPTIMAL
```

**Strategy Π³ (Earliest finish)**:
```
Sorted by finish time:
A: (1, 3)
C: (4, 6)
B: (2, 5)
D: (5, 8)

1. Pick A (1, 3) - finishes earliest
2. Pick C (4, 6) - compatible and earliest finish
3. No more compatible intervals
Result: A, C → |S| = 2 ✓ OPTIMAL
```

All three strategies give optimal solutions for this example!

---

### Example 3: Where Π³ Excels

**Input Intervals**:
```
A: (1, 2)
B: (1, 3)
C: (2, 4)
D: (3, 5)
E: (4, 6)
F: (5, 7)
```

**Strategy Π¹ (Shortest duration)**:
```
Shortest: A (duration 1)
1. Pick A (1,2)
2. Pick C (2,4) [shortest compatible]
3. Pick E (4,6) [shortest compatible]
Result: A, C, E → |S| = 3
```

**Strategy Π³ (Earliest finish)**:
```
Sorted by finish time:
A: (1,2), B: (1,3), C: (2,4), D: (3,5), E: (4,6), F: (5,7)

1. Pick A (1,2)
2. Pick C (2,4)
3. Pick E (4,6)
Result: A, C, E → |S| = 3 ✓ OPTIMAL

Alternative if we picked B first:
1. Pick B (1,3)
2. Pick D (3,5)
3. Pick F (5,7)
Result: B, D, F → |S| = 3 (also optimal)
```

Notice: Π³ consistently finds optimal solutions even with multiple optimal answers.

---

## Summary

### Key Takeaways

1. **Problem**: Select maximum number of non-overlapping intervals
2. **Greedy Strategy Π³**: Always pick the interval that ends earliest
3. **Why it works**: Earliest finish time maximizes remaining time for future intervals
4. **Proof**: Mathematical induction + exchange argument
5. **Result**: Optimal solution guaranteed
6. **Complexity**: O(n log n) with sorting

### Algorithm Template

```
SORT intervals by finish time
S ← empty set
FOR each interval i in sorted order:
    IF i is compatible with all intervals in S:
        ADD i to S
RETURN S
```

### When to Use Greedy Algorithms

✓ Use when:
- Problem has optimal substructure (optimal solution contains optimal solutions to subproblems)
- Greedy choice property holds (local optimum = global optimum)

✗ Don't use when:
- Problem structure doesn't guarantee optimality
- Example: Coin change (greedy fails for some denominations)

