![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.21%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.97%25)-green?style=for-the-badge)

---

## Problem Explained

The problem asks us to find all possible groups of **k** distinct numbers selected from the numbers **1** through **n**.

A group of numbers here is a **combination**, which means order does not matter. For example, `[1, 2]` and `[2, 1]` are considered the exact same group. To keep things clean and avoid duplicate groups, we list numbers inside each group in increasing order (like `[1, 2]`).

For example, if `n = 4` and `k = 2`, the available numbers are `1, 2, 3, 4`. We need to pick groups of `2` numbers. The output will be:
`[[1, 2], [1, 3], [1, 4], [2, 3], [2, 4], [3, 4]]`.

---

## Intuition

This problem is solved using **backtracking**, which is an approach where we build a solution step-by-step and turn back whenever a path leads to a complete solution or an invalid state.

To avoid generating identical combinations in different orders (like `[2, 1]` after already making `[1, 2]`), we enforce a simple rule: **always pick numbers in strictly increasing order**.

If we just picked number `1`, the next choice must be greater than `1` (so `2`, `3`, or `4`). If we picked `2`, the next choice must be greater than `2` (so `3` or `4`).

Instead of constantly adding and deleting items from a dynamic array, this C++ solution uses a clever memory trick:
1. Allocate a single output array `combination` of size `k` upfront.
2. Use the parameter `k` to represent how many empty slots are left to fill.
3. Calculate the current slot index directly using `combination.size() - k`.
4. Overwrite values at that slot as we try different choices.

---

## Approach

Here is how the code works step-by-step:

* `vector<vector<int>> result;`: Creates the main list that will store all completed combinations.
* `vector<int> combination(k);`: Pre-allocates a temporary array of fixed size `k` to build one combination at a time.
* `generateCombinations(1, n, k, combination, result);`: Starts the recursive helper function. It begins looking for numbers starting at `1`, with `k` unfilled slots remaining.
* `if (k == 0)`: Checks if there are no more slots left to fill (base case).
* `result.push_back(combination);`: Copies the fully filled `combination` array into our final answer list `result` when `k` reaches `0`, then returns.
* `for (int i = start; i <= n; ++i)`: Loops through all valid numbers from `start` up to `n`.
* `combination[combination.size() - k] = i;`: Places the current number `i` directly into the current slot. The slot index is calculated as `total length - remaining slots`.
* `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively calls the function to fill the next slot. It passes `i + 1` so the next choice is strictly larger than `i`, and decreases the remaining slot count `k` by `1`.

---

## Dry Run

### Case 1: Typical case (`n = 4, k = 2`)

| `start` | `k` (remaining) | `i` | `combination` | Action |
|---|---|---|---|---|
| 1 | 2 | - | `[0, 0]` | Call function. Remaining slots = 2. Index = `2 - 2 = 0`. |
| 1 | 2 | 1 | `[1, 0]` | Set slot 0 to 1. Recurse with `start = 2`, `k = 1`. |
| 2 | 1 | 2 | `[1, 2]` | Set slot 1 (`2 - 1 = 1`) to 2. Recurse with `start = 3`, `k = 0`. |
| 3 | 0 | - | `[1, 2]` | `k == 0`. Add `[1, 2]` to `result`. Return. |
| 2 | 1 | 3 | `[1, 3]` | Loop continues: set slot 1 to 3. Recurse with `start = 4`, `k = 0`. |
| 4 | 0 | - | `[1, 3]` | `k == 0`. Add `[1, 3]` to `result`. Return. |
| 2 | 1 | 4 | `[1, 4]` | Loop continues: set slot 1 to 4. Recurse with `start = 5`, `k = 0`. |
| 5 | 0 | - | `[1, 4]` | `k == 0`. Add `[1, 4]` to `result`. Return. |
| 1 | 2 | 2 | `[2, 0]` | Back to top loop: set slot 0 to 2. Recurse with `start = 3`, `k = 1`. |
| 3 | 1 | 3 | `[2, 3]` | Set slot 1 to 3. Recurse with `start = 4`, `k = 0`. |
| 4 | 0 | - | `[2, 3]` | `k == 0`. Add `[2, 3]` to `result`. Return. |
| 3 | 1 | 4 | `[2, 4]` | Set slot 1 to 4. Recurse with `start = 5`, `k = 0`. |
| 5 | 0 | - | `[2, 4]` | `k == 0`. Add `[2, 4]` to `result`. Return. |
| 1 | 2 | 3 | `[3, 0]` | Back to top loop: set slot 0 to 3. Recurse with `start = 4`, `k = 1`. |
| 4 | 1 | 4 | `[3, 4]` | Set slot 1 to 4. Recurse with `start = 5`, `k = 0`. |
| 5 | 0 | - | `[3, 4]` | `k == 0`. Add `[3, 4]` to `result`. Return. |

---

### Case 2: Minimal case (`n = 1, k = 1`)

| `start` | `k` (remaining) | `i` | `combination` | Action |
|---|---|---|---|---|
| 1 | 1 | - | `[0]` | Call function. Remaining slots = 1. Index = `1 - 1 = 0`. |
| 1 | 1 | 1 | `[1]` | Set slot 0 to 1. Recurse with `start = 2`, `k = 0`. |
| 2 | 0 | - | `[1]` | `k == 0`. Add `[1]` to `result`. Return. |

---

## Time & Space Complexity

* **Time Complexity:** **O(k * C(n, k))**, where `C(n, k)` is the total number of combinations (calculated as `n! / (k! * (n - k)!)`). There are `C(n, k)` combinations in total, and copying each combination array of size `k` into the `result` vector takes `O(k)` time.
* **Space Complexity:** **O(k)** auxiliary space (excluding the `result` array). The space is used by the recursion call stack (at most `k` levels deep) and the `combination` buffer array of size `k`.

### Can it be improved?

The time complexity is already **asymptotically optimal** because any algorithm must spend time writing out every valid combination to the output.

However, we can optimize runtime by **pruning** unnecessary search paths. Currently, the loop runs up to `i <= n`. But if there aren't enough remaining numbers between `i` and `n` to fill the `k` remaining slots, continuing the loop is wasted effort.

To fix this, we can stop the loop early when `i` exceeds `n - k + 1`.

```cpp
// Change the loop condition from (i <= n) to:
for (int i = start; i <= n - k + 1; ++i) {
    combination[combination.size() - k] = i;
    generateCombinations(i + 1, n, k - 1, combination, result);
}
```

* Why `n - k + 1`? If we need `k` more numbers, the largest starting number we can afford to pick is `n - k + 1`. For instance, if `n = 4` and we need `k = 2` numbers, `i` only needs to go up to `4 - 2 + 1 = 3`. Trying `i = 4` leaves only 1 number available (`4`), which is not enough to build a 2-number group.

The theoretical best time complexity remains **O(k * C(n, k))**, and this pruning optimization reaches it with fewer redundant steps.

---

## Edge Cases Handled

* **`k == n` (Select all numbers):** The algorithm picks every number from `1` to `n` in order and returns a single combination of length `n`.
* **`k == 1` (Select single items):** The algorithm works as a simple loop outputting `[[1], [2], ..., [n]]`.
* **Minimal input bounds (`n = 1, k = 1`):** Executes without out-of-bounds access or array size errors.
