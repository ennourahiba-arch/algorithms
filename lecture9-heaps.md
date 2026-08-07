
📚 Lecture 9: Heaps and Heap Sort - Complete Beginner's Guide
📋 Table of Contents
Introduction: What is a Heap?
Why Do We Need Heaps?
How Heaps Work: Array vs Sorted Array vs Heap
Understanding the Heap Property
Representing Heaps as Trees and Arrays
Core Operation 1: Heapify-Up (Bubble Up)
Proof of Heapify-Up by Induction
Core Operation 2: Heapify-Down (Bubble Down)
Proof of Heapify-Down by Reverse Induction
Adding Items to a Heap
Removing Items from a Heap
Sorting with Heaps: Heap Sort
Real-World Application: Interval Partitioning
Summary & Complexity Comparison
Introduction: What is a Heap?
🎯 The Core Idea
Imagine you're running a hospital emergency room. Patients arrive at different times with different urgency levels. You need:

Quick access to the most urgent patient
Quick ability to add new patients
Quick ability to remove treated patients
A Heap is a data structure designed exactly for this!

📦 What is a Heap?
A Heap is a special container that:

Stores N key-value pairs (a patient record and their urgency level, for example)
Always lets you find the minimum value in O(1) time ⚡
Lets you add a new item in O(log n) time ⚡
Lets you remove any item in O(log n) time ⚡
Key Point: The size N is fixed from the start - you know how many items the heap will hold.

💡 What Makes Heaps Special?
Unlike other data structures, heaps balance three important operations:

Operation	Regular Array	Sorted Array	Heap ✨
Find minimum	O(n) 🐢	O(1) ⚡	O(1) ⚡
Add item	O(1) ⚡	O(n) 🐢	O(log n) ⚡
Remove item	O(n) 🐢	O(n) 🐢	O(log n) ⚡
O(1) = instant, O(log n) = very fast, O(n) = slow for big datasets

Why Do We Need Heaps?
The Problem With Other Structures
Let's say you have 1 million patient records:

🔴 Using a Regular Array:

✅ Adding a patient: Super fast! Just put them at the end (0.001 seconds)
❌ Finding the most urgent: Must check all 1,000,000 patients! (1 second)
❌ Processing a patient: Must reorganize the array! (1 second)
🔴 Using a Sorted Array:

✅ Finding most urgent: Super fast! It's at the front (0.001 seconds)
❌ Adding a patient: Must find the right spot and shift everything! (1 second)
❌ Removing a patient: Must shift everything again! (1 second)
✅ Using a Heap:

⚡ Finding the most urgent: Super fast! It's always at the root (0.001 seconds)
⚡ Adding a patient: Very fast! O(log n) ≈ 20 operations for 1 million items
⚡ Removing a patient: Very fast! O(log n) ≈ 20 operations for 1 million items
How Heaps Work: Array vs Sorted Array vs Heap
🎪 Visual Comparison
Regular Array
Code
Values:   [5, 2, 8, 1, 9, 3, 7]
Index:     0  1  2  3  4  5  6

❌ To find minimum: Need to check all 7 values
❌ No order = many comparisons
Sorted Array
Code
Values:   [1, 2, 3, 5, 7, 8, 9]
Index:     0  1  2  3  4  5  6

✅ Minimum is ALWAYS at index 0!
❌ But adding [4] requires shifting: [1,2,3,4,5,7,8,9]
Heap (Our Hero!)
Code
           1                  ← Root (MINIMUM is here!)
         /   \
        2     3              ← All children ≥ parent
       / \   / \
      5   9 7   8

Stored as array: [1, 2, 3, 5, 9, 7, 8]

✅ Minimum always at index 0
✅ Can add/remove in O(log n) time
Key Insight
A heap is partially sorted: it only guarantees that parents are smaller than their children. It doesn't care about the relative order of siblings or cousins.

This partial sorting is enough to keep the minimum at the top, while allowing fast insertions and deletions!

Understanding the Heap Property
🏛️ The Heap Property (Min-Heap)
A Min-Heap satisfies one simple rule:

Every parent node is smaller than or equal to its children

That's it! That's the whole property.

Why This Works
Because of this rule:

The root (top) of the heap is guaranteed to be the smallest value
We don't need to search - it's always in one place
We can reorganize the heap without violating this property
The AHWL Property (Almost Heap With Largest)
Sometimes we need a helper concept:

AHWL Property: A structure where:

Either the tree structure is a complete binary tree, OR
Only the smallest value might violate the parent-child relationship
Everything else satisfies the heap property
Think of it as "almost a heap, but one value might be out of place."

Tree Representation Details
For a node at position i in the array:

Relationship	Formula	Example (i=3)
Left child position	2i (or 2i+1)	6 (or 7)
Right child position	2i+1 (or 2i+2)	7 (or 8)
Parent position	⌊i/2⌋	1
Note: Different systems use different indexing (starting at 0 or 1). The formulas vary slightly, but the concept is the same.

Representing Heaps as Trees and Arrays
🌳 Why Use Both Representations?
A heap is conceptually a tree (easier to visualize and understand), but stored as an array (easier to implement and more memory-efficient).

Visual Translation
As a Tree
Code
           1  (index 1)
         /   \
        2     3    (indices 2, 3)
       / \   / \
      5   9 7   8  (indices 4, 5, 6, 7)
As an Array
Code
Index:    0  1  2  3  4  5  6  7
Value:    -  1  2  3  5  9  7  8
(We often ignore index 0 to make math cleaner)

How We Navigate
From array index to tree relationships:

