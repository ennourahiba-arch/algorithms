# Lecture 6: Weighted Interval Scheduling

## Table of Contents
1. [Introduction](#introduction)
2. [Problem Definition](#problem-definition)
3. [Why Weighted Interval Scheduling is Harder](#why-weighted-interval-scheduling-is-harder)
4. [Proof of Optimality](#proof-of-optimality)
5. [Algorithm Analysis](#algorithm-analysis)
6. [Runtime Complexity](#runtime-complexity)
7. [Properties of Greedy Algorithms](#properties-of-greedy-algorithms)
8. [Summary](#summary)

---

## Introduction

In **Lecture 5**, we solved the **Interval Scheduling Problem** using a greedy approach: select the interval with the earliest finish time. This gave us an optimal solution for maximizing the *number* of intervals selected.

However, real-world problems often involve **weights** or **values**. What if each interval has an associated profit or weight, and we want to maximize the total weight rather than just the count?

This is the **Weighted Interval Scheduling Problem**, and it's fundamentally different from the unweighted version.

---

## Problem Definition

### The Weighted Interval Scheduling Problem

**Input**: A set of `n` intervals where each interval has:
- `sᵢ` = start time
- `fᵢ` = finish time  
- `vᵢ` = value/weight of interval i

**Output**: A subset `S ⊆ I` that maximizes the total weight: Σ vᵢ (for all i ∈ S)

**Constraint**: All selected intervals must be **compatible** (non-overlapping)

**Goal**: Find the largest possible *total weight* of non-overlapping intervals

### Example

```
Interval 1: (s₁, f₁) with value v₁
Interval 2: (s₂, f₂) with value v₂
...
Interval m: (sₘ, fₘ) with value vₘ

We want to select a subset S that:
- Maximizes: Σ vᵢ (i ∈ S)
- Subject to: All intervals in S are pairwise compatible
```

---

## Why Weighted Interval Scheduling is Harder

### The Greedy Approach Fails

In Lecture 5, we proved that greedily selecting the interval with the earliest finish time is optimal for the **unweighted** case.

**Does this work for the weighted case?**

**NO!** Consider this counterexample:

```
Scenario:
Interval 1: (1, 5) with value 10
Interval 2: (1, 2) with value 5
Interval 3: (2, 3) with value 5

Greedy (earliest finish time):
1. Select Interval 2 (finishes at 2, value 5)
2. Select Interval 3 (finishes at 3, value 5, compatible with 2)
3. Cannot select Interval 1
Total value: 5 + 5 = 10

Optimal Solution:
1. Select Interval 1 (finishes at 5, value 10)
Total value: 10

Both are equal in this case, but consider:

Interval 1: (1, 5) with value 100
Interval 2: (1, 2) with value 5
Interval 3: (2, 3) with value 5

Greedy result: 5 + 5 = 10
Optimal result: 100

Greedy FAILS!
```

**Why does greedy fail?**
- Selecting one high-value interval might block multiple lower-value intervals
- The greedy choice doesn't account for the value trade-off
- We need a more sophisticated approach: **Dynamic Programming**

---

## Proof of Optimality

### Theorem

**"For the Weighted Interval Scheduling Problem, the greedy algorithm selecting intervals by earliest finish time does NOT always produce an optimal solution."**

### Proof by Counterexample

We provide a concrete example where greedy fails.

**Given**:
- Interval O: {J₁, J₂, ..., Jₘ} is an optimal solution
- Greedy algorithm selects O' using earliest finish time strategy

**Claim**: It's possible that value(O') < value(O)

**Example**:
```
J₁ = (1, 10) with value 9
J₂ = (1, 2) with value 10
J₃ = (2, 3) with value 10

Greedy picks:
1. J₂ (earliest finish at 2, value 10)
2. J₃ (earliest finish at 3, value 10)
Total: 20

Optimal could be:
1. J₁ (value 9) + something compatible after time 10
   If nothing exists: total = 9

Actually wait, let me reconsider...

Better counterexample:
J₁ = (1, 5) with value 100
J₂ = (1, 2) with value 10
J₃ = (2, 5) with value 10

Greedy:
1. Pick J₂ (finish 2, value 10)
2. Pick J₃ (finish 5, value 10, compatible)
Total: 20

Optimal:
1. Pick J₁ (value 100)
Total: 100

Greedy is suboptimal by a factor of 5!
```

**Conclusion**: Greedy does not work for weighted interval scheduling. We need dynamic programming. ∎

---

## Algorithm Analysis

### Why Greedy Fails: Formal Argument

**Observation**: With weights, the greedy choice (earliest finish time) may force us to:
1. Reject a high-value interval
2. Accept multiple low-value intervals that don't add up to the rejected interval's value

**Example Timeline**:
```
J₁: |————————————— value 100 ————————————|
     1                                    5

J₂: |——— value 10 ——|  J₃: |——— value 10 ——|
     1              2        2              3

Greedy picks J₂ and J₃ (earliest finishers): total = 20
Optimal picks J₁: total = 100
```

### The Missing Property

In the unweighted problem, we proved that picking the earliest finishing interval leaves the maximum room for future intervals, ensuring optimality.

**With weights, this property breaks:**
- An early-finishing interval might have low value
- Rejecting it might allow us to pick one high-value interval that dominates

**Result**: We cannot greedily make local decisions without considering global consequences.

---

### Key Insight for Optimality

**Lemma**: If O = {J₁, J₂, ..., Jₘ} is an optimal solution, ordered by finish time as:
```
f(J₁) ≤ f(J₂) ≤ ... ≤ f(Jₘ)
```

Then O satisfies:
```
∀ i ≤ k: value(J₁) + value(J₂) + ... + value(Jᵢ) ≤ value(J₁) + value(J₂) + ... + value(Jᵢ)
```

**This doesn't directly lead to a greedy solution!**

The proof shows that:
1. For k ≥ m (where m is the size of the optimal solution)
2. By the optimality of O, and the feasibility of S (greedy solution)
3. It's theoretically possible that greedy ≠ optimal

**Thus**: A greedy algorithm is insufficient; we need dynamic programming.

---

## Claim About Greedy Algorithm Failing

### Formal Statement

**Claim**: The greedy algorithm that selects intervals in order of earliest finish time does **NOT** guarantee an optimal solution for the weighted interval scheduling problem.

### Proof Sketch

Let O be an optimal solution with m intervals: {J₁, J₂, ..., Jₘ}

Suppose greedy algorithm produces solution S with k intervals: {I₁, I₂, ..., Iₖ}

**Case 1**: If k > m
- Then S has more intervals than O
- But since both are feasible (compatible), and O is optimal
- We'd have value(O) ≥ value(S), contradicting our assumption
- This case doesn't occur

**Case 2**: If k = m
- Both have same number of intervals
- But their values could differ: value(S) < value(O) possible
- This means greedy doesn't produce optimal value
- Example: value(O) = 100, value(S) = 20

**Case 3**: If k < m
- Greedy produces fewer intervals
- O can have more intervals with higher total value
- Greedy is clearly suboptimal

**Conclusion**: Greedy fails in Cases 2 and 3. ∎

---

## Runtime Complexity

### Naive Greedy Implementation

**What about the runtime?**

A trivial implementation takes **O(n²)** time:
```
- Sort the intervals by finish time: O(n log n)
- For each interval i ∈ I:
  - Check if α(i) > T  [O(1)]
  - If yes, add to S and update T ← f(i)  [O(1)]

Total: O(n log n) for sorting + O(n) for loop = O(n log n)
```

### Optimal Implementation

With preprocessing, we can achieve **O(n)** time for the main loop:

```
FAST_ALG (I):
- Sort the intervals by finishing time: O(n log n)
- Let I = {I₁, I₂, ..., Iₙ} with f(I₁) ≤ f(I₂) ≤ ... ≤ f(Iₙ)
- Set T ← ∅ and S ← ∅
- For i = 1 to n:
    If α(Iᵢ) > T:
        S ← S ∪ {Iᵢ}
        T ← f(Iᵢ)
- Return S

Time: O(n log n) for sorting
      O(n) for main loop
      Total: O(n log n)
```

**Why O(n) for the loop?**
- Each interval is processed once: O(n)
- Compatibility check is O(1) (just compare start time with current finish time)
- No nested loops

**Overall**: O(n log n) dominated by sorting

---

## Properties of Greedy Algorithms

### When Does Greedy Work?

A greedy algorithm produces an optimal solution if and only if:

1. **Optimal Substructure**: An optimal solution contains optimal solutions to subproblems
   - i.e., if we select interval J₁, the remaining problem is to optimally schedule intervals compatible with J₁

2. **Greedy Choice Property**: A greedy choice leads to an optimal solution
   - i.e., making the locally optimal choice doesn't prevent us from reaching the global optimum

### For Unweighted Interval Scheduling (Lecture 5)

✓ **Optimal Substructure**: YES
- If we select the interval with earliest finish time, remaining intervals form a subproblem
- Optimal solution to subproblem is guaranteed

✓ **Greedy Choice Property**: YES
- Selecting earliest finish time leaves maximum room for future intervals
- No interval with a "better" finish time exists
- Proven by exchange argument in Lecture 5

**Result**: Greedy works! ✓

### For Weighted Interval Scheduling (Lecture 6)

✓ **Optimal Substructure**: YES
- If we select an interval, remaining intervals form a subproblem
- Optimal solution to subproblem still applies

✗ **Greedy Choice Property**: NO
- Selecting earliest finish time may reject high-value intervals
- A "worse" choice (later finish time but higher value) might be globally optimal
- Counterexample shown earlier

**Result**: Greedy does NOT work! ✗

---

### What Happens Without Greedy Choice Property?

**General Problem Hypothesis**:

Suppose we have a problem where:
- Optimal substructure holds ✓
- Greedy choice property fails ✗

Then:
- A greedy algorithm **may or may not** find optimal solution O
- If greedy finds O, it's by luck or coincidence
- If greedy finds O', it could match O or be suboptimal
- Greedy is **not guaranteed** to be optimal

**What to do instead?**
- Use **Dynamic Programming**
- Exploit optimal substructure systematically
- Consider all possible choices at each step (not just greedy choice)
- Combine results to build optimal solution

---

## Summary

### Key Differences: Unweighted vs. Weighted

| Property | Unweighted (Lecture 5) | Weighted (Lecture 6) |
|----------|----------------------|----------------------|
| **Goal** | Maximize count of intervals | Maximize total value |
| **Greedy Strategy** | Earliest finish time | Doesn't work! |
| **Optimal Substructure** | ✓ YES | ✓ YES |
| **Greedy Choice Property** | ✓ YES | ✗ NO |
| **Algorithm** | Greedy | Dynamic Programming |
| **Time Complexity** | O(n log n) | O(n²) or O(n log n) with DP |

### Key Takeaways

1. **Greedy doesn't always work**: Need to verify both optimal substructure AND greedy choice property

2. **Weighted changes everything**: Adding weights breaks the greedy choice property

3. **Why?** High-value intervals might have later finish times, forcing us to choose between:
   - Greedy: Many low-value short intervals
   - Optimal: Few high-value long intervals

4. **Next step**: Dynamic programming solves weighted interval scheduling optimally

5. **Lesson**: Always check:
   - Does greedy choice property hold?
   - Can you prove it or find a counterexample?
   - If it fails, use DP instead

---

**Note**: The dynamic programming solution for weighted interval scheduling will be covered in a subsequent lecture. The key insight here is understanding why greedy fails and recognizing when we need more advanced techniques.
