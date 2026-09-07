![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.21%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.82%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbered boxes from 1 up to some number **n**. The problem asks you to find every possible group of **k** boxes you can pick from that row. 

For example, if **n** is 4 and **k** is 2, you want to find all unique pairs of numbers you can choose from the numbers 1, 2, 3, and 4. Your choices are pairs like [1, 2], [1, 3], [1, 4], [2, 3], [2, 4], and [3, 4]. 

There are two main rules to keep in mind:
- **Order does not matter:** Picking 1 then 2 is the exact same combination as picking 2 then 1. We only want to list [1, 2] once.
- **No duplicates within a group:** You cannot pick the same number twice in the same combination.

---

## Intuition

The core idea here is **backtracking**, which is a fancy way of saying "let's build our answer step-by-step, and if we hit a dead end or finish a valid group, step back and try the next possibility."

Instead of writing endless nested loops (which we can't do anyway since **k** can be any number), we use a recursive function—a function that calls itself. We start picking numbers one by one. Each time we pick a number, we move forward and try to pick the next one, decreasing how many numbers we still need (**k**) until we have a full group. 

To avoid wasting time on duplicate groups like [1, 2] and [2, 1], our search always moves strictly forward. If we start with 1, we only look at numbers greater than 1 for our next choices. This guarantees every combination we find is unique.

---

## Approach

Here is how the code builds combinations step-by-step using the real variables:

- `vector<vector<int>> result;` — Initializes an empty outer container to store all of our completed combinations.
- `vector<int> combination(k);` — Creates a single temporary array of size **k** that we will fill up as we explore different paths.
- `generateCombinations(1, n, k, combination, result);` — Kicks off the recursive search starting at number 1, looking for **k** total numbers up to **n**.
- `if (k == 0)` — Checks if our temporary combination is fully complete (meaning we successfully picked **k** numbers).
- `result.push_back(combination);` — Saves a completed combination into our final results list, then stops this specific path by returning.
- `for (int i = start; i <= n; ++i)` — Loops through all available numbers starting from our current **start** position up to **n**.
- `combination[combination.size() - k] = i;` — Places the current number **i** into the correct open slot inside our temporary combination array.
- `generateCombinations(i + 1, n, k - 1, combination, result);` — Recursively calls the function to pick the next number, advancing the start point to **i + 1** so we never reuse numbers or repeat reverse orders, and reducing **k** by 1.

---

## Dry Run

### Case 1: Typical case (n = 4, k = 2)

| start | k | i | combination | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 2 | 1 | [1, 0] | Loop picks i = 1, places it at the first slot. Recurses with start = 2, k = 1. |
| 2 | 1 | 2 | [1, 2] | Inner loop picks i = 2, places it at the second slot. Recurses with start = 3, k = 0. |
| 3 | 0 | - | [1, 2] | k is 0. Saves [1, 2] to result, then goes back. |
| 2 | 1 | 3 | [1, 3] | Back in outer loop, i becomes 3. Places it. Recurses. |
| 4 | 0 | - | [1, 3] | k is 0. Saves [1, 3] to result, then goes back. |
| 2 | 1 | 4 | [1, 4] | i becomes 4. Places it. Recurses, saves [1, 4]. |

*(The process continues similarly, shifting the outer loop to start at 2 and 3, eventually finding [2, 3], [2, 4], and [3, 4].)*

### Case 2: Minimal case (n = 1, k = 1)

| start | k | i | combination | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 1 | [1] | Loop picks i = 1, places it at the single slot. Recurses with start = 2, k = 0. |
| 2 | 0 | - | [1] | k is 0. Saves [1] to result, then stops. |

---

## Time & Space Complexity

- **Time:** O(C(n, k) * k) — The algorithm generates every single valid combination. There are C(n, k) total combinations (read as "n choose k"), and for each one, it takes **k** steps to copy it into the final result.
- **Space:** O(k) — Beyond the memory needed to store the final output, the recursion stack and the temporary `combination` array never grow deeper or larger than **k**.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already the most optimal possible time and space complexity. Because the problem explicitly requires you to generate and return every single possible combination, your code *must* output C(n, k) items. Visiting every valid combination once and taking **k** time to store each one hits the absolute mathematical lower bound for this task. You cannot output all answers faster than the number of answers that exist.

---

## Edge Cases Handled

- **k equals n:** Handled correctly. The loop runs through all numbers from 1 to **n**, fills the single combination of size **n**, and returns [[1, 2, ..., n]].
- **k equals 1:** Handled correctly. The function immediately finds single-element combinations like [[1], [2], [3], ...].
- **Small constraints (n = 1, k = 1):** Handled cleanly without crashing or entering infinite loops, returning a single nested vector containing [[1]].
