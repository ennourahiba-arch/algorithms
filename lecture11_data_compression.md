# Lecture 11 — Data Compression

This lecture is about **data compression**: how to represent information using fewer bits.

---

## Table of contents
1. [What is data compression?](#what-is-data-compression)
2. [Why compression helps](#why-compression-helps)
3. [Fixed-length vs variable-length encoding](#fixed-length-vs-variable-length-encoding)
4. [Prefix codes](#prefix-codes)
5. [How decoding works](#how-decoding-works)
6. [Average bit length](#average-bit-length)
7. [Binary trees for codes](#binary-trees-for-codes)
8. [A full example](#a-full-example)
9. [The key lemma](#the-key-lemma)
10. [The optimization goal](#the-optimization-goal)
11. [Main idea to remember](#main-idea-to-remember)

---

## What is data compression?

Data compression means **writing the same information using fewer bits**.

Bits are just:
- `0`
- `1`

If we can make a message shorter, we save:
- storage space
- transmission time
- bandwidth

---

## Why compression helps

Imagine a binary string like:

```text
0000000011111111
```

This has a clear pattern:
- a block of zeros
- then a block of ones

Instead of sending every bit, we could send:
- “8 zeros”
- “8 ones”

That is compression.

### Big idea
If data has structure, we can often describe it more efficiently.

---

## Fixed-length vs variable-length encoding

Suppose we want to encode the 26 letters `A` to `Z`.

### Fixed-length encoding
Every letter gets the same number of bits.

Since:
- 4 bits gives only 16 codes
- 5 bits gives 32 codes

we need **5 bits per letter**.

Example:

```text
A = 00000
B = 00001
C = 00010
...
Z = 11001
```

This is easy, but not always efficient.

### Variable-length encoding
Not all letters appear equally often.

For example:
- `E` is common
- `Z` is rare

So we do better if:
- common letters get short codes
- rare letters get long codes

---

## Prefix codes

A **prefix code** is a code where **no code word starts another code word**.

### Good example
```text
A = 0
B = 10
C = 110
D = 111
```

This is prefix-free.

### Why?
- `0` is not the start of `10`, `110`, or `111`
- `10` is not the start of `110` or `111`

### Bad example
```text
A = 0
B = 01
```

This is not prefix-free because `0` is the prefix of `01`.

That causes ambiguity.

---

## How decoding works

With a prefix code, decoding is straightforward.

Suppose:

```text
A = 0
B = 10
C = 110
D = 111
```

And the received bit string is:

```text
01101110
```

We decode from left to right:

- `0` → `A`
- remaining: `1101110`
- `110` → `C`
- remaining: `1110`
- `111` → `D`
- remaining: `0`
- `0` → `A`

So the message is:

```text
A C D A
```

---

## Average bit length

To measure how good a code is, we compute its **average bit length**.

### Formula
If:
- `f_x` = frequency of character `x`
- `length(γ(x))` = number of bits used for `x`

then:

\[
\text{ABL}(\gamma) = \sum_{x \in S} f_x \cdot \text{length}(\gamma(x))
\]

### Meaning
For each character:
- multiply how often it appears
- by how long its code is

Then add everything.

---

## Binary trees for codes

Prefix codes can be represented by a **binary tree**.

### Tree convention
- left edge = `0`
- right edge = `1`
- each character is placed at a **leaf**

### Example tree

```text
                (root)
               /      \
             0/        \1
             /          \
           A            (*)
                       /     \
                    0 /       \1
                     /         \
                    B          (*)
                              /   \
                           0 /     \1
                            /       \
                           C         D
```

This tree gives the code:

```text
A = 0
B = 10
C = 110
D = 111
```

### Why leaves only?
If a character were placed inside the tree, then its code might become the prefix of another code.

Leaves avoid that problem.

---

## A full example

Let’s use 5 letters:

| Character | Frequency |
|----------|-----------|
| A        | 0.32      |
| B        | 0.25      |
| C        | 0.20      |
| D        | 0.18      |
| E        | 0.05      |

A good prefix code could be:

```text
A = 11
B = 01
C = 001
D = 10
E = 000
```

### Tree sketch

```text
                         (root)
                        /      \
                     0 /        \ 1
                      /          \
                    (*)          (*)
                   /   \        /   \
                0 /     \1   0 /     \1
                 /       \    /       \
               (*)       D   B         A
              /   \
           0 /     \1
            /       \
           E         C
```

### Average bit length

\[
\text{ABL} = (0.32 \cdot 2) + (0.25 \cdot 2) + (0.20 \cdot 3) + (0.18 \cdot 2) + (0.05 \cdot 3)
\]

\[
\text{ABL} = 0.64 + 0.50 + 0.60 + 0.36 + 0.15 = 2.25
\]

So the average is:

```text
2.25 bits per character
```

This is much better than fixed-length encoding with 5 bits per character.

---

## The key lemma

If characters are placed only on **leaf nodes** of a binary tree, then the resulting code is **prefix-free**.

### Why?
A leaf cannot be extended to reach another leaf, so one code cannot be the beginning of another.

---

## The optimization goal

We want a tree that minimizes:

\[
\sum f_x \cdot \text{depth}(x)
\]

### Interpretation
- frequent characters should be close to the root
- rare characters can be deeper

That gives the shortest average code length.

---

## Main idea to remember

> **Compression works best when you exploit unequal frequencies.**

So:
- common symbols get short codes
- rare symbols get long codes
- prefix-free codes allow unambiguous decoding
- binary trees are the natural way to represent them

---

## Quick exam version

### Prefix code
A code where no code word is a prefix of another.

### Average bit length
Weighted average of code lengths using symbol frequencies.

### Binary tree
A tree where each leaf is a character and the path from root to leaf is its code.

### Goal
Minimize:

\[
\sum f_x \cdot \text{depth}(x)
\]
