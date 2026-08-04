# Lecture 3 Explained (Plain English)

## 1) What the professor starts with
The lecture starts with this idea:

- Input size is called n.
- Runtime means: how many steps an algorithm needs when input size is n.
- We care about growth when n becomes large.

So the big question is not exact seconds, but how fast work increases as input gets bigger.

---

## 2) Linear search example (find in a list)
The notes use a function like: find(V, x)

How it works:
1. Look at each element in V one by one.
2. If you find x, return true.
3. If you reach the end and never found x, return false.

What this teaches:
- Best case: x is first element -> very fast.
- Worst case: x is missing (or last) -> you check everything.

So in worst case, work grows directly with list size.

---

## 3) Upper bound idea (the “O” idea)
Your professor defines the “upper bound” concept.

Meaning in simple words:
- Runtime is not worse than some reference growth, after n is large enough.
- Ignore constants like 2x or 100x. Focus on growth shape.

Example from notes:
- 0.5n + sqrt(n) behaves like n for large n.
- So it belongs to the linear-growth family.

Also from notes:
- n and n/2 are considered same growth family (constant factor does not matter).

---

## 4) Important comparison in the notes
The lecture asks:

- If something is at most linear, is it also at most quadratic?  
  Yes.

- If something is at most quadratic, is it also at most linear?  
  Not always.

Why:
- Quadratic can be much bigger than linear for large n.

---

## 5) Prime checking algorithm (first version)
Professor gives a prime-check function:

- Try dividing n by every number from 2 to n-1.
- If any division has no remainder, n is not prime.
- If none divide, n is prime.

Worst case:
- It tries many values (close to n checks).
- So growth is linear.

---

## 6) Prime checking algorithm (better version)
Then professor improves it:

- Only check divisors up to sqrt(n), not all the way to n-1.

Why this is valid:
- If n has factors, one of them must be small enough (at most sqrt(n)).

Result:
- Much fewer checks.
- Growth becomes sqrt(n), which is better than linear.

---

## 7) Lower bound idea (the “Omega” idea)
Then notes define a lower bound.

Meaning:
- Runtime is at least some reference growth (for large n).

Intuition:
- Upper bound = “it won’t be worse than this.”
- Lower bound = “it can’t be better than this.”

In examples like prime checking:
- Some inputs end quickly.
- Some inputs force many checks.
So runtime depends on input type, and the bound you discuss depends on case (best/worst/etc.).

---

## 8) Tight bound idea (the “Theta” idea)
Then professor defines tight bound.

Meaning:
- Runtime is both:
  - not worse than a growth, and
  - not better than that same growth
- So that growth is the exact asymptotic level.

For linear search worst case:
- It has a linear upper bound.
- It also has a linear lower bound.
So worst-case growth is exactly linear.

---

## 9) Efficient algorithms section (stable matching)
The notes move to algorithm efficiency.

For Gale-Shapley:
- Max iterations are proportional to n squared.

If people double:
- n becomes 2n.
- Work becomes about 4 times bigger.

This is a key scaling lesson:
- Quadratic growth increases, but still in a controlled polynomial way.

---

## 10) Contrast with trivial brute-force matching
Professor compares with a naive method:
- Try huge numbers of perfect matchings.
- Number of possibilities explodes very fast.

So the lecture message:
- Good polynomial algorithms are practical.
- Brute-force combinational methods quickly become impractical.

---

## Final takeaway from all pages
Your professor is teaching one connected story:

1. Measure runtime as function of input size.
2. Classify growth using upper/lower/tight bound ideas.
3. Apply to real algorithms (search, prime check, matching).
4. Prefer algorithms with slower growth as n increases.
