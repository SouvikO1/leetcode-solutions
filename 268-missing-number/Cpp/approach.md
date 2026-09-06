![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.42%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.97%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of lockers numbered from 0 up to N. If you have N lockers, you should have N distinct numbers. But someone took one locker key away! 

Your job is to look at a list of the numbers that are left and figure out which one is missing. 

For example, if you have the numbers [3, 0, 1], there are 3 numbers total. That means the full set should be 0, 1, 2, and 3. Looking at your list, you see 3, 0, and 1. The number 2 is missing. That is your answer.

## Intuition

The "aha" moment here is a math trick. Instead of searching through the list over and over to see which number is missing, we can just use addition. 

First, we add up all the numbers actually sitting inside our input list. 
Next, we figure out what the sum *should* be if no numbers were missing. We can use a quick formula for the sum of a sequence of numbers from 0 up to N: N times (N + 1) divided by 2. 
Finally, the difference between what the sum *should* be and what it *actually* is must be the missing number. 

It is like weighing a jar of coins: you know what the total weight should be if all coins are present, and the missing weight tells you exactly how many coins are gone.

## Approach

* `int sum = 0;`: Creates a variable named `sum` and sets it to zero. This will keep a running total of all the numbers found inside the `nums` vector.
* `for(int i=0; i<nums.size(); i++)`: Starts a loop that goes through every single element in the `nums` vector from the first index (0) up to the last index.
* `sum += nums[i];`: Adds the current number from the vector onto our running `sum` total during each turn of the loop.
* `int total = (nums.size() * (nums.size() + 1)) / 2;`: Calculates the expected sum of all numbers from 0 up to N using the standard sequence sum formula. `nums.size()` is our N.
* `return total - sum;`: Subtracts the actual sum of the vector from the expected total sum, and returns the leftover difference, which is our missing number.

## Dry Run

### Case 1: Typical case ([3, 0, 1])

| Step | `i` | `nums[i]` | `sum` | `total` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Initialization | - | - | 0 | (not yet) | Create `sum` variable set to 0. |
| Loop 0 | 0 | 3 | 3 | (not yet) | Add `nums[0]` (3) to `sum`. `sum` becomes 3. |
| Loop 1 | 1 | 0 | 3 | (not yet) | Add `nums[1]` (0) to `sum`. `sum` stays 3. |
| Loop 2 | 2 | 1 | 4 | (not yet) | Add `nums[2]` (1) to `sum`. `sum` becomes 4. |
| Math calculation | - | - | 4 | 6 | Calculate expected total: 3 * (3 + 1) / 2 = 6. |
| Return | - | - | 4 | 6 | Return `total - sum` (6 - 4 = 2). Missing number is 2. |

### Case 2: Small case ([0, 1])

| Step | `i` | `nums[i]` | `sum` | `total` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Initialization | - | - | 0 | (not yet) | Create `sum` variable set to 0. |
| Loop 0 | 0 | 0 | 0 | (not yet) | Add `nums[0]` (0) to `sum`. `sum` stays 0. |
| Loop 1 | 1 | 1 | 1 | (not yet) | Add `nums[1]` (1) to `sum`. `sum` becomes 1. |
| Loop 2 (Exit) | 2 | - | 1 | (not yet) | Loop ends because `i` is no longer less than `nums.size()` (2). |
| Math calculation | - | - | 1 | 3 | Calculate expected total: 2 * (2 + 1) / 2 = 3. |
| Return | - | - | 1 | 3 | Return `total - sum` (3 - 1 = 2). Missing number is 2. |

## Time & Space Complexity

**Time:** O(n) — The code uses a single loop that looks at every element in the vector of size N exactly once. 
**Space:** O(1) — The code only creates a few integer variables (`sum`, `total`, loop index `i`), meaning it uses a constant amount of extra memory no matter how large the input vector grows.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

This code is already optimal. 

- **Time Complexity:** O(n) is the best possible time complexity because you must look at the elements in the array at least once to know what is inside them. If you skipped looking at even one element, it could be the missing one.
- **Space Complexity:** O(1) is the best possible space complexity because we are solving the problem using only a fixed number of variables without building any extra data structures like hash tables or arrays.

No further improvements are possible.

## Edge Cases Handled

* **Single element array (e.g., [0] or [1]):** The loop runs once, `nums.size()` handles the math correctly, and it accurately finds if 0 or 1 is missing.
* **Missing element at the very end (e.g., [0, 1, 2]):** The expected sum covers up to 3, but the actual sum only adds up to 3. The math correctly computes a difference of 3.
* **Missing element at the very beginning (e.g., [1, 2, 3]):** The expected sum covers up to 3, but the actual sum only adds up to 6 instead of 6? Wait, actual sum is 1+2+3 = 6, expected sum for N=3 is 6. Wait, if 0 is missing, actual sum is 1+2+3=6, expected is 6, so 6-6=0. The math correctly evaluates to 0.
