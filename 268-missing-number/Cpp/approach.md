![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.42%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.97%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given an array of numbers called `nums`. The array has a length of `n`. 

Inside `nums`, you are supposed to have all the numbers starting from `0` up to `n`. However, exactly one number from that range is missing, and all numbers present are unique. 

Your goal is to find and return the missing number.

For example, if `nums = [3, 0, 1]`, the length `n` is `3`. The full list of numbers from `0` to `3` should be `0, 1, 2, 3`. Looking at `nums`, the number `2` is missing.

## Intuition

Think of this like checking the total price on a receipt. 

If you know what every item from `0` to `n` should sum up to, you can compare that to the actual sum of the items you actually have. The difference between the expected total and your actual total is the missing item.

There is a simple math formula to find the sum of all numbers from `0` to `n` without adding them one by one: 

```
expected_sum = n * (n + 1) / 2
```

If you sum all elements in `nums`, and subtract that from `expected_sum`, the remaining value must be the missing number.

## Approach

* `int sum =0 ;` — Create an integer variable `sum` initialized to `0`. This keeps track of the running total of all numbers inside `nums`.
* `for( int i=0 ; i<nums.size() ; i++){` — Start a loop using index `i`, running from `0` up to `nums.size() - 1` to look at every element in the array.
* `sum+=nums[i];` — Add the value of the current element `nums[i]` to `sum`.
* `int total = (nums.size()*( nums.size()+1))/2;` — Calculate the expected sum of all numbers from `0` to `n` (where `n` is `nums.size()`) using the arithmetic formula, and store it in `total`.
* `return total-sum;` — Subtract `sum` (what you have) from `total` (what you should have) and return the result.

## Dry Run

### Case 1: Typical unsorted input (`nums = [3, 0, 1]`)

Here, `nums.size()` is `3`.

| i | nums[i] | sum | total | Action |
|---|---|---|---|---|
| - | - | 0 | unassigned | Initialize `sum` to `0`. |
| 0 | 3 | 3 | unassigned | Add `nums[0]` (3) to `sum`. |
| 1 | 0 | 3 | unassigned | Add `nums[1]` (0) to `sum`. |
| 2 | 1 | 4 | unassigned | Add `nums[2]` (1) to `sum`. |
| End | - | 4 | 6 | Calculate `total` as `(3 * 4) / 2 = 6`. Return `total - sum` (`6 - 4 = 2`). |

**Output:** `2`

---

### Case 2: Missing upper bound value (`nums = [0, 1]`)

Here, `nums.size()` is `2`.

| i | nums[i] | sum | total | Action |
|---|---|---|---|---|
| - | - | 0 | unassigned | Initialize `sum` to `0`. |
| 0 | 0 | 0 | unassigned | Add `nums[0]` (0) to `sum`. |
| 1 | 1 | 1 | unassigned | Add `nums[1]` (1) to `sum`. |
| End | - | 1 | 3 | Calculate `total` as `(2 * 3) / 2 = 3`. Return `total - sum` (`3 - 1 = 2`). |

**Output:** `2`

## Time & Space Complexity

* **Time Complexity:** **O(n)** — The `for` loop inspects each of the `n` elements in `nums` once. The mathematical calculation takes constant time **O(1)**.
* **Space Complexity:** **O(1)** — Only two integer variables (`sum` and `total`) are created, using a constant amount of extra memory regardless of the size of `nums`.

### Can this be improved?

This code already achieves the theoretically optimal complexity of **O(n)** time and **O(1)** space. You must visit every number at least once to know what is missing, so you cannot go faster than **O(n)** time. You also cannot use less memory than **O(1)**.

However, arithmetic addition can theoretically cause integer overflow if numbers or array sizes become extremely large (though within this problem's constraint of `n <= 10^4`, standard integers will not overflow). 

An alternative technique uses **Bitwise XOR** logic instead of addition. The XOR operation (`^`) cancels out identical numbers (because `A ^ A = 0` and `A ^ 0 = A`). If you XOR all numbers from `0` to `n` together with all numbers inside `nums`, every number present twice cancels out, leaving only the missing number.

```cpp
int missingNumber(vector<int>& nums) {
    int xor_all = nums.size();
    for (int i = 0; i < nums.size(); i++) {
        xor_all ^= i ^ nums[i];
    }
    return xor_all;
}
```

* `int xor_all = nums.size();` — Start with `n` so it gets included in the XOR combinations.
* `xor_all ^= i ^ nums[i];` — XOR the index `i` (representing complete range values) and the actual value `nums[i]` into `xor_all`.

**Improved Complexity:** **Time:** **O(n)**, **Space:** **O(1)**. This matches the optimal theoretical bounds while eliminating any risk of number overflow.

## Edge Cases Handled

* **Smallest array size (`n = 1`):** Works when `nums` contains only `[0]` (returns `1`) or `[1]` (returns `0`).
* **Missing element is `0`:** If the array contains `[1, 2, 3]`, `total` is `6` and `sum` is `6`. The formula yields `6 - 6 = 0`, correctly detecting `0`.
* **Missing element is `n`:** Handled smoothly, as shown in Case 2 of the Dry Run.
* **Unsorted arrays:** The order of numbers does not affect addition. Unsorted inputs like `[9, 6, 4, 2, 3, 5, 7, 0, 1]` work without sorting.
