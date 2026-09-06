![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.20%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.95%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbered boxes from 1 up to some number **n**. Your job is to find every possible group of size **k** that you can make out of those numbers. 

For example, if **n** is 4 and **k** is 2, you want to pick groups of 2 numbers from the numbers 1, 2, 3, and 4. 
The possible groups are:
- 1 and 2
- 1 and 3
- 1 and 4
- 2 and 3
- 2 and 4
- 3 and 4

Order inside the group does not matter. This means the group [1, 2] is the exact same thing as the group [2, 1], so you only list it once. You just need to return all of these unique groups in any order.

## Intuition

The core idea here is **backtracking** (also known as a systematic trial-and-error search). Think of it like building a combination one number at a time. 

You start with an empty spot and try putting a 1 in it. Then you move to the next spot and try a 2, making your first valid group [1, 2]. Then you backtrack—meaning you step back, swap out the 2 for a 3, and make [1, 3]. 

To make this super fast and avoid wasting time, two smart things happen in this solution:
1. **No duplicates:** We always pick numbers in increasing order (like 1 then 2, never 2 then 1). By always moving forward (`i + 1`), we never look backward, which naturally prevents duplicate groups.
2. **Pre-allocated space:** Instead of growing our temporary combination list one element at a time using slow push operations, we create a blank container of size **k** right at the start and overwrite slots directly. When **k** counts down to 0, a full combination is ready and saved.

## Approach

Here is how the code executes, step by step:

- `vector<vector<int>> result;`: Creates an outer list named **result** to hold all of our completed combinations.
- `vector<int> combination(k);`: Creates a single temporary list named **combination** pre-sized to hold exactly **k** numbers.
- `generateCombinations(1, n, k, combination, result);`: Launches the helper function starting our search at number 1, with our target size **k**.
- `if (k == 0) { result.push_back(combination); return; }`: Checks if our combination is completely full (because **k** has counted down to 0). If it is, we copy it into **result** and stop this branch.
- `for (int i = start; i <= n; ++i)`: Loops through every available number from **start** up to **n** to try building combinations.
- `combination[combination.size() - k] = i;`: Places our current choice **i** directly into the correct open slot inside the **combination** list.
- `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively calls the function to fill the next slot, moving our starting point to **i + 1** so we never reuse a number, and reducing **k** by 1.

## Dry Run

### Case 1: Typical case (n = 4, k = 2)

| start | n | k | combination | i | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 4 | 2 | [0, 0] | 1 | Loop starts, places 1 at index 0. Recurses with start = 2, k = 1. |
| 2 | 4 | 1 | [1, 0] | 2 | Places 2 at index 1. Recurses with start = 3, k = 0. |
| 3 | 4 | 0 | [1, 2] | - | k reaches 0. Pushes [1, 2] to result. Returns. |
| 2 | 4 | 1 | [1, 2] | 3 | Loop continues, changes index 1 to 3. Recurses with start = 4, k = 0. |
| 4 | 4 | 0 | [1, 3] | - | k reaches 0. Pushes [1, 3] to result. Returns. |

*(The loop continues similarly, finding [1, 4], [2, 3], [2, 4], and [3, 4]).*

### Case 2: Boundary case (n = 1, k = 1)

| start | n | k | combination | i | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 1 | [0] | 1 | Loop starts, places 1 at index 0. Recurses with start = 2, k = 0. |
| 2 | 1 | 0 | [1] | - | k reaches 0. Pushes [1] to result. Returns. |

## Time & Space Complexity

- **Time:** O(C(n, k) * k) — where C(n, k) is the number of combinations (n choose k). The algorithm must visit and build every single valid combination, and copying each combination of size **k** takes **k** steps.
- **Space:** O(k) — for the recursion stack and the temporary **combination** vector, which both grow up to a maximum depth of **k**. (Note: This excludes the space needed to store the final output **result**, which is required by the problem).

**Is this already the most optimal possible complexity?**
Yes, this is already optimal. Any algorithm that returns all combinations of size **k** out of **n** *must* output every single one of them, meaning it cannot do better than time proportional to the number of output combinations. Furthermore, pre-allocating the vector avoids dynamic resizing overhead, beating over 99 percent of submissions. No further improvement is possible.

## Edge Cases Handled

- **k equals n:** The loop runs through all numbers from 1 to **n**, fills every slot, and correctly returns just one single combination containing all numbers.
- **k equals 1:** The function immediately builds single-element combinations for every number from 1 to **n** and returns them cleanly.
- **Minimum constraints (n = 1, k = 1):** Handled smoothly by outputting [[1]] without crashing or triggering infinite loops.
