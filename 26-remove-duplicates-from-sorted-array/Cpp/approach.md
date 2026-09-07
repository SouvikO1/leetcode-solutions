![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.92%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbers arranged from smallest to largest. Some of those numbers appear more than once. The problem asks you to clean up this list **in-place**, meaning you must rearrange the numbers inside the original array itself without creating a second array. 

When you finish, the front of the array must contain every unique number, listed in their original sorted order. Any extra space left over at the end of the array doesn't matter. Finally, you need to return a single number `k`, which represents how many unique numbers you found.

For example, if you start with the array `[1, 1, 2]`, your code should rearrange it so the first two slots hold `1` and `2`. It then returns `k = 2`.

## Intuition

The "aha" moment for this problem comes from a strategy called **Two Pointers**. 

Because the input array is already sorted, all identical numbers are guaranteed to sit right next to each other. Instead of searching the whole array over and over, we can walk through it using two markers (pointers):
- One pointer, `i`, marks the boundary of our clean, unique list so far.
- The second pointer, `j`, scouts ahead to find new numbers.

Whenever the scout (`j`) finds a number that is different from our last unique number (`i`), it means we found a fresh value. We increment `i` to make space, and then we copy the new value into that spot. By the time `j` reaches the end of the array, all unique values have been neatly packed into the front of the array.

## Approach

Here is how the code executes step-by-step:

- ``int i=0,j=1;``: Initializes two integer pointers. `i` starts at index `0` holding our first unique element, and `j` starts at index `1` to act as our scout.
- ``while( j< nums.size())``: Starts a loop that keeps running as long as the scout pointer `j` has not reached the end of the array.
- ``if( nums[i] != nums[j])``: Checks if the number at the scout pointer is different from the number at our unique boundary pointer.
- ``swap( nums[++i],nums[j++]);``: If the numbers are different, this moves our boundary `i` forward by one, moves scout `j` forward by one, and swaps the new unique number into place.
- ``else{ j++; }``: If the numbers are the same, it means we found a duplicate. We leave `i` where it is and only advance scout `j` to look for the next distinct number.
- ``return i+1;``: Once the loop finishes, `i` is at the index of our last unique element. Since indices start at `0`, we return `i + 1` to represent the total count of unique elements.

## Dry Run

### Case 1: Typical case with multiple duplicates
Input: `nums = [0, 0, 1, 1, 2]`

| Step | i | j | nums[i] | nums[j] | Action |
| --- | --- | --- | --- | --- | --- |
| Start | 0 | 1 | 0 | 0 | Loop starts; `nums[0]` equals `nums[1]` (both are 0) |
| 1 | 0 | 2 | 0 | 1 | `nums[0]` does not equal `nums[2]`; increment `i` to 1, swap `nums[1]` and `nums[2]`, increment `j` to 3 |
| 2 | 1 | 3 | 1 | 1 | `nums[1]` equals `nums[3]` (both are 1); increment `j` to 4 |
| 3 | 1 | 4 | 1 | 2 | `nums[1]` does not equal `nums[4]`; increment `i` to 2, swap `nums[2]` and `nums[4]`, increment `j` to 5 |
| End | 2 | 5 | - | - | `j` reaches array size; loop ends. Returns `i + 1`, which is 3 |

### Case 2: Array with no duplicates
Input: `nums = [1, 2, 3]`

| Step | i | j | nums[i] | nums[j] | Action |
| --- | --- | --- | --- | --- | --- |
| Start | 0 | 1 | 1 | 2 | Loop starts; `nums[0]` does not equal `nums[1]` |
| 1 | 0 | 1 | 1 | 2 | Increment `i` to 1, swap `nums[1]` with itself, increment `j` to 2 |
| 2 | 1 | 2 | 2 | 3 | `nums[1]` does not equal `nums[2]`; increment `i` to 2, swap `nums[2]` with itself, increment `j` to 3 |
| End | 2 | 3 | - | - | `j` reaches array size; loop ends. Returns `i + 1`, which is 3 |

## Time & Space Complexity

- **Time:** O(n) — The scout pointer `j` walks through the array of size n exactly once from left to right. Every check, swap, and increment takes constant time.
- **Space:** O(n) — We modify the array in-place using a few integer variables (`i` and `j`), requiring no extra data structures.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

This solution is already optimal. 

- **Can time complexity be improved?** No. Any solution must inspect every element in the array at least once to determine if it is a duplicate, which inherently requires O(n) time.
- **Can space complexity be improved?** No. We are already using O(1) auxiliary space (constant extra memory beyond the input array itself).

No further improvement is possible.

## Edge Cases Handled

- **Single-element array (length 1):** The loop condition `j < nums.size()` evaluates to false immediately because `j` starts at 1 and `nums.size()` is 1. The code safely skips the loop and returns `0 + 1 = 1`.
- **All elements are duplicates:** The `else` branch triggers on every iteration, advancing `j` while `i` stays put at `0`. The function correctly returns `1` at the end.
- **No duplicates exist:** Every comparison triggers the `if` branch, causing `i` and `j` to march forward together step-by-step, swapping elements with themselves and returning the full array size.
- **Negative numbers:** Because the algorithm relies only on relative equality checks (`nums[i] != nums[j]`) rather than absolute values, negative numbers and mixed positive-negative sorted arrays are handled correctly.
