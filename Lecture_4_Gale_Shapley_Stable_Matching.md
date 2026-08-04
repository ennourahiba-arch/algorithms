# Lecture 4: Gale-Shapley Algorithm & Stable Matching

## Table of Contents
1. [Problem Overview](#problem-overview)
2. [Gale-Shapley Algorithm](#gale-shapley-algorithm)
3. [Time Complexity Analysis](#time-complexity-analysis)
4. [Data Structures for O(1) Implementation](#data-structures-for-o1-implementation)
5. [Doubly-Linked List Operations](#doubly-linked-list-operations)
6. [Complete Algorithm Flow](#complete-algorithm-flow)
7. [Stability & Uniqueness](#stability--uniqueness)
8. [Exam Preparation Guide](#exam-preparation-guide)

---

## Problem Overview

### The Stable Matching Problem

**Goal**: Match two groups of people (men and women, or employers and candidates) such that:
- Each person is matched to exactly one person from the other group
- No pair of people would prefer each other over their current matches (stable)

**Input**: 
- Two groups: A (size m) and B (size n), where m = n
- Preference lists: Each person in A ranks all people in B, and vice versa

**Output**: 
- A stable matching (one-to-one pairing with no blocking pairs)

---

## Gale-Shapley Algorithm

### Algorithm Overview

The Gale-Shapley algorithm finds a stable matching by having men propose to women in order of their preferences.

### Pseudocode

```
INITIALLY:
  - Each person is "free"

WHILE LOOP:
  - While there exists a free man aᵢ who hasn't proposed to everyone:
    
    1. Let Q = a free man who hasn't yet proposed to everyone
    
    2. Let B' ∈ B = the set of people Q hasn't yet proposed to
    
    3. Let e_Q ⊆ B' = the person in B' that Q ranks highest in his preference list
    
    4. IF e_Q is FREE:
       - Match Q and e_Q
       - Both are now engaged
    
    5. ELSE (e_Q is already engaged):
       - Let e_k = current partner of e_Q
       
       - IF e_Q prefers Q to e_k:
         * BREAK UP with e_k (e_k becomes free)
         * Match Q and e_Q
       
       - ELSE:
         * Do nothing
         * Q remains free, will try next preference
```

### Step-by-Step Example

**Setup**:
```
Men's preferences:     Women's preferences:
a₁: b₁, b₂, b₃       b₁: a₂, a₁, a₃
a₂: b₂, b₃, b₁       b₂: a₁, a₃, a₂
a₃: b₃, b₁, b₂       b₃: a₃, a₂, a₁
```

**Execution**:
```
Step 1: a₁ proposes to b₁ (his 1st choice)
        → b₁ is free → Engagement: (a₁, b₁)
        → Status: a₁ engaged, a₂ free, a₃ free

Step 2: a₂ proposes to b₂ (his 1st choice)
        → b₂ is free → Engagement: (a₂, b₂)
        → Status: a₁ engaged, a₂ engaged, a₃ free

Step 3: a₃ proposes to b₃ (his 1st choice)
        → b₃ is free → Engagement: (a₃, b₃)
        → Status: All engaged

Final Matching: (a₁,b₁), (a₂,b₂), (a₃,b₃) ✓ STABLE
```

---

## Time Complexity Analysis

### Why O(n²) and Not O(n³)?

**Key Insight**: Each man proposes to each woman **at most once**.

**Analysis**:
- n men × n women = **at most n² proposals total**
- Each proposal takes **O(1) time** (with proper data structures)
- **Total time** = n² proposals × O(1) per proposal = **O(n²)**

### Linear Time in Input Size

**Important Distinction**:
- NOT linear in the number of people (n)
- LINEAR in the size of the input

**Input Size Calculation**:
- Each person must rank all n people on opposite side
- Total people: 2n (n men + n women)
- Each person's preference list: n entries
- **Total input size** = 2n × n = **2n² integers**

**Conclusion**:
- Algorithm runs in O(n²) time
- Input size is 2n²
- **Time is O(input size)** ✓ This is exceptional efficiency!

### Complexity Formula

```
Total Runtime = O(f₁(n) + f₂(n) + f₃(n) + f₄(n))
              = O(max(f₁(n), f₂(n), f₃(n), f₄(n)))

Where f₁, f₂, f₃, f₄ are the 4 main tasks in each iteration.

If each takes O(1): Total = T × O(1) = O(n²)
```

---

## Data Structures for O(1) Implementation

### Overview

To achieve O(1) time per operation, we need four key data structures:

| Structure | Type | Size | Purpose |
|-----------|------|------|---------|
| APREF | Matrix | m × n | Store preference lists |
| NEXT | Vector | m | Track next proposal |
| CURRENT | Array | n | Track current partners |
| RANKING | Matrix | n × m | Compare preferences |

### (A) APREF Array - Preference Lists

**Definition**: m × n matrix where `APREF[i][j]` is the index of the jth most preferred partner of aᵢ

**Example**:
```
If a₃'s preferences are: a₃: b₂ > b₄ > b₁ > b₃

Then:
  APREF[3][1] = 2  (1st choice)
  APREF[3][2] = 4  (2nd choice)
  APREF[3][3] = 1  (3rd choice)
  APREF[3][4] = 3  (4th choice)
```

**Time Complexity**: 
- Access: O(1)
- Construction: O(n²) (done once)

**Why it works**: Direct array indexing into precomputed preference order

---

### (B) NEXT Array - Proposal Tracking

**Definition**: Vector of size m where `NEXT[i]` is the rank of the next woman that aᵢ will propose to

**Purpose**: Avoid re-proposing to the same woman or forgetting where we left off

**Initialization**:
```
For i = 1 to m:
  NEXT[i] = 1  (everyone starts at their 1st preference)
```

**Usage in Algorithm**:
```
When aᵢ is free and it's his turn:
  j ← APREF[i][NEXT[i]]  (Get next woman to propose to)
  Propose aᵢ to bⱼ
  NEXT[i] ← NEXT[i] + 1  (Move to next preference)
```

**Time Complexity**: O(1) per access and update

**Example**:
```
a₂'s preferences: b₂, b₃, b₁

Initial: NEXT[2] = 1
After 1st proposal: NEXT[2] = 2
After 2nd proposal: NEXT[2] = 3
After 3rd proposal: NEXT[2] = 4 (out of range - he's proposed to everyone)
```

---

### (C) CURRENT Array - Current Partner Tracking

**Definition**: Array of size n where `CURRENT[j]` stores the index i of the person aᵢ currently engaged to bⱼ

**Purpose**: Quickly find a woman's current partner and check engagements

**Usage**:
```
If bⱼ is free:
  CURRENT[j] = None

If bⱼ is engaged to aᵢ:
  CURRENT[j] = i

To find bⱼ's current partner:
  partner = CURRENT[j]
```

**Time Complexity**: O(1) for lookup and update

**Implementation Note**: Can also be implemented in O(1) time

---

### (D) RANKING Array - Preference Ranking

**Definition**: n × m matrix where `RANKING[j][i]` is the rank (position) of aᵢ in bⱼ's preference list

**Purpose**: Quickly compare which man a woman prefers (without scanning her entire list)

**Example**:
```
If b₃'s preferences are: b₃: a₂ > a₄ > a₁ > a₃

Then:
  RANKING[3][2] = 1  (a₂ is ranked 1st)
  RANKING[3][4] = 2  (a₄ is ranked 2nd)
  RANKING[3][1] = 3  (a₁ is ranked 3rd)
  RANKING[3][3] = 4  (a₃ is ranked 4th)
```

**How to Check Preferences**:
```
Does bⱼ prefer aᵢ over her current partner aₖ?

if RANKING[j][i] < RANKING[j][k]:
  → Yes, she prefers aᵢ
else:
  → No, she prefers aₖ
```

**Time Complexity**: O(1) for comparison

**Why This Works**: 
- Lower rank = higher preference
- Simple integer comparison in O(1)
- No need to scan the entire preference list

---

## Doubly-Linked List Operations

### Purpose

Maintain the set of free men efficiently. Since men become free and engaged multiple times, we need fast insertion and deletion.

### Structure

```
HEAD → [Node 1] ↔ [Node 2] ↔ [Node 3] ↔ ... ↔ [Node n]

Each Node contains:
  - Value: The person (aᵢ)
  - Prev: Pointer to previous node
  - Next: Pointer to next node
```

### Operations

#### **1. Get First Free Man**
```
Head.Value
```
**Time**: O(1)

#### **2. Get Second Free Man**
```
Head.Next.Value
```
**Time**: O(1)

#### **3. Remove First Node (Man becomes engaged)**
```
Head = Head.Next
Head.Prev = None
```
**Time**: O(1)

**Visual**:
```
Before:
HEAD → [a₁] ↔ [a₂] ↔ [a₃] ↔ ...

After:
HEAD → [a₂] ↔ [a₃] ↔ ...
        (Prev = None)
```

#### **4. Add Element to List (Man becomes free)**
```
N = List()           // Create new node
N.Prev = None
N.Next = HEAD
HEAD.Prev = N
HEAD = N             // New node is now head
```
**Time**: O(1)

**Visual**:
```
Before:
HEAD → [a₂] ↔ [a₃] ↔ ...

After adding a₁:
HEAD → [a₁] ↔ [a₂] ↔ [a₃] ↔ ...
        (N.Prev = None, N.Next = old HEAD)
        (old HEAD.Prev = N)
```

### Usage in Algorithm

```
Initially:
  Free_List = {a₁, a₂, a₃, ..., aₙ}  (as doubly-linked list)

Each iteration:
  1. aᵢ = Head.Value              (Get free man)
  2. Head = Head.Next             (Remove him from free list)
  3. aᵢ proposes to bⱼ
  
  4. If bⱼ accepts aᵢ's proposal:
     - aᵢ stays engaged (not added back)
  
  5. If bⱼ rejects aᵢ:
     - aᵢ becomes free again
     - Add aᵢ back to free list (add to head in O(1))

Final:
  Free_List is empty (everyone engaged)
```

---

## Complete Algorithm Flow

### Main Loop with O(1) Operations

**Initialization**:
```
APREF[1..m][1..n]          // Precomputed preference matrix
NEXT[1..m] = 1             // All start at 1st preference
CURRENT[1..n] = None       // All women are free
RANKING[1..n][1..m]        // Precomputed ranking matrix
Free_List = {a₁, ..., aₙ}  // All men are free (doubly-linked)
```

**Main Loop**:
```
while Free_List is not empty:
  
  // Extract a free man - O(1)
  aᵢ = Free_List.Head.Value
  Free_List.Head = Free_List.Head.Next
  
  // Get next woman to propose to - O(1)
  j = APREF[i][NEXT[i]]
  NEXT[i] = NEXT[i] + 1
  
  // Check if woman is free - O(1)
  if CURRENT[j] == None:
    CURRENT[j] = i           // Match them - O(1)
  
  else:
    // Woman is engaged - O(1)
    aₖ = CURRENT[j]
    
    // Check if woman prefers new proposer - O(1)
    if RANKING[j][i] < RANKING[j][aₖ]:
      // Woman prefers aᵢ - O(1)
      CURRENT[j] = i         // New match
      aₖ becomes free        // Add aₖ back to Free_List - O(1)
    
    else:
      // Woman prefers current partner - O(1)
      aᵢ remains free        // Add aᵢ back to Free_List - O(1)

return CURRENT[]  // Matching result
```

**Time Per Iteration**: O(1)  
**Total Iterations**: O(n²)  
**Total Time**: O(n²)

---

## Stability & Uniqueness

### What is a Stable Matching?

A matching M is **stable** if there is no blocking pair (aᵢ, bⱼ) where:
- Both aᵢ and bⱼ prefer each other over their current partners
- OR both are unmatched

**Theorem**: The Gale-Shapley algorithm always produces a stable matching.

**Proof Sketch**:
1. Suppose at the end, there's a blocking pair (aᵢ, bⱼ)
2. This means aᵢ prefers bⱼ to his current partner
3. At some point, aᵢ must have proposed to bⱼ (since he proposes in preference order)
4. If bⱼ rejected him, she must prefer her current partner
5. This contradicts the assumption that (aᵢ, bⱼ) can block the matching ✓

---

### Multiple Stable Matchings

**Key Insight**: Different preference lists lead to different numbers of stable matchings.

**Example 1 - Unique Matching**:
```
Men's preferences:      Women's preferences:
a₁: b₁, b₂, b₃        b₁: a₁, a₂, a₃
a₂: b₂, b₃, b₁        b₂: a₂, a₃, a₁
a₃: b₃, b₁, b₂        b₃: a₃, a₁, a₂

Stable Matching: (a₁,b₁), (a₂,b₂), (a₃,b₃)
(Everyone gets their 1st choice)

Why unique? Everyone wants their top choice
→ Only one way to satisfy everyone
```

**Example 2 - Multiple Matchings**:
```
Men's preferences:      Women's preferences:
a₁: b₁, b₂            b₁: a₁, a₂
a₂: b₁, b₂            b₂: a₂, a₁

Stable Matching 1: (a₁,b₁), (a₂,b₂) ✓
Stable Matching 2: (a₁,b₂), (a₂,b₁) ✓

Both are stable! (Check: no blocking pairs in either)
```

---

### The 2ⁿ Matchings Question

**Problem**: For n men and n women, construct an instance where exactly 2ⁿ stable matchings exist.

**Why This Matters**:
- Shows the problem can have exponentially many solutions
- The Gale-Shapley algorithm finds only ONE of them
- Different executions (different proposal orders) might find different stable matchings

**Insight for Construction**:
- Create scenarios where multiple pairs are "interchangeable"
- If pairs can be swapped without creating blocking pairs, you get multiple stable matchings
- With careful construction, you can double the number of matchings n times → 2ⁿ matchings

---

## Exam Preparation Guide

### What You MUST Know

#### **1. Algorithm Trace** (Most Important)
- [ ] Can manually run Gale-Shapley step-by-step
- [ ] Can track engagements and rejections
- [ ] Can show the final matching
- [ ] Can verify stability of result

**Practice**: Trace algorithm with n=3 or n=4 examples

#### **2. Time Complexity**
- [ ] Understand why it's O(n²) not O(n³)
- [ ] Know the input size is 2n²
- [ ] Explain why O(n²) = O(input size)

**Practice**: Explain to someone without looking at notes

#### **3. Data Structures**
- [ ] Know what each structure stores
- [ ] Understand why each is O(1)
- [ ] Can explain the RANKING array comparison

**Practice**: Draw pictures of how each structure works

#### **4. Stability Proof**
- [ ] Understand why algorithm produces stable matching
- [ ] Can prove no blocking pair exists at end
- [ ] Can identify blocking pairs in unstable matchings

**Practice**: Construct examples with blocking pairs

---

### Likely Exam Questions

**Type 1: Direct Application**
```
Question: Apply Gale-Shapley to these preference lists.
          Show each step. Is the result stable?

What to do:
1. Write out all steps clearly
2. Show which proposals happen in order
3. Show any rejections and break-ups
4. Verify no blocking pairs exist
```

**Type 2: Complexity Analysis**
```
Question: Why is Gale-Shapley O(n²)? Explain the data structures.

What to do:
1. Explain each data structure and its O(1) property
2. Count total proposals (at most n²)
3. Show O(1) per proposal → O(n²) total
4. Connect to input size (2n²)
```

**Type 3: Stability Proof**
```
Question: Prove Gale-Shapley always produces stable matching.

What to do:
1. Assume there's a blocking pair (aᵢ, bⱼ)
2. Show aᵢ must have proposed to bⱼ
3. Show bⱼ must have rejected him
4. Derive contradiction
```

**Type 4: Construct Examples**
```
Question: Create an instance with multiple stable matchings.
          How many can you create with 2ⁿ?

What to do:
1. Create preferences where pairs are interchangeable
2. Show different stable matchings
3. Verify all are actually stable
```

**Type 5: Edge Cases**
```
Question: What if all men have identical preferences?
          What if preferences are completely opposite?

What to do:
1. Think through the algorithm
2. Explain what happens
3. Discuss how many stable matchings result
```

---

### Study Checklist

**Day 1 - Understand Algorithm**:
- [ ] Read through algorithm pseudocode
- [ ] Trace one example by hand
- [ ] Understand each proposal step

**Day 2 - Practice Tracing**:
- [ ] Trace 3 different examples (n=3, 4, or 5)
- [ ] Create your own example and trace it
- [ ] Time yourself (should take ~5-10 min for n=4)

**Day 3 - Complexity & Data Structures**:
- [ ] Understand each data structure
- [ ] Draw diagrams of how they work
- [ ] Explain why each is O(1)
- [ ] Explain total time complexity

**Day 4 - Proofs & Stability**:
- [ ] Understand why algorithm produces stable matching
- [ ] Practice identifying blocking pairs
- [ ] Create unstable examples to see why they fail

**Day 5 - Exam Practice**:
- [ ] Do practice problems from class
- [ ] Create your own exam questions
- [ ] Solve them under timed conditions
- [ ] Review any weak areas

---

### Key Formulas to Remember

```
Time Complexity:
  Each iteration: O(1)
  Max iterations: n²
  Total: O(n²)

Input Size:
  Preferences per person: n
  Total people: 2n
  Total input: 2n²

Data Structure Access:
  APREF[i][j]: O(1)
  NEXT[i]: O(1)
  CURRENT[j]: O(1)
  RANKING[j][i]: O(1)
  Free_List add/remove head: O(1)

Stability:
  No blocking pair exists in output ✓
  Blocking pair = (aᵢ,bⱼ) preferring each other over current partners
```

---

### Common Mistakes to Avoid

❌ **Mistake 1**: Thinking algorithm runs in O(n) or O(n³)  
✅ **Fix**: Each man proposes at most n times → O(n²)

❌ **Mistake 2**: Not tracking who's free/engaged properly  
✅ **Fix**: Use CURRENT array to always know partnerships

❌ **Mistake 3**: Forgetting to check preference comparison  
✅ **Fix**: Use RANKING array to compare in O(1)

❌ **Mistake 4**: Assuming algorithm finds all stable matchings  
✅ **Fix**: It finds ONE stable matching, not all

❌ **Mistake 5**: Not verifying final matching is actually stable  
✅ **Fix**: Always check no blocking pairs exist

---

## Summary

| Concept | Key Point | Complexity |
|---------|-----------|-----------|
| **Problem** | Find matching with no blocking pairs | - |
| **Algorithm** | Men propose in preference order | O(n²) |
| **APREF** | Preference lookup table | O(1) access |
| **NEXT** | Tracks proposal progress | O(1) per proposal |
| **CURRENT** | Current partnerships | O(1) lookup |
| **RANKING** | Preference comparison | O(1) compare |
| **Free List** | Track unmatched men | O(1) add/remove |
| **Total Time** | All operations combined | **O(n²)** |
| **Stability** | No blocking pairs at end | ✓ Guaranteed |

---

## Additional Resources

### Algorithm Properties
- **Correctness**: Always produces a stable matching
- **Termination**: Guaranteed to finish in O(n²) steps
- **Uniqueness**: Output depends on proposal order and preferences
- **Optimality**: Gale-Shapley is optimal for proposing side (man-optimal)

### Real-World Applications
- Hospital-Resident matching programs (used in USA)
- Job market clearinghouses
- School admissions
- Organ transplant coordination

### Further Study
- Try implementing the algorithm in code
- Analyze what happens when preferences have ties
- Explore variants (women propose instead, preference changes, etc.)
- Research other matching algorithms

---

**Good luck with your exam! 🎓**  
*Remember: Understand the algorithm, practice tracing, explain the efficiency, and verify stability.*
