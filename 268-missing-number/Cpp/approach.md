![Runtime](https://img.shields.io/badge/Runtime-12%20ms%20(beats%2012.44%25)-red?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-20.8%20MB%20(beats%2099.98%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of lockers numbered from 0 up to N. If there are 3 lockers, their numbers are 0, 1, 2, and 3. That means there are 4 lockers in total (N = 3). 

Someone takes all those locker numbers, mixes them up, and throws away *one* of them. They hand you the remaining numbers in a random list (an **array**, which is just an ordered line of boxes holding numbers). Your job is to look at that list and figure out which single number is missing from the full sequence.

For example, if you are given the list [3, 0, 1], the full sequence should have been 0, 1, 2, and 3. Comparing what you have to what should be there, you can see that the number 2 is missing.

## Intuition

The "aha" moment for this math approach comes from thinking about a sum of numbers. 

Instead of checking off numbers one by one or sorting the list, we can use a classic math trick. We know exactly how many numbers there should be and what range they cover (from 0 up to N). We can easily calculate what the **sum** of all those numbers *should* be if none were missing. 

Then, we add up all the numbers actually present in the given list. If we subtract the actual sum from the expected total sum, the leftover difference is precisely the missing number! 

## Approach

* `int sum =0 ;` — Initializes a variable named `sum` to 0. This will keep a running total of all the numbers actually found inside the input list.
* `for( int i=0 ; i<nums.size() ; i++)` — Starts a loop that looks at every single element in the list `nums` one by one, starting from the first position up to the end of the list.
* `sum+=nums[i];` — Adds the value of the current number in the list to our running `sum` total.
* `int total = (nums.size()*( nums.size()+1))/2;` — Calculates what the sum of the full sequence of numbers from 0 to N *should* be using the standard arithmetic sequence formula (length times length plus one, divided by 2), and stores it in `total`.
* `return total-sum;` — Subtracts the sum of the numbers we actually found from the expected `total` sum, revealing the missing number, and returns it.

## Dry Run

### Case 1: Typical case ([3, 0, 1])

| Step | `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | 0 | Initialize `sum` to 0 |
| Loop 1 | 0 | 3 | 3 | Add 3 to `sum` (0 + 3 = 3) |
| Loop 2 | 1 | 0 | 3 | Add 0 to `sum` (3 + 0 = 3) |
| Loop 3 | 2 | 1 | 4 | Add 1 to `sum` (3 + 1 = 4) |
| Math | - | - | 4 | Calculate expected total for N = 3: (3 * 4) / 2 = 6 |
| Return | - | - | 4 | Return total minus sum: 6 - 4 = 2 |

### Case 2: Edge case with minimal size ([0, 1])

| Step | `i` | `nums[i]` | `sum` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | 0 | Initialize `sum` to 0 |
| Loop 1 | 0 | 0 | 0 | Add 0 to `sum` (0 + 0 = 0) |
| Loop 2 | 1 | 1 | 1 | Add 1 to `sum` (0 + 1 = 1) |
| Math | - | - | 1 | Calculate expected total for N = 2: (2 * 3) / 2 = 3 |
| Return | - | - | 1 | Return total minus sum: 3 - 1 = 2 |

## Time & Space Complexity

* **Time:** O(n) — The code loops through the list of size N exactly once to calculate the sum of the elements.
* **Space:** O(1) — It only creates a couple of integer variables (`sum` and `total`), taking up a constant, tiny amount of extra memory no matter how large the input list gets.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

This code is already optimal in terms of both time and space. 

No further improvement is possible because to find a missing number in an unsorted list, you fundamentally have to look at all N elements at least once, which takes O(n) time. Doing it with variables instead of storing new data structures keeps extra space at O(1). 

*(Note: While the runtime performance on the platform can sometimes fluctuate due to server load or compiler quirks, the mathematical algorithm itself is running at the absolute theoretical limit of efficiency).*

## Edge Cases Handled

* **Missing 0:** If 0 is the missing number from the sequence (e.g., input list `[1, 2]`), the loop sum will be 3, the expected total for N = 2 is 3, and 3 - 3 = 0.
* **Missing the maximum value N:** If the largest number is missing (e.g., input list `[0, 1]`), the loop sum will be 1, the expected total is 3, and 3 - 1 = 2.
* **Single element lists:** The formula cleanly handles the smallest constraint boundary where N = 1 (e.g., input list `[0]` or `[1]`).
* **All numbers unique:** The problem constraints guarantee all numbers are unique, meaning the math formula for a continuous sequence from 0 to N will always match up perfectly without duplicate interference.
