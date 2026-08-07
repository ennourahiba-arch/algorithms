# Lecture 2 — Gale–Shapley Algorithm (Stable Matching)

## 1) Problem setup

We are given two sets of equal size:

- \(A = \{a_1, a_2, \dots, a_n\}\) (receivers)
- \(B = \{b_1, b_2, \dots, b_n\}\) (proposers)

Each proposer \(b \in B\) has a strict preference order over all elements of \(A\).  
Each receiver \(a \in A\) has a strict preference order over all elements of \(B\).

Goal: compute a matching \(M\) that is:

1. **Perfect**: everyone in \(A\) and \(B\) is matched.
2. **Stable**: there is no blocking pair.

---

## 2) Key definitions

### Matching
A set of pairs \(M \subseteq A \times B\) such that each person appears in at most one pair.

### Instability (blocking pair)
A pair \((a_i, b_j) \notin M\) is an instability if:

- \(a_i\) prefers \(b_j\) over their partner in \(M\), and
- \(b_j\) prefers \(a_i\) over their partner in \(M\).

### Stable matching
A matching with **no** instabilities.

### Perfect matching
A matching where every element of \(A\) and every element of \(B\) is matched.

---

## 3) Gale–Shapley algorithm (B proposes)

```text
GALE-SHAPLEY (B proposes)

Initially, each proposer p ∈ B and each receiver r ∈ A is FREE.

while there exists a free proposer p ∈ B
      that has not yet proposed to each receiver r ∈ A:

    let p be such a free proposer in B

    let U ⊆ A be the set of receivers
        that p has not yet proposed to

    let r ∈ U be the highest-ranked receiver
        in p's preference order

    if r is FREE then
        MATCH p with r
        (so they are not free anymore)

    else
        let q be the current partner of r

        if r prefers q to p then
            do nothing
            (p's proposal is rejected)

        else
            BREAK UP {q, r}
            MATCH UP  {p, r}
            q becomes FREE
        end if
    end if
end while

return the set of matched pairs M
```

---

## 4) Lemmas and theorems with proofs

## L1
**Statement:** Once a receiver \(r \in A\) receives a proposal, \(r\) stays matched until termination. Also, \(r\)'s partner can only improve over time (according to \(r\)'s preferences).

**Proof:**  
When \(r\) is free and gets a proposal, \(r\) accepts and becomes matched.  
Later, whenever a new proposer \(p\) proposes to \(r\), either:

- \(r\) prefers current partner \(q\): reject \(p\), keep \(q\), or
- \(r\) prefers \(p\): replace \(q\) by \(p\).

So \(r\) never becomes free again, and replacements are only with strictly preferred partners. ∎

---

## L2
**Statement:** For each proposer \(p \in B\), proposals are made in decreasing order of preference (best remaining first), so the sequence of proposed receivers gets worse over time from \(p\)'s perspective.

**Proof:**  
At each proposal step, \(p\) chooses the highest-ranked receiver not yet proposed to.  
After rejection, \(p\) cannot return to previously proposed receivers, so next proposal is lower in \(p\)'s list. ∎

---

## THM 1 (termination)
**Statement:** Gale–Shapley terminates after at most \(n^2\) proposals.

**Proof:**  
Each proposer \(p\in B\) proposes to each receiver \(r\in A\) at most once.  
There are \(n\) proposers and \(n\) receivers, so total proposals are at most \(n\cdot n=n^2\).  
Hence the loop cannot continue forever; algorithm terminates. ∎

---

## L3
**Statement:** The output is a valid matching.

**Proof:**  
A receiver has at most one current partner at any time (explicitly maintained by algorithm).  
A proposer proposes only when free; if accepted while receiver was matched, that previous pair is broken first.  
Therefore no person has two partners simultaneously. Final engagements form a matching. ∎

---

## L4
**Statement:** If a proposer \(p\in B\) is free at some moment, then \(p\) has at least one receiver not yet proposed to.

**Proof (contradiction):**  
Assume \(p\) is free and has already proposed to every receiver in \(A\).  
Then every receiver has received at least one proposal. By L1, every receiver is matched from first proposal onward.  
So all \(n\) receivers are matched. By L3 (matching property) and \(|A|=|B|=n\), this implies all proposers are matched.  
Contradiction: \(p\) was assumed free.  
Hence a free proposer must still have someone left to propose to. ∎

---

## L5
**Statement:** The algorithm outputs a perfect matching.

**Proof:**  
By L3, output is a matching.  
Suppose output is not perfect. Then at termination, some proposer is free.  
By L4, that free proposer must still have an unproposed receiver, so loop condition is still true.  
Contradiction with termination.  
Therefore no proposer is free at end, and because sizes are equal, everyone on both sides is matched: perfect matching. ∎

---

## T2
**Statement:** The output matching \(M^*\) is stable.

**Proof (contradiction):**  
By L5, \(M^*\) is perfect. Assume \(M^*\) has an instability \((a_i,b_j)\).  
Let \(b_j\) be matched in \(M^*\) with \(a_k\), and \(a_i\) be matched with \(b_\ell\), with:

1. \(a_i\) prefers \(b_j\) over \(b_\ell\),
2. \(b_j\) prefers \(a_i\) over \(a_k\).

Because \(b_j\) is preferred by \(a_i\) to final partner \(b_\ell\), proposer-side order implies \(b_j\) must have proposed to \(a_i\) before ending with \(a_k\) (equivalently, proposal-order contradiction argument in notes).  
When \(a_i\) dealt with \(b_j\), either rejected immediately or accepted then later replaced. In both cases, by L1, \(a_i\)'s final partner is at least as preferred as \(b_j\).  
So \(a_i\) cannot prefer \(b_j\) in the way required for blocking at termination. Contradiction.  
Therefore no instability exists; \(M^*\) is stable. ∎

