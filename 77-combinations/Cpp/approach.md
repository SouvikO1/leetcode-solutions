![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.20%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.95%25)-green?style=for-the-badge)

---

## Problem Explained

You are given two numbers: `n` and `k`.

Your task is to find all possible groups of `k` numbers chosen from the list of numbers `1` through `n`.

Key rules:
* Order does not matter. `[1, 2]` is the exact same combination as `[2, 1]`. You should only return unique combinations.
* Each number can only be used once per combination.

For example, if `n = 4` and `k = 2`:
* Numbers to choose from: `[1, 2, 3, 4]`
* Group size: `2`
* Answer: `[[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]`

---

## Intuition

To avoid making duplicate combinations like `[2, 1]` when we already have `[1, 2]`, we enforce a simple rule: **always pick numbers in strictly increasing order**.

If the first number we pick is `1`, the second number must be greater than `1` (either `2`, `3`, or `4`).
If the first number we pick is `2`, the second number must be greater than `2` (either `3` or `4`).

We use recursive **backtracking** to build combinations one slot at a time:
1. Pick a valid number for the current position.
2. Recursively fill the remaining positions with numbers strictly larger than the one we just picked.
3. Once a combination is full (when remaining needed numbers `k == 0`), save it.

Instead of adding (`push_back`) and removing (`pop_back`) elements continuously, this solution pre-allocates a vector of size `k` and overwrites positions directly.

---

## Approach

Here is step-by-step how the code works:

* `vector<vector<int>> result;`: Creates the main list that will hold all valid combinations.
* `vector<int> combination(k);`: Pre-allocates a fixed-size vector of size `k` to store the current combination.
* `generateCombinations(1, n, k, combination, result);`: Calls the recursive helper function. Starts choosing numbers from `1`, with `k` numbers remaining to be picked.
* `if (k == 0)`: Base case check. If `k` reaches `0`, it means we have picked all required `k` numbers.
* `result.push_back(combination);`: Adds a copy of the filled `combination` vector to `result`.
* `return;`: Stops this recursive branch and goes back to explore other choices.
* `for (int i = start; i <= n; ++i)`: Loops through every valid number from `start` up to `n`.
* `combination[combination.size() - k] = i;`: Places the number `i` into the correct index of `combination`. As `k` decreases, `combination.size() - k` moves from index `0` up to `k - 1`.
* `generateCombinations(i + 1, n, k - 1, combination, result);`: Makes a recursive call to pick the next number. Passes `i + 1` so the next number is strictly larger, and `k - 1` because one less number is needed.

---

## Dry Run

### Case 1: Standard case (`n = 4, k = 2`)

| start | k | i | combination | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 2 | 1 | `[1, _]` | Place `1` at index 0. Recurse with `start=2, k=1`. |
| 2 | 1 | 2 | `[1, 2]` | Place `2` at index 1. Recurse with `start=3, k=0`. |
| 3 | 0 | - | `[1, 2]` | Base case (`k == 0`). Add `[1, 2]` to `result`. Return. |
| 2 | 1 | 3 | `[1, 3]` | Place `3` at index 1. Recurse with `start=4, k=0`. |
| 4 | 0 | - | `[1, 3]` | Base case (`k == 0`). Add `[1, 3]` to `result`. Return. |
| 2 | 1 | 4 | `[1, 4]` | Place `4` at index 1. Recurse with `start=5, k=0`. |
| 5 | 0 | - | `[1, 4]` | Base case (`k == 0`). Add `[1, 4]` to `result`. Return. |
| 1 | 2 | 2 | `[2, _]` | Place `2` at index 0. Recurse with `start=3, k=1`. |
| 3 | 1 | 3 | `[2, 3]` | Place `3` at index 1. Recurse with `start=4, k=0`. |
| 4 | 0 | - | `[2, 3]` | Base case (`k == 0`). Add `[2, 3]` to `result`. Return. |
| 3 | 1 | 4 | `[2, 4]` | Place `4` at index 1. Recurse with `start=5, k=0`. |
| 5 | 0 | - | `[2, 4]` | Base case (`k == 0`). Add `[2, 4]` to `result`. Return. |
| 1 | 2 | 3 | `[3, _]` | Place `3` at index 0. Recurse with `start=4, k=1`. |
| 4 | 1 | 4 | `[3, 4]` | Place `4` at index 1. Recurse with `start=5, k=0`. |
| 5 | 0 | - | `[3, 4]` | Base case (`k == 0`). Add `[3, 4]` to `result`. Return. |
| 1 | 2 | 4 | `[4, _]` | Place `4` at index 0. Recurse with `start=5, k=1`. Loop `i` stops because `5 > 4`. |

Final `result`: `[[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]`

---

### Case 2: Minimal edge case (`n = 1, k = 1`)

| start | k | i | combination | Action |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 1 | 1 | `[1]` | Place `1` at index 0. Recurse with `start=2, k=0`. |
| 2 | 0 | - | `[1]` | Base case (`k == 0`). Add `[1]` to `result`. Return. |

Final `result`: `[[1]]`

---

## Time & Space Complexity

**Time Complexity:** O(k * C(n, k))
Where `C(n, k)` is the total number of combinations, calculated as `n! / (k! * (n - k)!)`. The code finds every valid combination, and copying each completed combination of size `k` into `result` takes `O(k)` time.

**Space Complexity:** O(k) auxiliary space
The space used by the call stack is bounded by the depth of recursion, which is at most `k`. The temporary vector `combination` also uses `O(k)` memory. (The output matrix `result` takes `O(k * C(n, k))` space to store the final answer).

---

### Can this code be improved?

**Yes, we can optimize the loop boundary (Pruning).**

Currently, the loop runs up to `i <= n`. However, if there are not enough remaining numbers left in the range `[i, n]` to fill the remaining `k` slots, continuing the loop is useless.

For example, if `n = 4` and `k = 2`, and we try `i = 4` at the top level:
* We need 2 numbers.
* Starting at `4`, there is only 1 number available (`4`), which is not enough.
* The current code still branches into `i = 4` only to fail later.

To fix this, `i` only needs to go up to `n - k + 1`.

#### Improved Code Snippet:

```cpp
// Change the loop condition from (i <= n) to (i <= n - k + 1)
for (int i = start; i <= n - k + 1; ++i) {
    combination[combination.size() - k] = i;
    generateCombinations(i + 1, n, k - 1, combination, result);
}
```

#### Why this works:
* `n - k + 1` represents the absolute maximum value `i` can take while still leaving enough numbers to pick `k` items in total.
* If `n = 4` and `k = 2`, `n - k + 1 = 4 - 2 + 1 = 3`. The loop will stop after `i = 3` and completely skip `i = 4`.

#### Resulting Complexity:
* **Time Complexity:** Still `O(k * C(n, k))` asymptotically, because we must output all valid combinations. However, it avoids exploring dead-end branches, making execution faster in practice.
* **Theoretical Best:** This improved version reaches the theoretical best possible time complexity because producing `C(n, k)` combinations of length `k` requires at least `O(k * C(n, k))` operations.

---

## Edge Cases Handled

* **`n = k`:** (e.g., `n = 4, k = 4`). Only one combination exists containing all numbers `[1, 2, 3, 4]`.
* **`k = 1`:** Selects individual elements `[[1], [2], ..., [n]]`.
* **Smallest constraints (`n = 1, k = 1`):** Recursion terminates immediately on the base case without buffer overflow or out-of-bound errors.
* **Duplicate Prevention:** By always setting `start = i + 1`, numbers are processed in strict ascending order, automatically preventing duplicate outputs.
