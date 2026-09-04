![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.35%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.97%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given an array of numbers called `nums`. The array contains `n` distinct numbers. 

These numbers are taken from the range `0` to `n`. Since there are `n + 1` possible numbers in the range `[0, n]` and the array only holds `n` numbers, exactly one number is missing.

Your task is to find and return that single missing number.

For example, if `nums = [3, 0, 1]`, there are `3` numbers in the array (`n = 3`). The full expected set of numbers from `0` to `3` is `0, 1, 2, 3`. Looking at the array, `2` is missing, so the answer is `2`.

## Intuition

The core trick behind this solution relies on basic math. 

There is a well-known mathematical formula to find the sum of all whole numbers from `0` up to `n`:
`expected_sum = n * (n + 1) / 2`

If no number were missing, the array elements would add up exactly to this expected sum. Because one number is missing, the actual sum of elements in `nums` will be smaller than expected.

The difference between what the sum **should be** and what the sum **actually is** equals the missing number!

## Approach

Here is how the code works step-by-step:

* `int sum =0 ;`: Creates an integer variable `sum` initialized to `0`. This keeps track of the running total of all numbers present inside `nums`.
* `for( int i=0 ; i<nums.size() ; i++)`: Sets up a loop using index `i` that starts at `0` and runs through every position in the array until it reaches `nums.size() - 1`.
* `sum+=nums[i];`: Takes the value at the current index `nums[i]` and adds it to our running total `sum`.
* `int total = (nums.size()*( nums.size()+1))/2;`: Calculates the total expected sum of all numbers from `0` up to `nums.size()` using the formula `n * (n + 1) / 2`.
* `return total-sum;`: Subtracts the actual array sum (`sum`) from the full expected sum (`total`). The remaining difference is the missing number, which gets returned.

## Dry Run

### Case 1: Typical unsorted array (`nums = [3, 0, 1]`)

Array length `n = 3`.

| Step | `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | 0 | Initialize `sum = 0`. |
| Loop 1 | 0 | 3 | 3 | Add `3` to `sum`. |
| Loop 2 | 1 | 0 | 3 | Add `0` to `sum`. |
| Loop 3 | 2 | 1 | 4 | Add `1` to `sum`. Loop ends. |
| End | - | - | 4 | Compute `total = 3 * (3 + 1) / 2 = 6`. Return `6 - 4 = 2`. |

Final Output: `2`

---

### Case 2: Short array with missing value at boundary (`nums = [0, 1]`)

Array length `n = 2`.

| Step | `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | 0 | Initialize `sum = 0`. |
| Loop 1 | 0 | 0 | 0 | Add `0` to `sum`. |
| Loop 2 | 1 | 1 | 1 | Add `1` to `sum`. Loop ends. |
| End | - | - | 1 | Compute `total = 2 * (2 + 1) / 2 = 3`. Return `3 - 1 = 2`. |

Final Output: `2`

## Time & Space Complexity

* **Time Complexity:** **O(n)** — The loop runs once for each of the `n` elements in `nums` to sum them up. The math formula calculation takes constant time **O(1)**.
* **Space Complexity:** **O(1)** — The solution uses only two extra integer variables (`sum` and `total`), regardless of how large the array is.

### Can it be improved?

The **O(n)** time and **O(1)** space complexities are already optimal. You cannot find a missing element without looking at every element at least once (**O(n)** time minimum), and you cannot use less memory than **O(1)** extra space.

However, the math formula method can risk an **integer overflow** if `n` is extremely large, because `n * (n + 1)` could exceed the maximum limit of a standard 32-bit integer before the division by 2 happens.

We can achieve the exact same **O(n)** time and **O(1)** space without any risk of overflow by using **Bit Manipulation (XOR)**.

#### Why Bitwise XOR works:
1. Any number XORed with itself equals `0` (for example, `5 ^ 5 = 0`).
2. Any number XORed with `0` stays the same (for example, `5 ^ 0 = 5`).

If we XOR all indices from `0` to `n` together with all values inside `nums`, every number present in the array will pair up with its matching index and cancel out to `0`. The only number that does not match any index will be left over as the final result.

```cpp
int ans = nums.size();
for (int i = 0; i < nums.size(); i++) {
    ans ^= i ^ nums[i];
}
return ans;
```

* `ans` starts with the value `n` (`nums.size()`).
* In each step of the loop, we XOR `ans` with index `i` and value `nums[i]`.
* Every pair cancels out, leaving only the missing number in `ans`.

* **Resulting Complexity:** **O(n)** Time, **O(1)** Space.
* **Theoretical Best:** **O(n)** Time and **O(1)** Space. Both the original math solution and the XOR solution reach this theoretical limit.

## Edge Cases Handled

* **Single element array (`n = 1`):** Works correctly for cases like `nums = [0]` (returns `1`) or `nums = [1]` (returns `0`).
* **Missing number is `0`:** If `0` is missing, `sum` equals `total`, so `total - sum` correctly produces `0`.
* **Missing number is `n`:** If the largest value `n` is missing, `sum` correctly reflects the sum of all smaller numbers, and subtracting it from `total` yields `n`.
* **Unsorted arrays:** Addition is commutative (order does not matter), so `sum` is correct regardless of how elements are ordered.
