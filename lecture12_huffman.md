# Lecture 12 — Optimal Prefix Codes & Huffman Coding

## Table of Contents
1. [What problem are we solving?](#1-what-problem-are-we-solving)
2. [Core definitions](#2-core-definitions)
3. [Why prefix codes are trees](#3-why-prefix-codes-are-trees)
4. [Objective function (Average Bits per Letter)](#4-objective-function-average-bits-per-letter)
5. [Structural properties of an optimal tree](#5-structural-properties-of-an-optimal-tree)  
   5.1 [Lemma 1: Full binary tree property](#51-lemma-1-full-binary-tree-property)  
   5.2 [Lemma 2: Monotonicity (higher frequency = not deeper)](#52-lemma-2-monotonicity-higher-frequency--not-deeper)  
   5.3 [Lemma 3: Deepest leaf has a leaf sibling](#53-lemma-3-deepest-leaf-has-a-leaf-sibling)  
   5.4 [Combined consequence of Lemmas 1–3](#54-combined-consequence-of-lemmas-13)
6. [Greedy choice and optimal substructure](#6-greedy-choice-and-optimal-substructure)
7. [Huffman algorithm (recursive view)](#7-huffman-algorithm-recursive-view)
8. [Equivalent priority-queue (iterative) implementation](#8-equivalent-priority-queue-iterative-implementation)
9. [Step-by-step example from your notes](#9-step-by-step-example-from-your-notes)
10. [Compute the final ABL carefully](#10-compute-the-final-abl-carefully)
11. [Why Huffman is globally optimal (proof idea)](#11-why-huffman-is-globally-optimal-proof-idea)
12. [Complexity analysis](#12-complexity-analysis)
13. [Important subtleties and common mistakes](#13-important-subtleties-and-common-mistakes)
14. [Mini second example (to strengthen intuition)](#14-mini-second-example-to-strengthen-intuition)
15. [Exam-ready summary](#15-exam-ready-summary)

---

## 1. What problem are we solving?

You have symbols (letters) with different frequencies/probabilities.  
You want to assign binary codewords so that:

- frequently used symbols get **short** codewords,
- rare symbols can tolerate **longer** codewords,
- and decoding is unambiguous and instant (prefix property).

Goal: **minimize expected bits per symbol**.

---

## 2. Core definitions

Let alphabet be \( S = \{x_1,\dots,x_n\} \), each with frequency/probability \(f_x > 0\), and usually \(\sum_x f_x = 1\).

- A **binary code** maps each symbol to a 0/1 string.
- A code is **prefix-free** if no codeword is a prefix of another.
- Prefix-free code ↔ leaf labeling of a binary tree:
  - left edge = `0`, right edge = `1`,
  - codeword of symbol = path from root to its leaf.

Depth of leaf = codeword length.

---

## 3. Why prefix codes are trees

If no codeword is a prefix of another, every symbol must be at a leaf.  
Internal nodes are decision points (“read next bit”).

So coding cost reduces to a tree-depth optimization problem.

---

## 4. Objective function (Average Bits per Letter)

\[
\text{ABL}(T)=\sum_{x\in S} f_x \cdot \text{DEPTH}_T(x)
\]

Interpretation:
- \(f_x\): how often symbol appears,
- \(\text{DEPTH}(x)\): bits used for that symbol.

So ABL = expected codeword length.

Minimizing ABL means best compression under prefix constraint.

---

## 5. Structural properties of an optimal tree

### 5.1 Lemma 1: Full binary tree property

**Claim:** In an optimal prefix tree, every internal node has exactly 2 children.

**Why?**  
If an internal node has only one child, remove that node and connect its parent directly to the child.  
All leaves below get depth reduced by 1; none increase.  
Since frequencies are positive, total ABL strictly decreases — contradiction to optimality.

✅ Therefore optimal tree is **full**.

---

### 5.2 Lemma 2: Monotonicity (higher frequency = not deeper)

If leaf \(u\) is shallower than leaf \(v\), then its frequency must be at least as large:
\[
\text{DEPTH}(u)<\text{DEPTH}(v)\Rightarrow f_u\ge f_v
\]

**Proof idea:**  
Assume opposite: deeper leaf has larger frequency. Swap labels of the two leaves.  
Because larger \(f\) moved to smaller depth, ABL decreases. Contradiction.

✅ So frequent symbols should never be deeper than less frequent ones.

---

### 5.3 Lemma 3: Deepest leaf has a leaf sibling

Take a leaf \(v\) with maximum depth. Let sibling be \(w\).

If \(w\) were internal, it would have a child deeper than \(v\), contradiction.  
Hence sibling must also be a leaf.

✅ Deepest leaves come in sibling pairs.

---

### 5.4 Combined consequence of Lemmas 1–3

There exists an optimal tree where the two **least frequent symbols** are siblings at maximum depth.

This is the key “shape fact” that enables Huffman’s greedy merge.

---

## 6. Greedy choice and optimal substructure

Suppose smallest-frequency symbols are \(y,z\), and in optimal tree they are siblings under parent \(p\).

If you replace \(y,z\) with meta-symbol \(yz\) of frequency \(f_y+f_z\), you get a smaller problem.

- Solve smaller problem optimally.
- Expand \(yz\) back into children \(y,z\).

This gives optimal solution to original problem.

That is exactly:
- **Greedy choice**: combine two minimum frequencies.
- **Optimal substructure**: reduced problem remains optimal subproblem.

---

## 7. Huffman algorithm (recursive view)

1. If only 2 symbols remain: one gets bit `0`, the other `1`.
2. Otherwise:
   - pick two smallest frequencies \(y,z\),
   - merge into meta-symbol \(yz\) with \(f_{yz}=f_y+f_z\),
   - recurse on reduced alphabet,
   - expand \(yz\) into two children \(y,z\).

Important:
- Left/right assignment (`0`/`1`) can be swapped with no effect on ABL.
- Multiple optimal codes may exist if ties happen.

---

## 8. Equivalent priority-queue (iterative) implementation

Practical implementation uses a min-heap:

- Push all symbols keyed by frequency.
- While heap size > 1:
  - pop two minimum nodes,
  - create parent with summed frequency,
  - push parent back.
- Remaining node is root.

This is the same as recursion, just iterative and efficient.

---

## 9. Step-by-step example from your notes

Frequencies:
- \(A=0.32,\ B=0.25,\ C=0.20,\ D=0.18,\ E=0.05\)

### Merge steps
1. Smallest two: \(E(0.05), D(0.18)\) → \(DE(0.23)\)
2. Now: \(A(0.32),B(0.25),C(0.20),DE(0.23)\)  
   Smallest two: \(C(0.20), DE(0.23)\) → \(CDE(0.43)\)
3. Now: \(A(0.32),B(0.25),CDE(0.43)\)  
   Smallest two: \(B(0.25),A(0.32)\) → \(AB(0.57)\)
4. Now two nodes: \(AB(0.57), CDE(0.43)\) → root.

### One valid code assignment
- Put \(AB\) on left, \(CDE\) on right:
  - \(A=00,\ B=01,\ C=10,\ D=110,\ E=111\)

(Equivalent swaps are also optimal.)

---

## 10. Compute the final ABL carefully

Depths:
- \(A,B,C\): depth 2
- \(D,E\): depth 3

\[
\text{ABL}
=0.32\cdot2 + 0.25\cdot2 + 0.20\cdot2 + 0.18\cdot3 + 0.05\cdot3
\]
\[
=0.64+0.50+0.40+0.54+0.15=2.23
\]

✅ Final expected length = **2.23 bits/symbol**.

---

## 11. Why Huffman is globally optimal (proof idea)

Induction on number of symbols \(n\):

- **Base \(n=2\)**: only possible full prefix tree has one bit per symbol, optimal.
- **Inductive step**:
  1. In some optimal tree, two least frequent symbols are deepest siblings.
  2. Contract them into one meta-symbol to obtain reduced instance of size \(n-1\).
  3. By induction, Huffman solves reduced instance optimally.
  4. Expanding meta-symbol restores optimality for original instance.

Thus Huffman is optimal for all \(n\).

---

## 12. Complexity analysis

With min-heap:
- Build heap: \(O(n)\) (or \(O(n\log n)\) depending method),
- \(n-1\) merges, each uses 2 pops + 1 push: \(O(\log n)\) each,
- Total: **\(O(n\log n)\)** time,
- Space: \(O(n)\).

---

## 13. Important subtleties and common mistakes

1. **Not choosing the two smallest every step** → may become suboptimal.
2. **Thinking codewords are unique**: no, many optimal trees may exist.
3. **Confusing tree cost with raw sum of frequencies**: cost depends on depth-weighting.
4. **Forgetting prefix-free constraint**: Huffman is specifically for prefix coding.
5. **Mist in manual steps**: always re-sort frequencies after each merge.
6. **Left/right bit labels** (`0`/`1`) do not change ABL.
7. **When frequencies tie**, any tie-break can still yield optimal ABL (shape/codes may differ).

---

## 14. Mini second example (to strengthen intuition)

Symbols: \(X=0.5,\ Y=0.25,\ Z=0.25\)

Merge:
- \(Y\) and \(Z\) → \(YZ=0.5\)
- \(X\) and \(YZ\) under root

Codes (one version):
- \(X=0,\ Y=10,\ Z=11\)

ABL:
\[
0.5\cdot1 + 0.25\cdot2 + 0.25\cdot2 = 1.5
\]

Intuition: highest probability symbol got shortest code.

---

## 15. Exam-ready summary

- Prefix code optimization = weighted external path length minimization.
- Optimal tree must be full.
- More frequent symbols cannot be deeper than less frequent ones.
- Deepest leaves occur as siblings; least frequent can be placed there.
- Therefore merging two least frequent symbols is a safe greedy step.
- Recurse/iterate until one tree remains.
- Huffman is optimal; complexity \(O(n\log n)\).
