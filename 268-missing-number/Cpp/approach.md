![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.44%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.98%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of lockers numbered from 0 up to N. If there are 3 lockers, their numbers are 0, 1, 2, and 3. That means there are 4 lockers in total (N = 3, but the numbers go from 0 to 3). 

Now imagine someone takes away one locker at random. You are given the remaining lockers in a random, jumbled-up list (an **array**). Your job is to find out which number is missing. 

For example, if the list is [3, 0, 1], the full set of numbers from 0 to 3 should be 0, 1, 2, and 3. Comparing our list to the full set, we can see that the number 2 is missing. That is our answer.

## Intuition

Instead of searching through the list over and over to see which number is missing, we can use a simple math trick. 

Think of it like adding up a pile of weights. We know what the total weight *should* be if all the numbers from 0 up to N were present in our list. We can calculate this expected total instantly using a classic math formula: (N * (N + 1)) / 2. 

Next, we add up the actual numbers that are sitting in front of us in the list. If we subtract our actual sum from the expected total, the leftover amount *must* be the missing number. It is like weighing a bag of flour with one scoop missing; the difference between the full weight and the current weight tells you exactly how much flour is gone.

## Approach

* `int sum = 0;` — Creates a variable named `sum` to keep a running total of all the numbers found inside the input list, starting at zero.
* `for(int i=0; i<nums.size(); i++)` — Starts a loop that looks at every single number in the `nums` list one by one, from the first element up to the last.
* `sum += nums[i];` — Adds the current number from the list into our running `sum` total.
* `int total = (nums.size() * (nums.size() + 1)) / 2;` — Calculates what the sum of all numbers *should* be if no numbers were missing, using the size of the list as N in our math formula.
* `return total - sum;` — Subtracts our actual list sum from the expected total, leaving us with the exact value of the missing number, which is then returned as the final answer.

## Dry Run

### Case 1: Typical case (nums = [3, 0, 1])

| `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- |
| 0 | 3 | 3 | Loop starts, adds 3 to `sum`. `nums.size()` is 3. |
| 1 | 0 | 3 | Adds 0 to `sum` (total stays 3). |
| 2 | 1 | 4 | Adds 1 to `sum`. Loop ends. |
| - | - | - | `total` is calculated as (3 * 4) / 2 = 6. |
| - | - | - | Returns `total - sum` (6 - 4 = 2). Missing number is 2. |

### Case 2: Edge case with larger array (nums = [0, 1])

| `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- |
| 0 | 0 | 0 | Loop starts, adds 0 to `sum`. `nums.size()` is 2. |
| 1 | 1 | 1 | Adds 1 to `sum`. Loop ends. |
| - | - | - | `total` is calculated as (2 * 3) / 2 = 3. |
| - | - | - | Returns `total - sum` (3 - 1 = 2). Missing number is 2. |

## Time & Space Complexity

**Time:** O(n) — The code uses a single loop that goes through the list of numbers one time. If the list has 10,000 numbers, the loop runs 10,000 times. 

**Space:** O(1) — The code only creates a couple of simple integer variables (`sum` and `total`) to do the math. It does not create any new lists or data structures that grow as the input gets bigger.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

This code is already optimal for both time and space. 

* **Time-wise:** To find a missing number, you fundamentally have to look at the numbers provided. Looking at a list of size N at least once takes O(n) time, so we cannot go any faster than that.
* **Space-wise:** O(1) constant space is the absolute best possible memory usage because we are solving the problem using only a few basic math variables without storing extra copies of the data. 

No further improvements are needed.

## Edge Cases Handled

* **Single element array (e.g., [0] or [1]):** The code correctly computes N as 1, finds the expected total, and subtracts the single item to find the missing neighbor.
* **Missing element at the very end (e.g., [0, 1, 2]):** The expected sum matches the actual sum until the formula calculation reveals the missing upper boundary.
* **Missing zero at the very beginning (e.g., [1, 2, 3]):** The loop sums 1, 2, and 3 to get 6. The expected total for N = 3 is also 6. Subtracting them gives 0, correctly identifying that zero is missing.
