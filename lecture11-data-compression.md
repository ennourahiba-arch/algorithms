# Lecture 11 — Data Compression

## Table of Contents

1. [What is data compression?](#1-what-is-data-compression)
2. [Why compression is useful](#2-why-compression-is-useful)
3. [Fixed-length vs variable-length encoding](#3-fixed-length-vs-variable-length-encoding)
4. [Non-uniform character frequencies](#4-non-uniform-character-frequencies)
5. [Prefix codes](#5-prefix-codes)
6. [How decoding works (step by step)](#6-how-decoding-works-step-by-step)
7. [Average bit length](#7-average-bit-length)
8. [Binary trees as prefix-free codes](#8-binary-trees-as-prefix-free-codes)
9. [The prefix-free lemma (and why it is true)](#9-the-prefix-free-lemma-and-why-it-is-true)
10. [Tree depth and code length](#10-tree-depth-and-code-length)
11. [The optimization goal](#11-the-optimization-goal)
12. [Full worked example](#12-full-worked-example)
13. [Quick exam-style summary](#13-quick-exam-style-summary)

---

## 1. What is data compression?

Data compression means **representing the same information using fewer bits**.

A bit is just a `0` or a `1`. Every file, message, or text you send is made of bits.

If we can express the same message with fewer bits:
- it takes **less memory** to store
- it is **faster to send** over a network
- it uses **less bandwidth**

The key rule is: the message must still be **fully recoverable** after compression (lossless compression).

---

## 2. Why compression is useful

### Simple example: repeated patterns

Imagine this binary string:

```
0000000011111111
```

This has 16 bits. But we can notice a pattern:
- 8 zeros followed by 8 ones

Instead of sending every single bit, we could just send:
- **"8 zeros, then 8 ones"**

That is a form of compression — we exploit a pattern in the data.

### Text example

For text, letters have very different frequencies. Common letters like `E` appear much more often than rare letters like `Z`. If we know this in advance, we can encode common letters more efficiently.

---

## 3. Fixed-length vs variable-length encoding

### Fixed-length encoding

Suppose we want to encode the English alphabet: `A, B, C, ..., Z` (26 letters).

If every letter gets the **same number of bits**:
- 4 bits → only 2⁴ = 16 combinations → not enough
- 5 bits → 2⁵ = 32 combinations → enough

So fixed-length encoding needs **5 bits per letter** for 26 characters.

```
A = 00000
B = 00001
C = 00010
...
Z = 11001
```

This is simple, but wastes space when some letters appear much more than others.

### Variable-length encoding

Instead of giving every letter 5 bits, we can:
- give **short codes** to **frequent** letters
- give **long codes** to **rare** letters

This saves bits overall, because the frequent letters dominate the total length of a message.

---

## 4. Non-uniform character frequencies

In real languages, letters are **not equally common**.

| Language | Most common | Second | Third | ... | Rarest |
|----------|-------------|--------|-------|-----|--------|
| English  | E (12.70%)  | T (9.10%) | A (8.20%) | ... | Z (0.07%) |
| Italian  | E (11.79%)  | A (11.74%) | I (11.28%) | ... | Z (0.49%) |

Because `E` appears ~180× more often than `Z` in English, it makes no sense to encode both with the same 5-bit code.

**Using short codes for common letters and long codes for rare letters** is the core idea of data compression.

---

## 5. Prefix codes

A **prefix code** (also called a prefix-free code) is a code where:

> **No code word is the beginning (prefix) of another code word.**

### Good example (prefix-free)

```
A = 0
B = 10
C = 110
D = 111
```

- `0` does not start `10`, `110`, or `111` ✓
- `10` does not start `110` or `111` ✓

This code is prefix-free.

### Bad example (not prefix-free)

```
A = 0
B = 01
```

Here `0` **is** the beginning of `01`. This breaks decoding — when you see `0`, you cannot tell whether it means `A` alone or the start of `B`.

---

## 6. How decoding works (step by step)

With a prefix-free code, decoding is simple and unambiguous.

**Rule:** scan bits from left to right. As soon as you see a complete valid code word, output the character and remove those bits. Repeat.

### Example

Code table:
```
A = 0
B = 10
C = 110
D = 111
```

Received bit string:
```
01101110
```

Decoding step by step:

```
Bit string:   0  1 1 0 1 1 1 0
              ^
              0 matches A → output A
              
Remaining:    1 1 0 1 1 1 0
              ^
              1 ... not a match yet
                ^
              1 1 0 matches C → output C
              
Remaining:    1 1 1 0
              ^
              1 1 1 matches D → output D
              
Remaining:    0
              ^
              0 matches A → output A

Result: A C D A
```

Because the code is prefix-free, we **never have to guess** when a code word ends.

---

## 7. Average bit length

We want to measure how efficient a code is overall.

### Formula

Let:
- `S` = the alphabet (set of characters)
- `f_x` = frequency of character `x` (between 0 and 1, all frequencies sum to 1)
- `length(γ(x))` = number of bits in the code for `x`

Then the **average bit length** is:

```
ABL(γ) = Σ  f_x · length(γ(x))
         x∈S
```

In plain words:
> For each character, multiply its frequency by its code length. Sum everything up.

### Why this matters

- If a character with frequency 0.40 has code length 2, it contributes `0.40 × 2 = 0.80`.
- If a character with frequency 0.05 has code length 5, it contributes `0.05 × 5 = 0.25`.

Keeping frequent characters short has a big impact on the total.

---

## 8. Binary trees as prefix-free codes

Prefix codes can be drawn as **binary trees**. This makes them much easier to understand and reason about.

### Rules

- Every edge going **left** represents bit `0`
- Every edge going **right** represents bit `1`
- Characters are placed only at **leaf nodes** (nodes with no children)
- The code for a character = the sequence of bits along the path from the **root** to that **leaf**

### Example tree

```
              (root)
             /      \
           0/        \1
           /          \
          A            (*)
                      /   \
                   0 /     \ 1
                    /       \
                   B         (*)
                            /   \
                         0 /     \ 1
                          /       \
                         C         D
```

This tree gives:

```
A = 0      (one left edge)
B = 10     (right, then left)
C = 110    (right, right, left)
D = 111    (right, right, right)
```

To encode a character, trace the path from root to its leaf and read the edge labels.

---

## 9. The prefix-free lemma (and why it is true)

> **Lemma:** If characters are placed only at leaf nodes of a binary tree, then the resulting code is prefix-free.

### Why is this true? (proof by contradiction)

Suppose the code is **not** prefix-free.

Then there exist two different characters `x ≠ y` such that `γ(x)` is a prefix of `γ(y)`.

In tree terms, this means the path to `x` is a prefix of the path to `y`.

That means node `x` lies **on the path** from the root to node `y`.

But if `x` is on the path to `y`, then `x` must have at least one child — making it an **internal node**, not a leaf.

This contradicts our rule that characters are only at leaves.

Therefore, no code word can be the prefix of another. **The code is prefix-free.** ∎

---

## 10. Tree depth and code length

### Definition

The **depth** of a node is the number of edges from the root to that node.

```
Depth 0:     (root)
              /    \
Depth 1:    (*)    (*)
            / \    / \
Depth 2:  (*) (*)(*)  (*)
```

### Key relationship

For a character at a leaf node:

```
length(γ(x)) = DEPTH_T(x)
```

The code length equals the depth of the leaf.

So:
- **shallow leaf** → **short code** → good for frequent characters
- **deep leaf** → **long code** → acceptable for rare characters

---

## 11. The optimization goal

We can now express the compression problem in tree language.

We want to build a binary tree `T` that **minimizes**:

```
ABL(T) = Σ  f_x · DEPTH_T(x)
         x∈S
```

This is also called the **weighted depth** of the tree.

### What this means visually

A good tree:
- places frequent characters **high up** (small depth)
- places rare characters **lower down** (larger depth)

This is exactly the problem solved by the **Huffman coding algorithm**, which finds the optimal tree efficiently.

---

## 12. Full worked example

### Setup

Alphabet: `{A, B, C, D, E}`

| Character | Frequency |
|-----------|-----------|
| A         | 0.32      |
| B         | 0.25      |
| C         | 0.20      |
| D         | 0.18      |
| E         | 0.05      |

### Prefix code assigned

```
A = 11      (length 2)
B = 01      (length 2)
C = 001     (length 3)
D = 10      (length 2)
E = 000     (length 3)
```

### Tree schematic

```
                  (root)
                 /      \
               0/        \1
               /          \
             (*)           (*)
            /   \         /   \
         0 /     \ 1   0 /     \ 1
          /       \   /         \
        (*)        C  D          A
       /   \
    0 /     \ 1
     /       \
    E         B
```

Reading paths from root to each leaf:

```
E: 0 → 0 → 0 = 000  ✓
B: 0 → 0 → 1 = 001  ✗  (B should be 01, not 001 — see corrected tree below)
```

### Corrected tree schematic

```
                  (root)
                 /      \
               0/        \1
               /          \
             (*)           (*)
            /   \         /   \
         0 /     \ 1   0 /     \ 1
          /       \   /         \
        (*)        B  D          A
       /   \
    0 /     \ 1
     /       \
    E         C
```

Reading paths:

```
E = 0 → 0 → 0 = 000  ✓ (length 3)
C = 0 → 0 → 1 = 001  ✓ (length 3)
B = 0 → 1     = 01   ✓ (length 2)
D = 1 → 0     = 10   ✓ (length 2)
A = 1 → 1     = 11   ✓ (length 2)
```

### Computing ABL

```
ABL = (0.32 × 2) + (0.25 × 2) + (0.20 × 3) + (0.18 × 2) + (0.05 × 3)
    =  0.64       +  0.50      +  0.60       +  0.36       +  0.15
    =  2.25 bits/character
```

Compare this to fixed-length encoding which would need **5 bits per character** — we saved almost 3 bits per character on average!

### Encoding a word

Let's encode `ABCADE`:

```
A → 11
B → 01
C → 001
A → 11
D → 10
E → 000
```

Encoded bit string: `11 01 001 11 10 000` → `1101001111000`

That is 13 bits for 6 characters = ~2.17 bits per character (close to our ABL of 2.25).

### Decoding the bit string

Received: `1101001111000`

```
Read 1  → no match yet
Read 11 → matches A → output A, remaining: 01001111000

Read 0  → no match yet
Read 01 → matches B → output B, remaining: 001111000

Read 0  → no match yet
Read 00 → no match yet
Read 001 → matches C → output C, remaining: 111000

Read 1  → no match yet
Read 11 → matches A → output A, remaining: 1000

Read 1  → no match yet
Read 10 → matches D → output D, remaining: 00

Read 0  → no match yet (wait, 000?)
Hmm — remaining is only 000
Read 0  → no match
Read 00 → no match
Read 000 → matches E → output E, remaining: (empty)

Decoded: A B C A D E ✓
```

---

## 13. Quick exam-style summary

| Concept | Definition |
|---------|------------|
| **Data compression** | Encoding data with fewer bits by exploiting patterns or frequencies |
| **Fixed-length code** | Every character gets the same number of bits (e.g. 5 bits for 26 letters) |
| **Variable-length code** | Characters get different numbers of bits based on frequency |
| **Prefix code** | No code word is a prefix of another; decoding is always unambiguous |
| **Average bit length (ABL)** | `Σ f_x · length(γ(x))` — average bits used per character |
| **Binary tree for codes** | Characters at leaves; edge labels 0/1; path = code word |
| **Prefix-free lemma** | Leaf-only character placement guarantees prefix-free codes |
| **Node depth** | Number of edges from root to node = length of code word |
| **Optimization goal** | Find tree minimizing `Σ f_x · DEPTH_T(x)` (Huffman coding solves this) |

### The one big idea

> **Put frequent characters near the top of the tree (short codes) and rare characters near the bottom (long codes) to minimize the average number of bits used.**
