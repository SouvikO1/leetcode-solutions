![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.21%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.82%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbered boxes from 1 up to some number **n**. Your job is to find every possible group of **k** numbers you can pick from those boxes. 

For example, if **n** = 4 and **k** = 2, you want all pairs of numbers you can choose from 1, 2, 3, and 4. The possible pairs are (1,2), (1,3), (1,4), (2,3), (2,4), and (3,4). 

Two rules matter here:
- The order inside a group does not matter. Picking 1 then 2 is the exact same combination as picking 2 then 1, so you only list it once.
- You cannot pick the exact same number twice in the same combination.

---

## Intuition

To build every combination without missing any or repeating duplicates, we use **backtracking**. This is a style of problem-solving where we build our answer step by step. If a choice leads to a valid path, we keep going deeper. If we hit a dead end or finish a group, we step back and try the next choice.

The secret to keeping our combinations unique and ordered is simple: **always pick numbers in increasing order**. If we start our search at number 1, the next number we pick must be greater than 1. This naturally stops us from generating both (1, 2) and (2, 1), because once we pick 1, we only look at numbers ahead of 1.

Even better, we pre-allocate the exact amount of memory we need for a single combination vector of size **k**, and we fill it in using a clever index trick: `combination[combination.size() - k] = i`. As **k** counts down, we overwrite slots from left to right until the combination is full.

---

## Approach

Here is how the solution works, line by line:

- `vector<vector<int>> result;`: Creates a container to store all of our completed combinations so we can return them at the end.
- `vector<int> combination(k);`: Creates a single temporary storage box sized exactly to **k** elements, which we will reuse over and over as we build different combinations.
- `generateCombinations(1, n, k, combination, result);`: Kicks off our recursive search starting at number 1, using our range **n**, target size **k**, and our storage vectors.
- `if (k == 0) { result.push_back(combination); return; }`: Checks if our target size **k** has reached 0. If it has, our temporary combination is completely filled, so we save a copy of it into our **result** list and stop this branch of the search.
- `for (int i = start; i <= n; ++i)`: Loops through every available number starting from our **start** point up to **n**. This ensures we only pick numbers moving forward, preventing duplicate combinations.
- `combination[combination.size() - k] = i;`: Places our current choice **i** directly into the correct open slot inside our **combination** vector, determined by how many spots are left to fill (**k**).
- `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively calls the function to pick the *next* number. We pass **i + 1** so we never reuse the current number, and we pass **k - 1** because we have one less number left to find.

---

## Dry Run

### Case 1: Typical case (n = 4, k = 2)

| start | n | k | combination | Action |
| --- | --- | --- | --- | --- |
| 1 | 4 | 2 | [0, 0] | Loop starts at **i** = 1. Places 1 at index `2 - 2` = 0. Recurses with **start** = 2, **k** = 1. |
| 2 | 4 | 1 | [1, 0] | Loop starts at **i** = 2. Places 2 at index `2 - 1` = 1. Recurses with **start** = 3, **k** = 0. |
| 3 | 4 | 0 | [1, 2] | **k** is 0. Saves `[1, 2]` to **result**. Returns. |
| 2 | 4 | 1 | [1, 2] | Loop moves to **i** = 3. Places 3 at index 1. Recurses with **start** = 4, **k** = 0. |
| 4 | 4 | 0 | [1, 3] | **k** is 0. Saves `[1, 3]` to **result**. Returns. |
| ... | ... | ... | ... | Continues this pattern until all 6 combinations are found and returned. |

### Case 2: Minimal case (n = 1, k = 1)

| start | n | k | combination | Action |
| --- | --- | --- | --- | --- |
| 1 | 1 | 1 | [0] | Loop starts at **i** = 1. Places 1 at index `1 - 1` = 0. Recurses with **start** = 2, **k** = 0. |
| 2 | 1 | 0 | [1] | **k** is 0. Saves `[1]` to **result**. Returns. |
| 1 | 1 | 1 | [1] | Loop finishes. Function exits and returns **result** containing `[[1]]`. |

---

## Time & Space Complexity

**Time:** O(C(n, k) * k) — where C(n, k) is the number of combinations (n choose k). The algorithm must visit and build every single valid combination, and copying a combination of size **k** into the final result takes **k** steps.
**Space:** O(k) for the recursion stack and the temporary **combination** vector at any given moment (excluding the memory required to store the final output).

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

This is already the most optimal possible complexity. Because the problem explicitly requires us to generate and return *every* possible combination, any valid algorithm *must* output every single one of those elements. Since the output size itself is C(n, k) * k, the time complexity is bound by the sheer size of the answer. You cannot output all answers faster than the time it takes to write them down.

---

## Edge Cases Handled

- **k = 1:** Handled cleanly; the loop runs once per number, producing single-element combinations like `[[1], [2], [3]]`.
- **n equals k:** Handled correctly; the loop runs through all numbers from 1 to **n**, fills the entire combination in one go, and returns the single full-range combination (e.g., `n = 3, k = 3` returns `[[1, 2, 3]]`).
- **Upper bound constraints (n = 20):** The recursion depth never exceeds 20, which easily executes within time limits without overflowing the call stack.