Code
Array: [_, 1, 2, 3, 5, 9, 7, 8]
        0  1  2  3  4  5  6  7

Node 3 (value 3):
├─ Left child: index 2*3 = 6 (value 7)
├─ Right child: index 2*3+1 = 7 (value 8)
└─ Parent: index ⌊3/2⌋ = 1 (value 1)

Check: Is 3 ≤ 7? Yes ✓
Check: Is 3 ≤ 8? Yes ✓
Therefore: Heap property satisfied at node 3
Initialization
When we create a heap:

Code
Capacity: m (we can hold m items)
Current size: 0 (no items yet)

Only positions 1 to m can be used
Positions beyond m stay empty
Core Operation 1: Heapify-Up (Bubble Up)
🆙 What is Heapify-Up?
When you add a new value to the heap, it might be smaller than its parent. Heapify-Up fixes this by moving the value up the tree until the heap property is restored.

🎬 Step-by-Step Example
Starting heap:

Code
       1
      / \
     4   2
    / \
   8   5
Add value 3:

Code
       1
      / \
     4   2
    / \ /
   8   5 3  ← Added here (at the end)
Is 3 > parent (2)? YES! Violates heap property

Step 1: Swap 3 with parent 2

Code
       1
      / \
     4   3
    / \ /
   8   5 2
Is 3 > parent (1)? NO! Heap property restored ✓

Final result:

Code
       1
      / \
     4   3
    / \ /
   8   5 2
💻 Algorithm
Code
HeapifyUp(H, i):
    IF i > 1:
        parent_index = floor(i / 2)
        IF H[parent_index] > H[i]:
            SWAP H[parent_index] and H[i]
            HeapifyUp(H, parent_index)
In English:

If this node has a parent
And the parent is BIGGER than this node
Swap them
Recursively fix the parent's position
⏱️ Time Complexity
O(log n) - We go up the tree, and a tree of n elements has height log n
Best case: O(1) - If the element is already in the right place
Worst case: O(log n) - If it needs to bubble all the way to the root
🧠 Why This Works (Proof Sketch)
Before swap: Parent might be > this node, and children of this node don't care (they're farther away)

After swap:

This node is now higher (so being bigger is okay - it has fewer children to compare to)
The parent moves down, but we recursively fix it
Eventually, everything settles in the right place
Proof of Heapify-Up by Induction
📐 Formal Statement
Claim: After calling HeapifyUp(H, i), the heap property is satisfied at node i and all its ancestors, assuming the heap property is satisfied everywhere else.

🎯 Understanding What We Need to Prove
Before diving into the proof, let's clarify what "heap property" means at each position:

Heap Property at position i:

Code
For all ancestors a of i:
    H[a].VALUE ≤ H[child].VALUE
What we're proving:

Starting with a violation at position i (where H[i] < H[parent(i)])
After HeapifyUp returns, the violation is fixed
The entire path from i to root satisfies the heap property
💻 Algorithm Review
Code
HeapifyUp(H, i):
    IF i > 1:                           // Node i has a parent
        parent = floor(i / 2)
        
        IF H[parent].VALUE > H[i].VALUE:  // Violation detected
            SWAP H[parent] and H[i]
            HeapifyUp(H, parent)        // Recursively fix parent
        // ELSE: No violation, return (base case)
🧮 Proof by Induction
We'll use strong induction on the height of node i in the tree.

Define height(i) = ⌊log₂(i)⌋ (distance from node i to root)

Base Case: i = 1 (the root)
What to prove: After HeapifyUp(H, 1), the heap property holds.

Proof:

Code
At i = 1:
    The condition "i > 1" is FALSE
    So the function returns immediately without any changes
    
