![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.44%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.98%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given an array of numbers called `nums`. If the array contains `n` numbers, those numbers are taken from the full range of `0` to `n`. 

Because there are `n + 1` total numbers in the full range from `0` to `n`, but the array only has `n` slots, exactly one number is missing. Your job is to find and return that single missing number.

For example, if `nums = [3, 0, 1]`, there are `3` numbers in the array (`n = 3`). The full list of numbers should be `0, 1, 2, 3`. Looking at the array, `2` is missing, so the answer is `2`.

## Intuition

The core idea relies on a simple math rule: we can calculate what the sum of all numbers from `0` to `n` *should* be using a famous formula:

```text
expected_sum = (n * (n + 1)) / 2
```

If we add up all the numbers that are actually in our array, that total will be smaller than the expected sum. The difference between the expected total sum and the actual sum of our array is exactly the missing number!

For example, for numbers `0` to `3`:
* Expected sum: `(3 * 4) / 2 = 6`
* Actual array elements: `[3, 0, 1]`, which add up to `4`
* Missing number: `6 - 4 = 2`

## Approach

Here is how the code works step-by-step:

* `int sum =0 ;`: Creates a tracking variable named `sum` and sets it to `0`. This will hold the total sum of all elements currently inside the array.
* `for( int i=0 ; i<nums.size() ; i++)`: Starts a loop that goes through every position in `nums`, from index `0` up to `nums.size() - 1`.
* `sum+=nums[i];`: Adds the element at the current index `nums[i]` to our running variable `sum`.
* `int total = (nums.size()*( nums.size()+1))/2;`: Uses the math formula to calculate what the sum of all numbers from `0` to `n` (where `n` is `nums.size()`) ought to be, saving it in `total`.
* `return total-sum;`: Subtracts the actual array total (`sum`) from the full expected total (`total`). The remaining value is our missing number.

## Dry Run

### Case 1: Standard input with unsorted numbers (`nums = [3, 0, 1]`)

Array length `nums.size()` is `3`.

| Step | Loop index `i` | Current element `nums[i]` | Running `sum` | Action / Decision |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | 0 | Initialize `sum = 0`. |
| 1 | 0 | 3 | 3 | Add `3` to `sum`. |
| 2 | 1 | 0 | 3 | Add `0` to `sum`. |
| 3 | 2 | 1 | 4 | Add `1` to `sum`. |
| End Loop | - | - | 4 | Loop finishes. |
| Formula | - | - | 4 | Calculate `total = (3 * (3 + 1)) / 2 = 6`. |
| Return | - | - | 4 | Return `total - sum` which is `6 - 4 = 2`. |

### Case 2: Array where missing number is at the boundary (`nums = [0, 1]`)

Array length `nums.size()` is `2`.

| Step | Loop index `i` | Current element `nums[i]` | Running `sum` | Action / Decision |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | 0 | Initialize `sum = 0`. |
| 1 | 0 | 0 | 0 | Add `0` to `sum`. |
| 2 | 1 | 1 | 1 | Add `1` to `sum`. |
| End Loop | - | - | 1 | Loop finishes. |
| Formula | - | - | 1 | Calculate `total = (2 * (2 + 1)) / 2 = 3`. |
| Return | - | - | 1 | Return `total - sum` which is `3 - 1 = 2`. |

## Time & Space Complexity

* **Time Complexity:** **O(n)** — The code iterates through the array of length `n` exactly once to calculate the sum. The math formula calculation takes constant O(1) time.
* **Space Complexity:** **O(1)** — Memory usage is constant because the code only creates two integer variables (`sum` and `total`), regardless of how large the array gets.

**Is this optimal?**
Yes, **O(n) time and O(1) space is the theoretical best possible complexity** for an unsorted input array. 
* You cannot do better than **O(n) time** because you must inspect every number in the array at least once to know which one is absent.
* You cannot do better than **O(1) space** because no extra memory structures (like hash sets or extra arrays) are used.

*(Note: An alternative O(n) time, O(1) space solution uses the Bitwise XOR operator `^` to avoid potential integer overflow if `n` were extremely large, but both approaches share the exact same optimal Big-O complexity).*

## Edge Cases Handled

* **Missing number is 0:** If `nums = [1, 2, 3]`, `total` is `6` and `sum` is `6`. The calculation `6 - 6` correctly returns `0`.
* **Missing number is n:** If `nums = [0, 1]`, `total` is `3` and `sum` is `1`. The calculation `3 - 1` correctly returns `2`.
* **Single element array:** If `nums = [0]`, `total` is `1` and `sum` is `0`, returning `1`. If `nums = [1]`, `total` is `1` and `sum` is `1`, returning `0`.
* **Unordered input:** Addition works the same regardless of order, so scrambled inputs like `[9, 6, 4, 2, 3, 5, 7, 0, 1]` compute the correct total without requiring prior sorting.
