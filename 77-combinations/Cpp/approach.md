![Runtime](https://img.shields.io/badge/Runtime-31%20ms%20(beats%2099.20%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-62.6%20MB%20(beats%2071.95%25)-green?style=for-the-badge)

---

## Problem Explained

The problem asks us to find all possible groups of `k` unique numbers chosen from the range `1` to `n`. 

In combinations, **order does not matter**. For example, `[1, 2]` and `[2, 1]` are considered the exact same combination, so we should only include one of them.

### Example
If `n = 4` and `k = 2`, our available pool of numbers is `[1, 2, 3, 4]`. We need to pick groups of `2` numbers:
* Answer: `[[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]`
* Notice that `[2,1]` is not included because `[1,2]` already covers it.

---

## Intuition

To avoid generating duplicates like `[2, 1]` when we already have `[1, 2]`, we enforce a simple rule: **always pick numbers in strictly increasing order**. 

If we choose `1` first, the next numbers must be picked from `2, 3, 4`. If we choose `2` first, the next numbers must be picked from `3, 4`. This guarantees that every combination is unique and sorted.

We build each combination step-by-step using **backtracking** (a recursive process of exploring a choice, going deeper, and then trying the next choice):
1. Pick a number.
2. Put it in our current combination list.
3. Recursively pick the remaining numbers from the higher values left.
4. Once a combination reaches length `k`, save it to our results.

A neat trick in this C++ code is pre-allocating a vector of size `k`. Instead of adding and removing elements repeatedly (`push_back` and `pop_back`), the code directly overwrites the array slots at calculated positions.

---

## Approach

Here is how the code works step-by-step:

* `vector<int> combination(k);`: Creates a fixed-size vector of length `k`. This avoids dynamic array resizing during recursion.
* `generateCombinations(1, n, k, combination, result);`: Starts the recursive helper function. We pass `1` as our starting number and `k` as the count of numbers left to pick.
* `if (k == 0)`: Checks if we have picked all `k` numbers needed. When `k` reaches `0`, the combination is full.
* `result.push_back(combination);`: Adds the completed `combination` vector to our overall list of results.
* `return;`: Steps back out of the current recursion layer to explore other choices.
* `for (int i = start; i <= n; ++i)`: Loops through every valid integer `i` from `start` up to `n`.
* `combination[combination.size() - k] = i;`: Places the chosen number `i` into the correct index of `combination`. The index is computed as `combination.size() - k`.
* `generateCombinations(i + 1, n, k - 1, combination, result);`: Recursively calls the helper to pick the next number. We pass `i + 1` so we only pick larger numbers, and `k - 1` because we need one fewer number now.

---

## Dry Run

### Case 1: Standard case (`n = 4`, `k = 2`)

| `start` | `k` | `i` | `combination` | Action |
|---|---|---|---|---|
| 1 | 2 | 1 | `[1, 0]` | Set index 0 (`2 - 2`) to 1. Recurse with `start=2, k=1`. |
| 2 | 1 | 2 | `[1, 2]` | Set index 1 (`2 - 1`) to 2. Recurse with `start=3, k=0`. |
| 3 | 0 | - | `[1, 2]` | Base case (`k == 0`). Add `[1, 2]` to `result`. Return. |
| 2 | 1 | 3 | `[1, 3]` | Set index 1 to 3. Recurse with `start=4, k=0`. |
| 4 | 0 | - | `[1, 3]` | Base case (`k == 0`). Add `[1, 3]` to `result`. Return. |
| 2 | 1 | 4 | `[1, 4]` | Set index 1 to 4. Recurse with `start=5, k=0`. |
| 5 | 0 | - | `[1, 4]` | Base case (`k == 0`). Add `[1, 4]` to `result`. Return. |
| 1 | 2 | 2 | `[2, 4]` | Set index 0 to 2. Recurse with `start=3, k=1`. |
| 3 | 1 | 3 | `[2, 3]` | Set index 1 to 3. Recurse with `start=4, k=0`. |
| 4 | 0 | - | `[2, 3]` | Base case (`k == 0`). Add `[2, 3]` to `result`. Return. |
| 3 | 1 | 4 | `[2, 4]` | Set index 1 to 4. Recurse with `start=5, k=0`. |
| 5 | 0 | - | `[2, 4]` | Base case (`k == 0`). Add `[2, 4]` to `result`. Return. |
| 1 | 2 | 3 | `[3, 4]` | Set index 0 to 3. Recurse with `start=4, k=1`. |
| 4 | 1 | 4 | `[3, 4]` | Set index 1 to 4. Recurse with `start=5, k=0`. |
| 5 | 0 | - | `[3, 4]` | Base case (`k == 0`). Add `[3, 4]` to `result`. Return. |
| 1 | 2 | 4 | `[4, 4]` | Set index 0 to 4. Recurse with `start=5, k=1`. Loop `i <= 4` ends. |

Final `result` = `[[1,2], [1,3], [1,4], [2,3], [2,4], [3,4]]`.

---

### Case 2: Max selection (`n = 3`, `k = 3`)

| `start` | `k` | `i` | `combination` | Action |
|---|---|---|---|---|
| 1 | 3 | 1 | `[1, 0, 0]` | Set index 0 (`3 - 3`) to 1. Recurse with `start=2, k=2`. |
| 2 | 2 | 2 | `[1, 2, 0]` | Set index 1 (`3 - 2`) to 2. Recurse with `start=3, k=1`. |
| 3 | 1 | 3 | `[1, 2, 3]` | Set index 2 (`3 - 1`) to 3. Recurse with `start=4, k=0`. |
| 4 | 0 | - | `[1, 2, 3]` | Base case (`k == 0`). Add `[1, 2, 3]` to `result`. Return. |

Final `result` = `[[1,2,3]]`.

---

## Time & Space Complexity

* **Time Complexity:** O(k * C(n, k)), where `C(n, k) = n! / (k! * (n - k)!)`.
  * There are `C(n, k)` total combinations.
  * For each combination found, copying it into `result` takes O(k) time.
* **Space Complexity:** O(k) auxiliary space.
  * The recursion depth reaches at most `k` calls.
  * The temporary `combination` array takes O(k) memory. (We ignore the memory used by `result` for storing the final output).

### Can it be improved?

Yes, we can optimize the execution time using **loop pruning**.

Currently, the loop runs `for (int i = start; i <= n; ++i)`. However, if there are not enough remaining numbers left in `[i, n]` to fill the remaining `k` slots, exploring that branch is a waste of work.

To pick `k` numbers, we need at least `k` elements available. If we consider index `i`, there are `n - i + 1` elements remaining. We must ensure `n - i + 1 >= k`, which simplifies to `i <= n - k + 1`.

#### Pruned Code Optimization:
```cpp
// Change the loop condition from 'i <= n' to 'i <= n - k + 1'
for (int i = start; i <= n - k + 1; ++i) {
    combination[combination.size() - k] = i;
    generateCombinations(i + 1, n, k - 1, combination, result);
}
```

* **Why it works:** If `n = 4` and we need `k = 2` numbers, starting at `i = 4` leaves only 1 number available (`[4]`), but we need 2 numbers. `n - k + 1` equals `4 - 2 + 1 = 3`. So `i` stops at `3`, avoiding dead branches completely.
* **Improved Complexity:** The Big-O theoretical bound stays **O(k * C(n, k))**, but actual runtime drops significantly because zero invalid recursive paths are explored.
* **Optimal Status:** This pruned version achieves the **theoretical best possible complexity** for this problem, as any algorithm must at least spend time generating and storing all `C(n, k)` valid output items.

---

## Edge Cases Handled

* **Single Element Pick (`k = 1`):** The logic cleanly runs the loop once per number, returning `[[1], [2], ..., [n]]`.
* **Select All Elements (`k = n`):** The recursion follows exactly one path straight down to produce `[[1, 2, ..., n]]`.
* **Smallest Input (`n = 1, k = 1`):** Handled seamlessly without out-of-bounds array access.
* **No Duplicate Work:** By passing `i + 1` to the next call, the algorithm avoids generating permutations or repeated values without needing extra dynamic memory (like hash sets).