Node 1 has no parent, so the heap property is vacuously true
(there's no parent-child relationship to violate)

✓ Base case holds
Inductive Step: i > 1
Inductive Hypothesis: For all nodes at smaller heights (closer to leaves), HeapifyUp correctly fixes the heap property.

What to prove: HeapifyUp(H, i) correctly fixes the heap property at node i and all its ancestors.

Proof:

We consider two sub-cases:

Sub-case 1: H[parent(i)].VALUE ≤ H[i].VALUE
Code
This means: parent_value ≤ child_value
           
This is EXACTLY the heap property!

Action: The IF condition is false, so we return

Result: 
    ✓ Node i satisfies heap property (parent ≤ child)
    ✓ All ancestors already satisfied it (by assumption)
    ✓ Proof complete for this case
Sub-case 2: H[parent(i)].VALUE > H[i].VALUE
Code
This means: parent_value > child_value
           
This VIOLATES the heap property - we must fix it!

Action: 
    STEP 1: SWAP H[parent(i)] and H[i]
    
    After the swap:
    - H[i] now contains the OLD parent_value (larger)
    - H[parent(i)] now contains the OLD H[i] (smaller)
    
    STEP 2: Call HeapifyUp(H, parent(i))
    - By the Inductive Hypothesis, this call will fix the heap property
      at parent(i) and all its ancestors
    
    STEP 3: Analyze the result
Why the swap fixes position i:

Code
Before swap:
    parent(i)    has value X (larger)
       ↓
    i            has value Y (smaller)
    ├─ left_child  (was already ≥ Y before swap)
    └─ right_child (was already ≥ Y before swap)

After swap:
    parent(i)    has value Y (smaller) ← HeapifyUp will fix this
       ↓
    i            has value X (larger)
    ├─ left_child  (still ≥ Y, so still ≥ X? NO!)
    └─ right_child (still ≥ Y, so still ≥ X? NO!)

Wait - we need to verify that X ≥ left_child and X ≥ right_child!
Key Observation:

Before the swap, we had:

Code
Position i:    H[i] = Y
├─ H[2i] = left_child
└─ H[2i+1] = right_child

By the AHWL property (Almost Heap With Largest):
- If 2i ≤ m: left_child ≥ Y
- If 2i+1 ≤ m: right_child ≥ Y
After the swap:

Code
Position i:    H[i] = X (the old parent)
├─ H[2i] = left_child  (still ≥ Y)
└─ H[2i+1] = right_child (still ≥ Y)

Since X was PARENT of i in the tree:
    Before swap: X > Y (otherwise we wouldn't swap)
    
But what about X vs children of i?
    - We know children ≥ Y
    - We know X > Y
    - Therefore: children ≥ Y < X
    
Hmm, this doesn't immediately prove X ≥ children...
🔧 Refined Proof (More Careful)
Let me restructure this more carefully.

Key Insight: The AHWL Property
Definition: A structure satisfies the AHWL property if:

It's a complete binary tree
At most ONE node violates the heap property
That node is smaller than its parent
Crucial Fact: Before calling HeapifyUp(H, i), the tree satisfies AHWL with the violation only at node i.

Revised Inductive Proof
Claim: If the heap satisfies AHWL at node i (violation at i, valid everywhere else), then HeapifyUp(H, i) makes the entire heap satisfy the heap property.

Base Case: i = 1

Code
i = 1 is the root
    If i = 1, then "i > 1" is false
    Function returns without changes
    Root has no parent, so heap property at root holds ✓
Inductive Case: i > 1

Code
Assume: 
    - The heap satisfies AHWL at node i
    - This means: H[i] < H[parent(i)], but everywhere else is valid

Let p = parent(i)

Case 1: H[p] ≤ H[i]
    The violation is already fixed (contradiction)
    Function returns - heap property satisfied ✓

Case 2: H[p] > H[i]
    The violation exists at i
    
    Action: SWAP H[p] ↔ H[i]
    
    After swap:
    - H[p] now has the smaller value (old H[i])
    - H[i] now has the larger value (old H[p])
    
    State after swap:
    - Node i has the larger value
    - All children of i still satisfy heap property with this larger value
      (because children were ≥ old H[i], and old H[i] < old H[p])
    - Node p now might violate heap property with ITS parent
    
    Next: Call HeapifyUp(H, p)
    
    By Inductive Hypothesis:
        This call recursively fixes p and all ancestors
        Since the AHWL property now holds at p (violation only there),
        HeapifyUp(H, p) will restore full heap property
    
    Result: Entire heap satisfies heap property ✓
📋 Complete Structured Proof
Theorem: HeapifyUp(H, i) correctly restores the heap property.

Proof by Complete Induction on height:

Define height(i) = ⌊log₂(i)⌋ (distance from node i to root)

Base Case: height(i) = 0 (i = 1, the root)

Code
At root: i > 1 is false
Return immediately
Root has no parent - heap property vacuously true ✓
Inductive Step: Assume HeapifyUp works for all nodes with height < h. Consider a node i with height(i) = h.

Code
IF i > 1:  // i is not the root
    parent = floor(i/2)
    
    CASE A: H[parent] ≤ H[i]
        Heap property already satisfied
        Return - done ✓
    
    CASE B: H[parent] > H[i]
        SWAP H[parent] ↔ H[i]
        
        Claim: After swap, the only violation is at 'parent'
        Proof of claim:
            - Node i now has larger value
            - Children of i had value ≥ old H[i]
            - New H[i] = old H[parent] > old H[i]
            - So children of i still satisfy property ✓
            
        Call HeapifyUp(H, parent)
        
        Observation: height(parent) < height(i)
        By Inductive Hypothesis: HeapifyUp(H, parent) is correct
        Therefore: After recursive call, entire heap is valid ✓

QED
🎨 Visual Proof by Example
Let's trace through a concrete example to see the induction in action:

Code
Initial tree (violation at node 5):

           10                height = 2 (root)
          /  \
         7    8             height = 1
        / \  / \
       3  5 2   9           height = 0 (leaves)
           ↑
        VIOLATION: 5 < 7
Iteration 1: HeapifyUp(H, 5) [height = 0]

Code
At node 5:
    i = 5 > 1 ✓
    parent = floor(5/2) = 2, which is node 7
    H[2] = 7, H[5] = 5
    7 > 5 → SWAP!

After swap:
           10
          /  \
         5    8
        / \  / \
       3  7 2   9
         ↑
        Violation now at node 2
        
Recursive call: HeapifyUp(H, 2) [height = 1]
Iteration 2: HeapifyUp(H, 2) [height = 1]

Code
At node 2:
    i = 2 > 1 ✓
    parent = floor(2/2) = 1, which is node 10
    H[1] = 10, H[2] = 5
    10 > 5 → SWAP!

After swap:
           5
          / \
        10   8
        / \ / \
       3  7 2  9
       ↑
      Violation now at node 1 (root)
      
Recursive call: HeapifyUp(H, 1) [height = 2]
Iteration 3: HeapifyUp(H, 1) [height = 2]

Code
At node 1 (root):
    i = 1 > 1? NO!
    Return immediately

Final tree:
           5
          / \
        10   8
        / \ / \
       3  7 2  9

✓ Heap property satisfied everywhere!
Core Operation 2: Heapify-Down (Bubble Down)
🔽 What is Heapify-Down?
When you remove the root or put a new value at the root, it might be larger than its children. Heapify-Down fixes this by moving the value down the tree.

🎬 Step-by-Step Example
Heap with 5 items:

Code
       1
      / \
     2   3
    / \
   4   5
Remove the 1 (the minimum):

Code
       5        ← Move last item to root
      / \
     2   3
    /
   4
Is 5 > children (2 and 3)? YES! Violates heap property

Step 1: Swap 5 with SMALLER child (2)

Code
       2        ← Swap with 2 (smaller than 3)
      / \
     5   3
    /
   4
Is 5 > children (4)? YES! Need to continue

Step 2: Swap 5 with its child (4)

Code
       2
      / \
     4   3
    /
   5
No more children below 5 - DONE ✓

💻 Algorithm
Code
HeapifyDown(H, i):
    // Count how many items are in heap
    m = current_size
    
    // If no children, we're done
    IF 2*i > m:
        RETURN
    
    // Find the smaller child
    IF 2*i = m:
        // Only left child exists
        j = 2*i
    ELSE:
        // Both children exist - pick the smaller one
        IF H[2*i] < H[2*i + 1]:
            j = 2*i
        ELSE:
            j = 2*i + 1
    
    // If this node is bigger than its smaller child, swap
    IF H[j] < H[i]:
        SWAP H[i] and H[j]
        HeapifyDown(H, j)
Key insight: Always swap with the smaller child. This keeps the heap property at all nodes.

⏱️ Time Complexity
O(log n) - Height of the tree
Each recursive call goes one level down
Tree with n elements has height ≈ log₂(n)
🧠 Why We Swap With The Smaller Child
Code
Example: i has two children

       10
      /  \
     3    4

If we swap with 4:
       4
      / \
     3   10   ← 10 is NOT smaller than 3, so heap property broken!

If we swap with 3:
       3
      / \
     10  4   ← This is correct! 3 is smaller than both children
Proof of Heapify-Down by Reverse Induction
📐 Formal Statement
Claim: If a heap satisfies the AHWL property (Almost Heap With Largest) at node i—meaning only node i might violate the heap property, with all its descendants valid—then HeapifyDown(H, i) restores the complete heap property for the entire subtree rooted at i.

🎯 Understanding AHWL Property
AHWL Property at node i:

Code
- Node i might be larger than its children (violation at i)
- All descendants of i satisfy the heap property with each other
- Everything in other subtrees is fine

Visual:
           i (might violate heap property)
         /   \
       ✓       ✓  (all descendants here are valid)
      / \     / \
     ✓   ✓   ✓   ✓
Key Insight: The violation is ONLY at node i, propagating downward potentially. Everything below could be fine, but node i might be out of place.

💻 Algorithm Review
Code
HeapifyDown(H, i):
    m = current_size_of_heap
    
    // If node i has no children
    IF 2*i > m:
        RETURN (i is a leaf, heap property trivially satisfied)
    
    // If node i has only left child
    ELIF 2*i = m:
        j = 2*i  (left child index)
    
    // If node i has both children
    ELSE:
        left_child = 2*i
        right_child = 2*i + 1
        
        // Find the smaller child
        IF H[left_child].VALUE ≤ H[right_child].VALUE:
            j = left_child
        ELSE:
            j = right_child
    
    // If child is smaller than parent, swap and recurse
    IF H[j].VALUE < H[i].VALUE:
        SWAP H[i] and H[j]
        HeapifyDown(H, j)
🧮 Proof by Reverse Induction
We use reverse induction on the height of node i, starting from the leaves and working up to the root.

Why "reverse"?

Normal induction goes from base case (simple) up to complex cases
Here, leaves are simple (no children), and we work upward to complex cases (nodes with children)
We start at height 0 and go up, working "backwards" from leaves to root
Base Case: Leaf Nodes (height = 0)
What to prove: For any leaf node i, HeapifyDown(H, i) correctly maintains the heap property.

Proof:

Code
A leaf node has no children.

At leaf node i:
    2*i > m  (no children exist)
    
The condition "2*i > m" is TRUE
Function returns immediately without any changes

Result: 
    Leaf has no children to compare with
    Heap property is VACUOUSLY TRUE at a leaf
    
✓ Base case holds
Inductive Case: Nodes with Height h > 0
Inductive Hypothesis:

Code
For all nodes j with height(j) < h:
    If AHWL property holds at j,
    then HeapifyDown correctly restores full heap property
What to prove:

Code
For a node i with height h:
    If AHWL property holds at i,
    then HeapifyDown(H, i) restores full heap property
Proof:

Consider a node i at height h > 0. By definition, i has at least one child.

We have three cases:

Case 1: Node i has only the left child (2*i = m)
Code
Initial state:
           i
          /
    left_child

Condition: 2*i = m (exactly one child exists)

Sub-case 1a: H[left_child] ≥ H[i]
    ├─ Heap property already satisfied
    ├─ IF condition is false: H[j] < H[i] is false
    └─ Return without changes
    
    Result: 
        ✓ Node i satisfies property (parent ≤ child)
        ✓ Descendants already valid (AHWL assumption)
        ✓ Done ✓

Sub-case 1b: H[left_child] < H[i]
    ├─ Heap property is violated at i
    ├─ j = left_child
    ├─ H[j] < H[i] is true
    ├─ SWAP H[i] ↔ H[left_child]
    
    After swap:
    
           left_child (had smaller value, now at i)
          /
       i  (had larger value, now at left_child)
    
    ├─ Call: HeapifyDown(H, left_child)
    ├─ Observation: height(left_child) < height(i)
    ├─ By Inductive Hypothesis: This recursive call is correct
    
    Result:
        ✓ Entire subtree rooted at left_child is now valid
        ✓ Node i now has the smaller value (parent)
        ✓ Full heap property restored ✓
Case 2: Node i has both children (2*i < m)
Code
Initial state:
           i
         /   \
    left    right
    
Both children exist.

Sub-case 2a: H[i] ≤ H[left] AND H[i] ≤ H[right]
    ├─ Node i is smaller than both children
    ├─ Heap property ALREADY satisfied at i
    ├─ IF condition is false: H[j] < H[i] is false
    └─ Return without changes
    
    Result:
        ✓ Node i ≤ all its children ✓
        ✓ Descendants are valid (AHWL assumption)
        ✓ Done ✓

Sub-case 2b: H[i] > H[left] OR H[i] > H[right]
    ├─ Heap property IS violated at i
    
    Step 1: Find smaller child
    
        IF H[left] ≤ H[right]:
            j = left
        ELSE:
            j = right
    
    We denote the smaller child as j:
        H[j] = min(H[left], H[right])
        
    Step 2: Swap with smaller child
    
        SWAP H[i] ↔ H[j]
        
        Why swap with the SMALLER child?
        ─────────────────────────────────
        
        Suppose we have:
                   10
                 /    \
                3      8
        
        If we swapped with 8:
                   8
                 /    \
                3      10
        
        Check: Is 8 ≤ 3? NO! Property broken at left child!
        
        But if we swapped with 3:
                   3
                 /    \
                10     8
        
        Check: Is 3 ≤ 10? YES ✓
        Check: Is 3 ≤ 8? YES ✓
        Property satisfied!
        
        General principle:
        ──────────────────
        After swap with j (the smaller child):
            - Node i moves down to position j
            - Node i now has value min(left, right)
            - This means node i's value is now ≤ all siblings
            - And node i's value is ≤ its (former) parent's value
            - So both parent-child relationships are valid!
    
    Step 3: Recurse
    
        Call: HeapifyDown(H, j)
        
        Why is this valid?
        ──────────────────
        - Node j previously had the smaller value
        - The value at j is now smaller (the old i value)
        - So there's a potential violation at j
        - But all descendants of j are still valid!
        - Therefore, AHWL property holds at j
        
        - Observation: height(j) < height(i)
        - By Inductive Hypothesis: HeapifyDown(H, j) is correct
        
        After recursive call:
        ────────────────────
            ✓ Subtree at j is completely valid
            ✓ Node i at position j has the right value
            ✓ Entire subtree rooted at original i is valid ✓
📋 Complete Structured Proof
Theorem: If AHWL property holds at node i, then HeapifyDown(H, i) restores the complete heap property for the subtree rooted at i.

Proof by Reverse Complete Induction on height:

Define height(i) = max distance from i to any leaf in its subtree

Base Case: height(i) = 0 (Leaf nodes)
Code
At a leaf i:
    There are no children
    Condition 2*i > m is TRUE
    Return immediately
    
Leaf property: Vacuously satisfies heap property
✓ Base case holds
Inductive Step: height(i) = h > 0
Inductive Hypothesis: For all nodes j with height(j) < h, if AHWL holds at j, then HeapifyDown(H, j) correctly restores heap property.

Proof for node i with height h:

Node i has at least one child (since height > 0).

Code
Determine children:
    left = 2*i
    right = 2*i + 1
    
    IF 2*i > m:
        ✓ No children (contradicts height > 0)
        Return ✓
    
    IF 2*i = m:
        j = 2*i (only left child)
        
        IF H[left].VALUE ≥ H[i].VALUE:
            ✓ Heap property satisfied
            Return ✓
        
        ELSE:
            ✗ Heap property violated
            SWAP H[i] ↔ H[left]
            
            After swap:
                - H[i] has the smaller value ✓
                - H[left] has the larger value
                - AHWL property holds at left (only it might violate)
                - height(left) < height(i)
            
            Call HeapifyDown(H, left)
            By IH: This call fixes the entire subtree at left ✓
    
    IF 2*i < m:  // Both children exist
        Choose smaller child:
            j = smaller of {H[2*i], H[2*i+1]}
        
        IF H[i].VALUE ≤ H[j].VALUE:
            ✓ Heap property satisfied
            Return ✓
        
        ELSE:
            ✗ Heap property violated
            SWAP H[i] ↔ H[j]
            
            After swap:
                - H[i] has value = min(H[left], H[right]) ✓
                - H[j] has value > min(left, right)
                
                Key claim: H[i] ≤ H[other_child]
                ─────────────────────────────────
                If j = left:
                    H[i] = H[left] ≤ H[right] by definition of j
                If j = right:
                    H[i] = H[right] < H[left] by definition of j
                    So definitely H[i] ≤ H[left]
                
                Therefore: H[i] ≤ both children ✓
            
                - AHWL property holds at j
                  (j might violate with i's value, but descendants ok)
                - height(j) < height(i)
            
            Call HeapifyDown(H, j)
            By IH: This call fixes the entire subtree at j ✓
            
        Result:
            - Subtree at j is completely valid
            - Node i at position j is correctly placed
            - Entire subtree rooted at i is valid ✓

QED
🎨 Visual Proof by Example
Let's trace through a concrete example showing the reverse induction:

Code
Initial tree (violation at root):

           20              ← height 2
          /  \
         5    10           ← height 1
        / \  / \
       2  3 6  9           ← height 0 (leaves)
       
AHWL property: Violation at node 20, but:
               5 ≤ its children ✓
               10 ≤ its children ✓
               All leaves are trivially valid ✓
Step 1: HeapifyDown(H, leaves) - height 0

Code
These satisfy heap property trivially (no children)
✓ Base case applies directly
Step 2: HeapifyDown(H, node 5) - height 1

Code
Node 5 (has children 2 and 3):
    2*node_5 < m: Both children exist
    
    Find smaller child:
        H[2] = 2, H[3] = 3
        j = 2 (left child, smaller)
    
    Check: H[5] ≤ H[2]?
        5 ≤ 2? NO - violation exists!
    
    SWAP H[5] ↔ H[2]:
           5 → 2
           2 → 5
    
    Result:
           2 ← correctly placed (≤ both 5 and 3) ✓
          / \
         5   3
    
    Call: HeapifyDown(H, 5)
        5 is now a leaf (height 0)
        Base case: Return immediately ✓
    
    Subtree at node 5 is now valid ✓
Step 3: HeapifyDown(H, node 10) - height 1

Code
Node 10 (has children 6 and 9):
    Find smaller child:
        H[6] = 6, H[9] = 9
        j = 6 (left child, smaller)
    
    Check: H[10] ≤ H[6]?
        10 ≤ 6? NO - violation exists!
    
    SWAP H[10] ↔ H[6]:
           10 → 6
           6 → 10
    
    Result:
           6 ← correctly placed ✓
          / \
         10  9
    
    Call: HeapifyDown(H, 10)
        10 is now a leaf (height 0)
        Base case: Return immediately ✓
    
    Subtree at node 10 is now valid ✓
Step 4: HeapifyDown(H, root 20) - height 2

Code
Current tree after steps 1-3:
           20
          /  \
         2   6
        / \ / \
       5  3 10 9
Node 20 (has children 2 and 6): Find smaller child: H[2] = 2, H[6] = 6 j = 2 (left child, smaller)

Code
Check: H[20] ≤ H[2]?
    20 ≤ 2? NO - violation exists!

SWAP H[20] ↔ H[2]:
       20 → 2
       2 → 20

After swap:
       2 ← correctly placed at root ✓
      / \
     20  6
    / \ / \
   5  3 10 9

Check key claim: 2 ≤ other child (6)?
    2 ≤ 6? YES ✓

Call: HeapifyDown(H, 20)
    But now 20 is at position 2 (left child of 2)
    20 has children 5 and 3
    
    Find smaller child:
        H[5] = 5, H[3] = 3
        j = 3 (right child, smaller)
    
    Check: H[20] ≤ H[3]?
        20 ≤ 3? NO - violation exists!
    
    SWAP H[20] ↔ H[3]:
        20 ↔ 3
    
    After swap:
           2
          / \
         3   6
        / \ / \
       5  20 10 9
    
    Call: HeapifyDown(H, 20)
        20 is now a leaf (height 0)
        Base case: Return immediately ✓
Code

**Final Result:**
Code
       2 ← heap root, minimum value ✓
      / \
     3   6 ✓
    / \ / \
   5  20 10 9 ✓
   
All heap properties satisfied! 2 ≤ 3, 6 ✓ 3 ≤ 5, 20 ✓ 6 ≤ 10, 9 ✓

Code

### 🔄 Why "Reverse" Induction?

#### **Normal Induction (Forward)**
Prove base case (simple, n=1) ↓ Assume true for n ↓ Prove for n+1 ↓ Conclude true for all n

Code

#### **Reverse Induction (Backward)**
Prove base case (leaves, height 0, simple) ↓ Assume true for nodes of height < h ↓ Prove for nodes of height h (more complex) ↓ Conclude true for all nodes (including root)

Code

**Why it's natural here:**
- Leaves are the simplest case (no children to worry about)
- Parents are more complex (must manage children)
- We naturally work from leaves up to root
- This mirrors how the algorithm actually works: bubbles violations down, eventually reaching leaves

### 📌 Key Insights from the Proof

#### **1. Why Swap With the Smaller Child?**

If we swap with the larger child:

Code
   Parent (too big)
  /              \
Smaller Child Larger Child (after swap, parent moves here)

Relationship after swap: Smaller Child ≤ Parent? UNKNOWN - might be violated!

If we swap with the smaller child:

Code
   Parent (too big)
  /              \
Smaller Child Larger Child (after swap, parent moves here)

Relationship after swap: Smaller Child ≤ Parent? YES! Because Smaller ≤ Larger < Parent (before swap)

Code
Smaller Child ≤ Larger Child?
    YES! By definition of smaller
Code

#### **2. Why AHWL Property Guarantees Correctness?**

The AHWL property says: "Only one node might violate the property"

This means: - We can identify exactly where the problem is - All descendants are already fine - We only need to fix this one node and its descendants - The recursive call on a child will find a similar situation

Code

#### **3. Why Time Complexity is O(log n)?**

Each recursive call moves down one level in the tree

Height of tree = log₂(n)

Maximum recursive calls = log₂(n)

Therefore: O(log n) time

Code

### 📊 Comparison: Induction Directions

| Property | Heapify-Up | Heapify-Down |
|---|---|---|
| **Direction** | Forward induction | Reverse induction |
| **Base case** | Root (no parent) | Leaves (no children) |
| **Simple case** | Fewer levels to fix | No recursive call needed |
| **Complex case** | Deep nodes | Nodes near root |
| **Recursion** | Upward to root | Downward to leaves |
| **Why chosen** | Natural to go UP when too small | Natural to go DOWN when too large |

---

## Adding Items to a Heap

### ➕ The Add Operation

Adding an item to a heap has **two parts:**

1. **Add the item at the end** - This maintains the tree structure (complete binary tree)
2. **Fix the heap property** - Using HeapifyUp

### 🎬 Complete Example

**Starting heap (size = 3):**
Code
   1
  / \
 2   3
Code

**Add 1.5:**

**Step 1: Add at end**
Code
   1
  / \
 2   3
/
1.5

Code
✓ Tree structure is maintained (complete binary tree)
✗ Heap property broken (1.5 < 2, but 2 is parent)

**Step 2: HeapifyUp(H, 4)**
Is 1.5 < parent (2)? YES Swap them:

Code
   1
  / \
1.5 3 / 2

Code

✓ Now 1.5 ≥ parent (1)? YES - check fails, so we're done!
✓ Heap property restored

**Final result:**
Code
   1
  / \
1.5 3 / 2

Code

### 💻 Algorithm

Add(H, value, m): // m is the current number of items

Code
IF m ≤ N-1:  // If there's space
    m = m + 1
    H[m] = value         // Add at the end
    HeapifyUp(H, m)      // Fix the heap property
Code

### ⏱️ Time Complexity

**O(log n)**
- Adding at end: O(1)
- HeapifyUp: O(log n)
- Total: O(log n)

### Key Points

- ✅ Always add at the end (maintains complete binary tree structure)
- ✅ Always heapify up (fixes the property)
- ✅ The value "bubbles up" as far as it needs to go

---

## Removing Items from a Heap

### ➖ The Remove Operation

Removing an item (especially from the middle) requires:

1. **Swap it with the last item** - Maintains tree structure
2. **Remove the last item** - Now we have n-1 items
3. **Fix the heap property** - Using either HeapifyUp or HeapifyDown

### 🎬 Complete Example

**Starting heap (remove position 2, value 2):**
Code
   1
  / \
 2   3
/ \
4 5

Code

**Step 1: Swap position 2 with last position (5)**
Code
   1
  / \
 5   3
/ \
4 2

Code

**Step 2: Remove the last item (now just 2 is gone)**
Code
   1
  / \
 5   3
/
4

Code

**Step 3: Fix the heap property**
- Is 5 > children (4 and 3)? **YES** - Need to fix!
- Call HeapifyDown(H, 2) to bubble 5 down
- Swap 5 with smaller child (3):

Code
   1
  / \
 3   5
/
4

Code

✓ Heap property restored!

### 💻 Algorithm

Remove(H, i): // Swap the item at position i with the last item SWAP H[i] and H[m]

Code
// Remove the last item
H[m] = NULL
m = m - 1

// Now fix the heap property
IF H[i] < H[parent(i)]:
    // Item is smaller than parent - bubble up
    HeapifyUp(H, i)
ELSE:
    // Item is larger than at least one child - bubble down
    HeapifyDown(H, i)
Code

### ⏱️ Time Complexity

**O(log n)**
- Swap: O(1)
- HeapifyUp or HeapifyDown: O(log n)
- Total: O(log n)

### ⚠️ Important Edge Case

When might we bubble UP instead of DOWN after a swap?

Example: 1 /
5 3 /
10 2

Remove the 5, swap with 2: 1 /
2 3 /
10 5

Now 2 < parent(1)? YES! Need to HeapifyUp, not HeapifyDown

Code

---

## Sorting with Heaps: Heap Sort

### 🎯 The Idea

**Heap Sort** is a sorting algorithm that:
1. Builds a heap from unsorted data
2. Repeatedly extracts the minimum element
3. The elements come out in sorted order!

### 🎬 Step-by-Step Example

**Unsorted array:**
[5, 2, 8, 1, 9]

Code

**Step 1: Build a heap (O(n log n))**
Add 5: 5 Add 2: 2 /
5

Add 8: 2 /
5 8

Add 1: 1 /
2 8 / 5

Add 9: 1 /
2 8 /
5 9

Code

**Step 2: Extract min repeatedly (O(n log n))**
Extract 1: → Sorted array: [1] Remove and HeapifyDown: 2 /
5 8

Extract 2: → Sorted array: [1, 2] Remove and HeapifyDown: 5 /
9 No, need to reorganize

Extract 5: → Sorted array: [1, 2, 5] Extract 8: → Sorted array: [1, 2, 5, 8] Extract 9: → Sorted array: [1, 2, 5, 8, 9]

Code

### 💻 Algorithm

HeapSort(V): // V is array with m items

Code
// Phase 1: Build heap - O(m log m)
Create empty heap H with capacity m
FOR i = 0 to m-1:
    Add(H, V[i])

// Phase 2: Extract in sorted order - O(m log m)
FOR i = 0 to m-1:
    V[i] = H[1]              // Get the minimum
    Remove(H, 1)             // Remove it from heap

RETURN V (now sorted!)
Code

### ⏱️ Time Complexity

| Phase | Operations | Time per Op | Total |
|-------|---|---|---|
| Build heap | m Adds | O(log m) | **O(m log m)** |
| Extract mins | m Removes | O(log m) | **O(m log m)** |
| **Total** | | | **O(m log m)** |

### 📊 Comparison With Other Sorts

| Algorithm | Best | Average | Worst | Space |
|---|---|---|---|---|
| **Heap Sort** | O(n log n) | O(n log n) | O(n log n) | O(1) |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(1) |
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) |

**Heap Sort is consistent and space-efficient!**

---

## Real-World Application: Interval Partitioning

### 📅 The Problem

You have a bunch of classes with **start time** and **finish time**:

Math: 9:00 - 10:00 English: 9:30 - 10:30 Physics: 10:00 - 11:00 History: 9:45 - 10:45

Code

**Problem:** Rooms can only hold one class. How many rooms do we need?

**Answer:** At least 3 rooms! (9:30-9:45: Math, English, History overlap)

### 🤔 The Greedy Algorithm

**Intuition:** Whenever a class finishes, reuse that room for the next class.

**Steps:**

1. **Sort by start time:** O(n log n) using HeapSort
Math: 9:00 - 10:00 English: 9:30 - 10:30 History: 9:45 - 10:45 Physics: 10:00 - 11:00

Code

2. **Process each class:**
- Keep a **heap of finish times** (which rooms become available when?)
- For each new class:
  - Check if any room is free (heap[0] ≤ start time of new class)
  - If yes: Reuse that room, update its finish time
  - If no: Need a new room

3. **Use a heap to track available rooms:**
Process Math (9:00-10:00):

No rooms available yet
Assign to Room 1
Heap: [10:00 from Room 1]
Process English (9:30-10:30):

Room 1 is free at 10:00, but new class starts at 9:30
Can't use Room 1
Assign to Room 2
Heap: [10:00 from Room 1, 10:30 from Room 2]
Process History (9:45-10:45):

Both rooms busy
Assign to Room 3
Heap: [10:00, 10:30, 10:45]
Process Physics (10:00-11:00):

Room 1 is free at 10:00!
Class starts at 10:00 - perfect fit
Reuse Room 1, update to finish at 11:00
Heap: [10:30, 10:45, 11:00]
Code

### 💻 Algorithm (Pseudocode)

IntPartition(intervals): // intervals = list of (start_time, finish_time)

Code
// Step 1: Sort by start time - O(n log n)
SORT intervals by start time

rooms_needed = 0

// Step 2: Process each interval - O(n log n)
available_rooms_heap = empty heap

FOR each interval (a, f) in sorted order:
    // Check if we can reuse a room
    IF heap is non-empty AND heap[1] ≤ a:
        old_finish_time = heap[1]
        Remove(heap, 1)
        Add(heap, f)  // Update room to finish at f
    ELSE:
        rooms_needed += 1
        Add(heap, f)

RETURN rooms_needed
Code

### ⏱️ Time Complexity

| Step | Time |
|---|---|
| Sort by start time | O(n log n) |
| Process n intervals (each with heap ops) | O(n log n) |
| **Total** | **O(n log n)** |

### 🎯 Why This Works

The greedy approach is **optimal** because:
1. We're respecting ordering (sorted by start time)
2. We reuse rooms as soon as possible (minimal rooms)
3. The heap efficiently tracks which room becomes free soonest

---

## Summary & Complexity Comparison

### 📊 Heap Operations at a Glance

| Operation | What It Does | Time | When To Use |
|---|---|---|---|
| **HeapifyUp(H, i)** | Bubble element up when it's smaller than parent | O(log n) | After adding a new element |
| **HeapifyDown(H, i)** | Bubble element down when it's larger than children | O(log n) | After removing the root |
| **Add(H, v)** | Insert new value | O(log n) | Inserting into heap |
| **Remove(H, i)** | Remove element at position i | O(log n) | Removing from heap |
| **Find Min** | Get the smallest value | O(1) | Always at position 1 |

### 🏆 Heap vs Other Data Structures

| Operation | Array | Sorted Array | **Heap** ✨ |
|---|---|---|---|
| Find minimum | O(n) | O(1) | **O(1)** ⚡ |
| Add | O(1) | O(n) | **O(log n)** ⚡ |
| Remove | O(n) | O(n) | **O(log n)** ⚡ |
| **Overall** | ❌ Slow | ❌ Slow | ✅ **Balanced!** |

### 🎓 Key Takeaways

1. **Heaps are balanced:**
   - Arrays are fast at finding min but slow at changing
   - Sorted arrays are fast at finding min but slow at changing
   - Heaps are reasonably fast at everything

2. **The heap property is simple:**
   - Every parent < its children
   - This guarantees min is at root
   - But doesn't require full sorting (saves time!)

3. **Two core operations:**
   - HeapifyUp: Move up when too small
   - HeapifyDown: Move down when too large
   - Both are O(log n) because tree height is log n

4. **Both operations have rigorous proofs:**
   - HeapifyUp uses **forward induction** (from root down)
   - HeapifyDown uses **reverse induction** (from leaves up)
   - Both prove correctness and O(log n) complexity

5. **Practical uses:**
   - **Priority queues:** Get highest-priority item instantly
   - **Heap sort:** Sort in O(n log n) space-efficient time
   - **Scheduling:** Interval partitioning, resource allocation

### 🚀 What's Next?

Now that you understand heaps:
- Try implementing Add and Remove operations
- Trace through HeapifyUp and HeapifyDown step-by-step
- Practice Heap Sort on small arrays (5-10 elements)
- Think about other problems that need priority access to data

---

## 💡 Practice Exercises

### Easy
1. Given an array `[3, 1, 4, 1, 5, 9]`, trace through building a min-heap
2. What is the height of a heap with 1000 elements? (Hint: height = log₂(n))
3. In a heap stored as an array, what is the parent of element at index 5?

### Medium
4. Trace through removing the root element from a 5-element heap
5. Explain why we always swap with the **smaller** child in HeapifyDown
6. Draw the tree representation of the heap `[1, 2, 3, 5, 9, 7, 8]`

### Hard
7. Prove that HeapifyUp takes O(log n) time (we did this with induction!)
8. Why is Heap Sort O(n log n) instead of O(n)?
9. Design an algorithm that removes the maximum element from a min-heap (not the root)

---

**End of Lecture 9 - Ready for code implementation!** 🎓
