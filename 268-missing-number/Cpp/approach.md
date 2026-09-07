![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.44%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.98%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of lockers numbered from 0 up to N. If there are 3 lockers, their numbers are 0, 1, 2, and 3. That means there are 4 lockers in total (N = 3). 

Now imagine someone takes all those locker numbers, scrambles them up, and throws away *one* of them. They hand you the remaining numbers in a list. Your job is to look at the list, figure out which number is missing, and return it.

For example, if the list is [3, 0, 1], the numbers present are 0, 1, and 3. The missing number from the range [0, 3] is 2.

## Intuition

The "aha" moment for this math approach comes from grade-school arithmetic. 

Instead of checking every number one by one using a **hash table** (a data structure that lets you look up items quickly) or sorting the list, we can use a simple math trick. We know exactly what the sum of all numbers from 0 to N *should* be. 

If we add up all the numbers actually present in our input list, that sum will be smaller than the expected total because one number is missing. The difference between what we *expect* the total sum to be and what we *actually* get by adding up the list items is the missing number itself.

## Approach

* `int sum = 0;`: Creates a variable named `sum` starting at zero to hold the running total of all the numbers found inside the input list.
* `for(int i=0; i<nums.size(); i++)`: Loops through every single element in the `nums` vector from the first index up to the last one.
* `sum += nums[i];`: Adds the current number from the list into our running `sum` total during each loop cycle.
* `int total = (nums.size() * (nums.size() + 1)) / 2;`: Calculates the expected sum of all integers from 0 up to N using the standard mathematical formula for triangular numbers, where N is the size of the array.
* `return total - sum;`: Subtracts the actual sum of the elements from the expected total sum, leaving precisely the missing number, which is then returned.

## Dry Run

### Case 1: Typical case ([3, 0, 1])

| `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- |
| - | - | 0 | Initialize `sum` to 0. `nums.size()` is 3. |
| 0 | 3 | 3 | Add `nums[0]` (3) to `sum`. |
| 1 | 0 | 3 | Add `nums[1]` (0) to `sum`. Total remains 3. |
| 2 | 1 | 4 | Add `nums[2]` (1) to `sum`. Total becomes 4. |
| - | - | 4 | Calculate `total` as (3 * 4) / 2 = 6. |
| - | - | 2 | Return `total - sum` (6 - 4 = 2). |

### Case 2: Edge case with two elements ([0, 1])

| `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- |
| - | - | 0 | Initialize `sum` to 0. `nums.size()` is 2. |
| 0 | 0 | 0 | Add `nums[0]` (0) to `sum`. |
| 1 | 1 | 1 | Add `nums[1]` (1) to `sum`. Total becomes 1. |
| - | - | 1 | Calculate `total` as (2 * 3) / 2 = 3. |
| - | - | 2 | Return `total - sum` (3 - 1 = 2). |

## Time & Space Complexity

* **Time:** O(n) — The code uses a single loop that visits each of the N elements in the array exactly once.
* **Space:** O(1) — The code only creates a couple of integer variables (`sum` and `total`) regardless of how large the input array grows, meaning it uses constant extra memory.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this solution is already optimal. 

You cannot achieve a faster time complexity than O(n) because you must at least look at every element in the array to know if it is present. Similarly, O(1) space complexity is the absolute best possible memory usage because you are only storing a few simple variables and not creating any new data structures.

## Edge Cases Handled

* **Smallest array size (N = 1):** Correctly handles arrays with just one element (like [0] or [1]) by applying the math formula smoothly.
* **Missing element at the very end:** If the missing number is N itself, the sum of the array will equal the expected total, causing `total - sum` to correctly evaluate to 0.
* **Missing element at zero:** If 0 is missing from the range, the sum of the array will equal the expected total minus zero, and the math still resolves accurately.
