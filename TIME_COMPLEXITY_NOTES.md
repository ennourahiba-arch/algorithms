# Time Complexity Notes

This document explains time complexity in a simple way, with examples and step-by-step intuition.

---

## 1. What is time complexity?

Time complexity tells us how the running time of an algorithm grows as the input size grows.

- Input size is usually written as `n`
- We do not care about exact seconds
- We care about how the number of steps increases

Example:
- If `n = 10`, an algorithm may do 10 steps
- If `n = 100`, it may do 100 steps
- Another algorithm may do 100 steps for `n = 10`, but 10,000 for `n = 100`

Time complexity helps us compare algorithms.

---

## 2. Big-O notation

Big-O describes how an algorithm grows as the input gets larger.

Common complexities:

- `O(1)` — constant time
- `O(log n)` — logarithmic time
- `O(n)` — linear time
- `O(n log n)` — linearithmic time
- `O(n²)` — quadratic time
- `O(2ⁿ)` — exponential time
- `O(n!)` — factorial time

Lower is usually better.

---

## 3. How do we calculate time complexity?

We estimate how many basic operations happen as `n` grows.

Rules:

- Ignore constants: `3n` becomes `O(n)`
- Ignore smaller terms: `n² + n + 5` becomes `O(n²)`
- Keep the dominant term: the fastest-growing part

---

## 4. O(1) — constant time

```python
def get_first(arr):
    return arr[0]
```

### What happens?
This always does one access operation.

It does not matter if the array has 10 items or 1,000,000 items.

### Complexity
`O(1)`

---

## 5. O(n) — linear time

```python
def print_all(arr):
    for x in arr:
        print(x)
```

### What happens?
The loop runs once for every element.

If `n = 5`, it prints 5 times.  
If `n = 100`, it prints 100 times.

### Complexity
`O(n)`

---

## 6. O(n²) — quadratic time

```python
def print_pairs(arr):
    for i in arr:
        for j in arr:
            print(i, j)
```

### What happens?
For each element, we loop through the whole array again.

If `n = 3`, total operations are `3 × 3 = 9`.  
If `n = 100`, total operations are `100 × 100 = 10,000`.

### Complexity
`O(n²)`

---

## 7. O(log n) — logarithmic time

```python
def binary_search(arr, target):
    left = 0
    right = len(arr) - 1

    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1

    return -1
```

### What happens?
Binary search works on a sorted array.

Each step cuts the search space in half.

Example with 16 elements:
- 16 → 8 → 4 → 2 → 1

That takes about 4 to 5 checks.

### Why `log n`?
Because we ask:

> how many times can we divide `n` by 2 until we reach 1?

That number is `log2(n)`.

### Complexity
`O(log n)`

---

## 8. O(n log n) — merge sort

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr

    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)
```

### What happens?
Merge sort has two main parts:

1. Split the array into halves repeatedly
2. Merge the sorted halves

### Why `n log n`?
- Splitting happens in about `log n` levels
- Merging takes `n` total work per level

So total work is:

- `n` work per level
- `log n` levels

### Complexity
`O(n log n)`

---

## 9. Best case, average case, worst case

Some algorithms behave differently depending on the input.

### Best case
The easiest input.

### Average case
Expected behavior for typical inputs.

### Worst case
The hardest input.

---

## 10. Linear search example

```python
def search(arr, target):
    for x in arr:
        if x == target:
            return True
    return False
```

### Best case
Target is the first element.

- 1 comparison

Best case: `O(1)`

### Worst case
Target is last or not present.

- `n` comparisons

Worst case: `O(n)`

### Average case
About `n/2` comparisons, which still becomes `O(n)` in Big-O.

---

## 11. Nested loops are not always O(n²)

```python
def example(arr):
    for i in arr:
        for j in range(10):
            print(i, j)
```

### What happens?
- Outer loop: `n` times
- Inner loop: always 10 times

Total: `n × 10`

Since 10 is constant, we ignore it.

### Complexity
`O(n)`

---

## 12. Two loops one after another

```python
def example(arr):
    for x in arr:
        print(x)

    for y in arr:
        print(y)
```

### What happens?
- First loop: `n` steps
- Second loop: `n` steps

Total: `2n`

Ignore the constant `2`.

### Complexity
`O(n)`

---

## 13. Loop that halves the value

```python
def count_steps(n):
    while n > 1:
        n = n // 2
```

### What happens?
Each loop divides `n` by 2.

Example with `n = 32`:
- 32 → 16 → 8 → 4 → 2 → 1

That is about 5 steps.

### Complexity
`O(log n)`

---

## 14. Recursion with one call per step

```python
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

### What happens?
The function calls itself once per value of `n`.

Example with `factorial(5)`:
- factorial(5)
- factorial(4)
- factorial(3)
- factorial(2)
- factorial(1)
- factorial(0)

That is about `n` calls.

### Complexity
`O(n)`

---

## 15. Recursion with branching

```python
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

### What happens?
Each call creates 2 more calls.

The number of calls grows very quickly.

### Complexity
Exponential growth, often written as `O(2ⁿ)` for intuition.

---

## 16. Recursion with three branches

```python
def f(n):
    if n <= 0:
        return 1
    return f(n - 1) + f(n - 2) + f(n - 3)
```

### What happens?
Each call creates 3 more calls.

This grows even faster than the Fibonacci recursion.

### Important note
This is **not** `O(3n)`.

`O(3n)` is the same as `O(n)` because constants are ignored.

This recursion is **exponential**, often described as roughly `O(3ⁿ)` as intuition.

A more exact recurrence is:

```python
T(n) = T(n-1) + T(n-2) + T(n-3) + O(1)
```

### Complexity
Exponential growth

---

## 17. How to estimate complexity by looking at code

Ask these questions:

### A. How many loops are there?
- One loop over `n` → `O(n)`
- Nested loops over `n` and `n` → `O(n²)`

### B. Does the problem size shrink by half?
- `n = n // 2` → `O(log n)`

### C. Does recursion branch?
- One recursive call → often `O(n)`
- Two or more recursive calls → often exponential

### D. Can constants be ignored?
- `O(3n)` becomes `O(n)`
- `O(2n + 5)` becomes `O(n)`

---

## 18. Quick summary table

| Complexity | Meaning | Example |
|---|---|---|
| `O(1)` | constant | array access |
| `O(log n)` | shrinks quickly | binary search |
| `O(n)` | one pass | linear search |
| `O(n log n)` | split + merge | merge sort |
| `O(n²)` | compare many pairs | double loop |
| `O(2ⁿ)` | branching recursion | Fibonacci |
| `O(3ⁿ)` | even more branching | 3-way recursion |

---

## 19. Key intuition

- `O(1)` = same work every time
- `O(log n)` = cut the problem in half repeatedly
- `O(n)` = look at every item once
- `O(n²)` = compare items with each other
- `O(2ⁿ)` and beyond = recursion branches too much

---

## 20. Final note

Time complexity is about how work grows as input grows.

It does not measure exact time in seconds.  
It measures how the number of steps scales.

That is why it is so useful for comparing algorithms.
