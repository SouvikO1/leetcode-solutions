![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.38%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.97%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given an array of numbers called `nums`. The array contains `n` unique numbers picked from the full range of `0` to `n`. 

Because there are `n + 1` possible numbers in the range `[0, n]` and the array only has `n` slots, exactly one number is missing. Your goal is to find that missing number and return it.

For example, if `nums = [3, 0, 1]`, the array length `n` is 3. The full range of numbers should be `0`, `1`, `2`, and `3`. Looking at the array, `2` is not present, so the answer is `2`.

## Intuition

The core idea is based on simple arithmetic. 

If you know what all numbers from `0` to `n` should add up to, you can compare that ideal sum with the actual sum of the numbers inside your array. 

There is a simple formula to calculate the total sum of all numbers from `0` to `n`:
```text
total = (n * (n + 1)) / 2
```

If you add up all numbers currently in `nums` and subtract that value from `total`, the remaining value is guaranteed to be the missing number.

## Approach

*   `int sum = 0;` — Initialize a variable named `sum` to 0. This variable keeps track of the running total of all numbers present in the array.
*   `for( int i=0 ; i<nums.size() ; i++)` — Start a loop that runs through every index `i` from `0` up to `nums.size() - 1`.
*   `sum+=nums[i];` — Add the number at the current position `nums[i]` into `sum`.
*   `int total = (nums.size()*( nums.size()+1))/2;` — Calculate the sum of all expected numbers from `0` to `n` using the math formula `(n * (n + 1)) / 2`, where `nums.size()` is `n`. Store this result in `total`.
*   `return total-sum;` — Subtract the actual sum of elements in `nums` from the expected total sum. Return this difference as the missing number.

## Dry Run

### Case 1: Typical unsorted array (`nums = [3, 0, 1]`)

Array size `n = 3`. Expected range is `0` to `3`.

| Step | Index `i` | Current Value `nums[i]` | Running `sum` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Initial | - | - | 0 | Initialize `sum = 0` |
| Loop 1 | 0 | 3 | 3 | Add `3` to `sum` |
| Loop 2 | 1 | 0 | 3 | Add `0` to `sum` |
| Loop 3 | 2 | 1 | 4 | Add `1` to `sum` |

After the loop:
*   `total = (3 * (3 + 1)) / 2 = (3 * 4) / 2 = 6`
*   `total - sum = 6 - 4 = 2`

**Output:** `2`

---

### Case 2: Missing number is `n` (`nums = [0, 1]`)

Array size `n = 2`. Expected range is `0` to `2`.

| Step | Index `i` | Current Value `nums[i]` | Running `sum` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Initial | - | - | 0 | Initialize `sum = 0` |
| Loop 1 | 0 | 0 | 0 | Add `0` to `sum` |
| Loop 2 | 1 | 1 | 1 | Add `1` to `sum` |

After the loop:
*   `total = (2 * (2 + 1)) / 2 = (2 * 3) / 2 = 3`
*   `total - sum = 3 - 1 = 2`

**Output:** `2`

## Time & Space Complexity

*   **Time:** O(n) — We iterate through the array `nums` of size `n` exactly once to compute the sum.
*   **Space:** O(1) — We only use a constant amount of extra memory for standard integer variables (`sum`, `total`, `i`).

**Is this optimal?**
Yes, this code is already optimal in terms of Big-O complexity! 
*   **Time O(n)** is optimal because you must inspect every element in an unsorted array at least once to know which number is missing.
*   **Space O(1)** is optimal because you cannot use less memory than a few scalar variables.

**Optional Optimization Note:**
While the math formula approach is O(n) time and O(1) space, adding numbers together can theoretically lead to integer overflow if `n` is extremely large. To avoid integer overflow completely, you can use **Bit Manipulation (XOR)**. 

XORing a number with itself cancels it out (`a ^ a = 0`), and XORing with 0 leaves it unchanged (`a ^ 0 = a`). If you XOR all indices from `0` to `n` together with all values in `nums`, every number present cancels itself out, leaving only the missing number.

```cpp
int xor_all = nums.size();
for (int i = 0; i < nums.size(); i++) {
    xor_all ^= i ^ nums[i]; // XOR the index and the value
}
return xor_all;
```

Both the summation and XOR methods share the same optimal complexity: **Time: O(n)**, **Space: O(1)**.

## Edge Cases Handled

*   **Missing Number is 0:** Handled correctly. If `0` is missing (e.g., `nums = [1, 2]`), `sum` is `3` and `total` is `3`, yielding `3 - 3 = 0`.
*   **Missing Number is `n`:** Handled correctly. If the largest number is missing (e.g., `nums = [0, 1]`), `total` is `3` and `sum` is `1`, yielding `3 - 1 = 2`.
*   **Single Element Array (`n = 1`):** Works properly. If `nums = [0]`, `total` is `1` and `sum` is `0`, yielding `1 - 0 = 1`.
*   **Unsorted Input:** Because addition is commutative (order does not matter), the numbers can appear in any arbitrary order without breaking the logic.
