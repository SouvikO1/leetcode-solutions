![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.21%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.82%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbered boxes from 1 up to some number **n**. The problem asks you to find every possible group of size **k** that you can make using those numbers. 

For example, if **n** is 4 and **k** is 2, you want to pick 2 numbers out of the group [1, 2, 3, 4]. 
Your choices could be [1, 2], [1, 3], [1, 4], [2, 3], [2, 4], and [3, 4]. 

There are two strict rules:
1. **Order does not matter**: Picking 1 then 2 is the exact same combination as picking 2 then 1. The code only generates them in ascending order so you never accidentally count duplicates.
2. **No repeats**: You cannot pick the same number twice in the same combination. Every number in a chosen group is unique.

---

## Intuition

The core idea here is **backtracking** (also known as a systematic search). Instead of guessing randomly, we build our combinations one number at a time, like climbing down a tree of choices. 

If we need a combination of size **k**, we loop through our available numbers starting from a given point. We pick one number, place it in our current combination, and then recursively call the function to pick the next number, starting our search *after* the number we just picked. This guarantees we never look backward, which naturally prevents duplicate pairs like [2, 1] when we already made [1, 2]. 

When our combination reaches the required size (**k == 0**), we save it and step back (backtrack) to try the next branch. A neat trick used in this code is pre-allocating the vector size and filling slots from right to left using `combination.size() - k`, which avoids slow container resizing operations and keeps memory usage lean.

---

## Approach

Here is how the code executes step by step, using the real variable names:

- `vector<vector<int>> result;`: Creates an outer container to hold all of our completed valid combinations.
- `vector<int> combination(k);`: Creates a single temporary container of size **k** that we will overwrite and reuse as we build our combinations.
- `generateCombinations(1, n, k, combination, result);`: Kicks off the recursive search starting at number 1.
- `if (k == 0)`: Checks if our current combination is fully built (meaning we have successfully placed **k** numbers).
- `result.push_back(combination);`: Copies the completed combination into our final results list when the base case is hit.
- `return;`: Stops the current branch of the function once a combination is saved.
- `for (int i = start; i <= n; ++i)`: Loops through all available numbers starting from the `start` position up to **n**.
- `combination[combination.size() - k] = i;`: Places the current loop number `i` into the correct empty slot inside our temporary combination vector.
- `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively calls the function to pick the next number. Notice that we pass `i + 1` so we never reuse the current number, and we pass `k - 1` to track that we need one fewer number.

---

## Dry Run

### Case 1: Typical case (n = 4, k = 2)

| `start` | `k` | `i` | `combination` | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 2 | 1 | [1, 0] | Loop starts at 1. Places 1 in slot. Recurses with start=2, k=1. |
| 2 | 1 | 2 | [1, 2] | Inner loop starts at 2. Places 2 in slot. Recurses with start=3, k=0. |
| 3 | 0 | - | [1, 2] | Base case reached (k == 0). Pushes [1, 2] to result. Returns. |
| 2 | 1 | 3 | [1, 3] | Back to outer branch. Next loop index i = 3. Places 3. Recurses. |
| 4 | 0 | - | [1, 3] | Base case reached. Pushes [1, 3] to result. Returns. |
| 2 | 1 | 4 | [1, 4] | Next loop index i = 4. Places 4. Recurses. |
| 5 | 0 | - | [1, 4] | Base case reached. Pushes [1, 4] to result. Returns. |

*(The process continues similarly for outer loops starting at 2 and 3, eventually finding [2, 3], [2, 4], and [3, 4]).*

### Case 2: Edge case with equal values (n = 1, k = 1)

| `start` | `k` | `i` | `combination` | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 1 | [1] | Loop runs once where i = 1. Places 1 in slot. Recurses with start=2, k=0. |
| 2 | 0 | - | [1] | Base case reached (k == 0). Pushes [1] to result. Returns. |

---

## Time & Space Complexity

- **Time:** O(N choose K * K) — The algorithm generates every valid combination, and there are total combinations equal to N choose K. For each generated combination, it takes K operations to copy or place elements into the vector.
- **Space:** O(K) — The auxiliary stack depth of the recursion reaches a maximum depth of K, and our temporary `combination` vector holds at most K elements (excluding the output storage required to return the final answers).

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

This code is already optimal. Because the problem explicitly requires us to generate and return *every* valid combination, any valid algorithm must output all combinations. The total number of valid combinations is N choose K, meaning any solution must at least touch every output element, making O(N choose K * K) the theoretical best possible time complexity. No further structural improvements can bypass generating the actual output items.

---

## Edge Cases Handled

- **Minimum constraints (n = 1, k = 1):** Handled cleanly by executing a single loop iteration, hitting the base case immediately, and returning [[1]].
- **K equals N (e.g., n = 3, k = 3):** The code loops through all items, fills the combination completely in one clean sweep, and returns the single full-range combination [[1, 2, 3]].
- **Values where K is larger than N:** Though ruled out by the problem constraints (1 <= k <= n), the `for` loop condition (`i <= n`) naturally prevents any out-of-bounds execution if invalid ranges were somehow passed.
