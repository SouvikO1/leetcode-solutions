![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.21%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.82%25)-green?style=for-the-badge)

---

## Problem Explained

The problem asks us to build all possible combinations of **k** numbers chosen from the range of numbers **1 through n**. 

A **combination** is a set of unique numbers where order does not matter. For example, `[1, 2]` and `[2, 1]` are considered the same combination, so we only include one of them. Every number in a combination must be distinct.

For example:
- If **n = 4** and **k = 2**, we choose 2 numbers from `[1, 2, 3, 4]`.
- The output is `[[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]`.

## Intuition

To build combinations without generating duplicates, we can construct them step-by-step. 

1. **Always moving forward:** To avoid picking `[2, 1]` after we already picked `[1, 2]`, we enforce a simple rule: every newly added number must be strictly larger than the previous number.
2. **Backtracking:** We pick a number, move forward to pick the next, and when our combination reaches size **k**, we save it. Then we step back, undo the pick, and try the next available larger number.
3. **Memory optimization:** Instead of using `.push_back()` and `.pop_back()` to add and remove numbers dynamically, this implementation allocates a fixed-size array of length **k** once. It calculates the exact index to overwrite based on how many elements are still needed.

## Approach

Here is how the code works step-by-step:

*   `vector<int> combination(k);`: Pre-allocates a single temporary list of size `k`. Overwriting elements in this list avoids frequent allocations during recursion.
*   `generateCombinations(1, n, k, combination, result);`: Starts the recursive helper function. We begin picking numbers starting at `1`, with `k` remaining slots to fill.
*   `if (k == 0)`: Checks the base case. If `k` reaches `0`, it means all `k` slots in our combination are filled with valid numbers.
*   `result.push_back(combination);`: Adds a copy of the completed `combination` into our final list of answers, then returns to try other branches.
*   `for (int i = start; i <= n; ++i)`: Loops through every valid number `i` from `start` up to `n` to place in the current position.
*   `combination[combination.size() - k] = i;`: Calculates the current index in the array (`total size - remaining items needed`) and writes `i` directly into that slot.
*   `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively fills the next slot. It sets the new `start` to `i + 1` so that future choices are strictly larger, and decreases `k` by 1 because one slot was filled.

## Dry Run

### Case 1: Typical case (n = 4, k = 2)

`combination` is pre-allocated with length 2.

| Recursion Depth | `start` | `k` | Loop `i` | `combination` State | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Level 1 | 1 | 2 | 1 | `[1, _]` | Writes 1 at index 0 (`2 - 2`). Recurses with `start = 2`, `k = 1`. |
| Level 2 | 2 | 1 | 2 | `[1, 2]` | Writes 2 at index 1 (`2 - 1`). Recurses with `start = 3`, `k = 0`. |
| Level 3 | 3 | 0 | - | `[1, 2]` | Base case (`k == 0`): Save `[1, 2]` to `result`. Return to Level 2. |
| Level 2 | 2 | 1 | 3 | `[1, 3]` | Writes 3 at index 1 (`2 - 1`). Recurses with `start = 4`, `k = 0`. |
| Level 3 | 4 | 0 | - | `[1, 3]` | Base case (`k == 0`): Save `[1, 3]` to `result`. Return to Level 2. |
| Level 2 | 2 | 1 | 4 | `[1, 4]` | Writes 4 at index 1 (`2 - 1`). Recurses with `start = 5`, `k = 0`. |
| Level 3 | 5 | 0 | - | `[1, 4]` | Base case (`k == 0`): Save `[1, 4]` to `result`. Return to Level 2. |
| Level 1 | 1 | 2 | 2 | `[2, _]` | Writes 2 at index 0 (`2 - 2`). Recurses with `start = 3`, `k = 1`. |
| Level 2 | 3 | 1 | 3 | `[2, 3]` | Writes 3 at index 1 (`2 - 1`). Base case hits next depth: Save `[2, 3]`. |
| Level 2 | 3 | 1 | 4 | `[2, 4]` | Writes 4 at index 1 (`2 - 1`). Base case hits next depth: Save `[2, 4]`. |
| Level 1 | 1 | 2 | 3 | `[3, _]` | Writes 3 at index 0 (`2 - 2`). Recurses with `start = 4`, `k = 1`. |
| Level 2 | 4 | 1 | 4 | `[3, 4]` | Writes 4 at index 1 (`2 - 1`). Base case hits next depth: Save `[3, 4]`. |
| Level 1 | 1 | 2 | 4 | `[4, _]` | Loop at next depth `start = 5` up to `n = 4` doesn't run. Ends. |

Final `result`: `[[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]`

---

### Case 2: Minimal input (n = 1, k = 1)

`combination` is pre-allocated with length 1.

| Recursion Depth | `start` | `k` | Loop `i` | `combination` State | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Level 1 | 1 | 1 | 1 | `[1]` | Writes 1 at index 0 (`1 - 1`). Recurses with `start = 2`, `k = 0`. |
| Level 2 | 2 | 0 | - | `[1]` | Base case (`k == 0`): Save `[1]` to `result`. Return. |

Final `result`: `[[1]]`

## Time & Space Complexity

- **Time Complexity:** **O(k * C(n, k))**, where **C(n, k)** is the total number of combinations, calculated as `n! / (k! * (n - k)!)`. There are **C(n, k)** valid combinations built, and copying each combination of size **k** into the final result list takes **O(k)** time.
- **Space Complexity:** **O(k)** extra space for the recursion call stack and the single shared working list `combination`. (This excludes the space needed to store the final output `result`).

### Can this be improved?

The overall time complexity bound cannot be improved because any correct algorithm must take at least **O(k * C(n, k))** time to write out every element of every combination.

However, the **practical runtime** can be improved by **loop pruning**. 

Currently, the loop runs up to `i <= n`. But if we need `k` elements and there aren't enough numbers left between `i` and `n` to form a full combination, exploring that branch is useless. 

For example, if `n = 4` and `k = 2`, starting a combination with `4` is useless because no number after `4` exists to complete the length of 2. 

The number of elements available from `i` to `n` is `n - i + 1`. We need at least `k` elements, so:
`n - i + 1 >= k`  -->  `i <= n - k + 1`

Changing the loop condition to `i <= n - k + 1` avoids useless recursion branches.

```cpp
// Change the loop condition inside generateCombinations:
for (int i = start; i <= n - k + 1; ++i) {
    combination[combination.size() - k] = i;
    generateCombinations(i + 1, n, k - 1, combination, result);
}
```

- **Improved Complexity:** The Big-O bound remains **O(k * C(n, k))**, but it reaches the theoretical minimum number of function calls possible.

## Edge Cases Handled

*   **k = 1:** The loop runs from `1` to `n` and immediately saves single-element lists without deeper recursion.
*   **k = n:** Only one combination exists containing all numbers from `1` to `n`. The strict `i + 1` progression ensures exactly one path reaches the bottom depth.
*   **Smallest boundary constraints (n = 1, k = 1):** Handled gracefully without out-of-bounds access due to exact array size allocation.
*   **Duplicate prevention:** By passing `i + 1` as the new `start` value, the algorithm never reuses the same number or generates reversed permutations like `[2, 1]` when `[1, 2]` already exists.
