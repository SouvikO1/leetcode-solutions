![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.24%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2072.15%25)-green?style=for-the-badge)

---

## Problem Explained

The problem asks you to find all possible groups of `k` numbers chosen from the range `1` to `n`. 

In combinations, order does not matter. This means `[1, 2]` and `[2, 1]` are considered the exact same group, so you should only include one of them.

For example, if `n = 4` and `k = 2`, your available numbers are `1, 2, 3, 4`. You need to pick groups of `2` numbers. The output will be:
`[[1, 2], [1, 3], [1, 4], [2, 3], [2, 4], [3, 4]]`.

## Intuition

To build combinations without creating duplicates, we always pick numbers in strictly increasing order. If we just picked `1`, the next number must be larger than `1` (either `2`, `3`, or `4`). This guarantees we generate `[1, 2]` but never accidentally generate `[2, 1]`.

We use a technique called **backtracking** (trying a choice, exploring where it leads, and then overwriting it to try the next choice). 

Instead of constantly pushing and popping items from a dynamic array, this solution pre-allocates a single array of size `k`. As we step deeper into our choices, we overwrite positions directly using the number of remaining items needed (`k`).

## Approach

Here is how the code works step-by-step:

* `vector<int> combination(k);`: Pre-allocates a vector of fixed size `k`. This saves time by avoiding repeated memory allocations.
* `generateCombinations(1, n, k, combination, result);`: Starts the recursive helper function. We start picking from number `1`, with `k` numbers left to pick.
* `if (k == 0)`: Checks if we have chosen enough numbers. When `k` reaches `0`, the current combination is full.
* `result.push_back(combination);`: Copies the completed combination into our final answer list.
* `for (int i = start; i <= n; ++i)`: Loops through every valid number from `start` up to `n` to try as the next element.
* `combination[combination.size() - k] = i;`: Calculates the current target index in `combination` and assigns the number `i` to it.
* `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively calls the function to pick the next number. We pass `i + 1` as the new starting point to prevent duplicate combinations, and reduce `k` by `1`.

## Dry Run

### Case 1: Standard input (`n = 4, k = 2`)

| `start` | `k` | `i` | `combination` | Action |
| :--- | :--- | :--- | :--- | :--- |
| `1` | `2` | `1` | `[1, _]` | Place `1` at index `0`. Call `generateCombinations(2, 4, 1)` |
| `2` | `1` | `2` | `[1, 2]` | Place `2` at index `1`. Call `generateCombinations(3, 4, 0)` |
| `3` | `0` | - | `[1, 2]` | `k == 0`: Add `[1, 2]` to `result`. Return |
| `2` | `1` | `3` | `[1, 3]` | Place `3` at index `1`. Call `generateCombinations(4, 4, 0)` |
| `4` | `0` | - | `[1, 3]` | `k == 0`: Add `[1, 3]` to `result`. Return |
| `2` | `1` | `4` | `[1, 4]` | Place `4` at index `1`. Call `generateCombinations(5, 4, 0)` |
| `5` | `0` | - | `[1, 4]` | `k == 0`: Add `[1, 4]` to `result`. Return |
| `1` | `2` | `2` | `[2, 4]` | Place `2` at index `0`. Call `generateCombinations(3, 4, 1)` |
| `3` | `1` | `3` | `[2, 3]` | Place `3` at index `1`. Call `generateCombinations(4, 4, 0)` |
| `4` | `0` | - | `[2, 3]` | `k == 0`: Add `[2, 3]` to `result`. Return |
| `3` | `1` | `4` | `[2, 4]` | Place `4` at index `1`. Call `generateCombinations(5, 4, 0)` |
| `5` | `0` | - | `[2, 4]` | `k == 0`: Add `[2, 4]` to `result`. Return |
| `1` | `2` | `3` | `[3, 4]` | Place `3` at index `0`. Call `generateCombinations(4, 4, 1)` |
| `4` | `1` | `4` | `[3, 4]` | Place `4` at index `1`. Call `generateCombinations(5, 4, 0)` |
| `5` | `0` | - | `[3, 4]` | `k == 0`: Add `[3, 4]` to `result`. Return |

### Case 2: Minimal input (`n = 1, k = 1`)

| `start` | `k` | `i` | `combination` | Action |
| :--- | :--- | :--- | :--- | :--- |
| `1` | `1` | `1` | `[1]` | Place `1` at index `0`. Call `generateCombinations(2, 1, 0)` |
| `2` | `0` | - | `[1]` | `k == 0`: Add `[1]` to `result`. Return |

## Time & Space Complexity

**Time:** O(k * C(n, k)) — where `C(n, k)` is the total number of combinations, calculated as `n! / (k! * (n - k)!)`. For each combination, we spend `O(k)` time copying elements into the `result` list.

**Space:** O(k) — auxiliary space (excluding output memory). We use `O(k)` memory for the recursion call stack and `O(k)` space for the temporary `combination` array.

### Optimization Potential

Can this be improved? **Yes, slightly.**

Right now, the loop runs up to `n`: `for (int i = start; i <= n; ++i)`. 

However, if there aren't enough numbers left between `i` and `n` to pick `k` items, the code still explores those paths even though they are guaranteed to fail. We can prune (stop early) these dead ends by changing the loop condition to `i <= n - k + 1`.

```cpp
// Change the loop condition to prune impossible branches
for (int i = start; i <= n - k + 1; ++i) {
    combination[combination.size() - k] = i;
    generateCombinations(i + 1, n, k - 1, combination, result);
}
```

* Reason for `n - k + 1`: If we need `k` more numbers, the highest number we can start from must leave at least `k - 1` remaining numbers after it.

**Resulting Complexity:**
* **Time:** Still O(k * C(n, k)) asymptotically, but runs noticeably faster in practice by cutting down dead recursive branches.
* **Theoretical Best:** O(k * C(n, k)) is already the absolute theoretical lower bound, because any correct algorithm must construct and store every single combination of size `k`.

## Edge Cases Handled

* **Smallest Input (`n = 1, k = 1`):** Recursion stops immediately after one pick and correctly returns `[[1]]`.
* **Select All (`k = n`):** Handled correctly without out-of-bounds errors, returning a single combination containing all numbers `[1, 2, ..., n]`.
* **Select One (`k = 1`):** Recursion depth stays at 1, generating `n` single-element lists correctly.