---

## 5) Valid matches and optimality/pessimality

### Valid match (for \(a_i\in A\))
A proposer \(b_j\in B\) is a valid match for \(a_i\) if there exists a stable matching \(M\) with \((a_i,b_j)\in M\).

### \(best(a_i)\)
The best valid match of \(a_i\), according to \(a_i\)'s preference list.

### Theorem
For the side that proposes, Gale–Shapley returns proposer-optimal stable matching; equivalently, each proposer gets their best valid stable partner.

Dual notion for receiver side:

### \(worst(r)\)
For a receiver \(r\), worst valid stable partner among all stable matchings.

When the opposite side proposes, receivers get pessimal valid stable partners.

---

## 6) Important consequence

Stable matching is not always unique.  
Changing who proposes can produce a different stable matching.

---

## 7) Practical coding checklist

- Keep all free proposers in a queue/list.
- For each proposer, store index of next receiver to propose to.
- For each receiver, store current partner.
- Precompute preference rank map for receivers to compare proposers in \(O(1)\).
- Overall complexity: \(O(n^2)\).

---

## 8) Python implementation (B proposes)

```python
def stable_matching(pref_B, pref_A, verbose=False):
    """
    Gale-Shapley where B proposes to A.

    pref_B: dict[str, list[str]]
        proposer preferences (each b in B ranks all a in A)
    pref_A: dict[str, list[str]]
        receiver preferences (each a in A ranks all b in B)

    Returns:
        match_B_to_A, match_A_to_B
    """

    # --- Validation ---
    B = list(pref_B.keys())
    A = list(pref_A.keys())

    if len(B) != len(A):
        raise ValueError("A and B must have same size.")

    setA, setB = set(A), set(B)

    for b, lst in pref_B.items():
        if set(lst) != setA or len(lst) != len(A):
            raise ValueError(f"Invalid preference list for proposer {b}")

    for a, lst in pref_A.items():
        if set(lst) != setB or len(lst) != len(B):
            raise ValueError(f"Invalid preference list for receiver {a}")

    # --- Ranking map for receivers: rank_A[a][b] ---
    rank_A = {}
    for a, lst in pref_A.items():
        rank_A[a] = {b: i for i, b in enumerate(lst)}

    # Free proposers
    free_B = B[:]  # list used as queue
    next_idx = {b: 0 for b in B}  # next receiver index to propose to

    match_A_to_B = {}  # receiver -> proposer
    match_B_to_A = {}  # proposer -> receiver

    step = 1
    while free_B:
        b = free_B.pop(0)
        a = pref_B[b][next_idx[b]]
        next_idx[b] += 1

        if verbose:
            print(f"Step {step}: {b} proposes to {a}")
            step += 1

        if a not in match_A_to_B:
            # a is free
            match_A_to_B[a] = b
            match_B_to_A[b] = a
            if verbose:
                print(f"  {a} was free -> now matched with {b}\n")
        else:
            current = match_A_to_B[a]
            # a compares b vs current
            if rank_A[a][b] < rank_A[a][current]:
                # a prefers new proposer b
                match_A_to_B[a] = b
                match_B_to_A[b] = a
                del match_B_to_A[current]
                free_B.append(current)
                if verbose:
                    print(f"  {a} prefers {b} over {current}")
                    print(f"  {current} becomes free\n")
            else:
                # reject b
                free_B.append(b)
                if verbose:
                    print(f"  {a} rejects {b} (keeps {current})\n")

    return match_B_to_A, match_A_to_B


def is_perfect_matching(match_B_to_A, pref_B, pref_A):
    B = set(pref_B.keys())
    A = set(pref_A.keys())

    if set(match_B_to_A.keys()) != B:
        return False
    if set(match_B_to_A.values()) != A:
        return False
    return True


def is_stable(match_B_to_A, pref_B, pref_A):
    # invert matching
    match_A_to_B = {a: b for b, a in match_B_to_A.items()}

    # ranking maps
    rank_B = {b: {a: i for i, a in enumerate(lst)} for b, lst in pref_B.items()}
    rank_A = {a: {b: i for i, b in enumerate(lst)} for a, lst in pref_A.items()}

    for b in pref_B:
        current_a = match_B_to_A[b]
        for a in pref_B[b]:
            if a == current_a:
                break  # lower options are not preferred by b
            # b prefers a over current_a
            current_b_of_a = match_A_to_B[a]
            if rank_A[a][b] < rank_A[a][current_b_of_a]:
                # a also prefers b over current partner -> blocking pair
                return False
    return True


if __name__ == "__main__":
    pref_A = {
        "hiba":   ["hmad", "hamid", "mohammed"],
        "latifa": ["hamid", "mohammed", "hmad"],
        "ikram":  ["hmad", "mohammed", "hamid"]
    }

    pref_B = {
        "mohammed": ["latifa", "ikram", "hiba"],
        "hmad":     ["hiba", "ikram", "latifa"],
        "hamid":    ["ikram", "latifa", "hiba"]
    }

    match_B_to_A, match_A_to_B = stable_matching(pref_B, pref_A, verbose=True)

    print("Final matching (B -> A):", match_B_to_A)
    print("Perfect:", is_perfect_matching(match_B_to_A, pref_B, pref_A))
    print("Stable :", is_stable(match_B_to_A, pref_B, pref_A))
```

---

## 9) Quick exam summary

- GS always terminates (\(\le n^2\) proposals).
- Output is a matching (L3).
- Output is perfect (L5).
- Output is stable (T2).
- Proposing side gets optimal stable partners (for that side).
- Stable matching may change when proposal side changes.
