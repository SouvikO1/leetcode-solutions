![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.85%25)-yellow?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of colored balls. They are mixed up, but each ball is one of three colors: red, white, or blue. 

Instead of actual colors, the computer represents them as numbers:
- 0 means red
- 1 means white
- 2 means blue

Your job is to rearrange (sort) these balls right inside the original list (in-place) so that all the 0s come first, all the 1s come in the middle, and all the 2s go to the end. The final order must strictly be all 0s, then all 1s, then all 2s. You are not allowed to use any built-in sorting functions provided by the programming language.

## Intuition

If you use a standard sorting algorithm, you might compare numbers back and forth or use extra memory, which takes too long or uses too much space. But here, we only have three distinct values: 0, 1, and 2. 

The "aha" moment is using **three pointers** (tracking positions with index variables) to partition the array into four sections all at once in a single pass:
1. Everything before pointer `s` is definitely 0.
2. Everything after pointer `e` is definitely 2.
3. Everything between pointer `s` and pointer `i` is definitely 1.
4. Everything between `i` and `e` is unexplored territory.

As our main reader pointer `i` walks through the array, it inspects numbers one by one. If it sees a 0, it sends it to the left side (`s`). If it sees a 2, it sends it to the right side (`e`). If it sees a 1, it simply skips past it because 1s belong safely in the middle. By the time `i` passes `e`, the entire array is neatly sorted.

## Approach

Here is how the code executes step-by-step:

- `int n= nums.size();`: Finds the total number of elements in the array and stores it in `n`.
- `int s=0 , e= n-1;`: Initializes the start pointer `s` at the very beginning of the array (index 0) and the end pointer `e` at the very end of the array (index `n - 1`).
- `int i=0;`: Initializes our scanner pointer `i` at the beginning of the array (index 0) to look at elements one by one.
- `while( i<=e){`: Starts a loop that keeps running as long as our scanner pointer `i` has not crossed past the end pointer `e`.
- `if( nums[i]==0){`: Checks if the element currently pointed to by `i` is a 0 (red).
- `swap( nums[i] , nums[s]);`: Swaps the current element with the element sitting at the start pointer `s` to push the 0 toward the front.
- `i++;s++;`: Advances both the scanner pointer `i` and the start pointer `s` forward by 1 because we know the newly swapped element at `s` is sorted.
- `else if( nums[i]==2){`: Checks if the element currently pointed to by `i` is a 2 (blue) instead.
- `swap( nums[i] , nums[e]);`: Swaps the current element with the element sitting at the end pointer `e` to push the 2 toward the back.
- `e--;`: Moves the end pointer `e` backward by 1. Notice that `i` does *not* increase here, because the number we just swapped from the end into position `i` hasn't been checked yet!
- `else{`: Handles the remaining case where the current element is neither 0 nor 2 (meaning it must be a 1, white).
- `i++;`: Leaves the 1 in place and just moves the scanner pointer `i` forward by 1 to check the next element.

## Dry Run

### Case 1: Typical case (Example 1)
Input: `nums = [2, 0, 2, 1, 1, 0]`

| Step | `i` | `s` | `e` | `nums[i]` | Action | Array State (`nums`) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Initial | 0 | 0 | 5 | 2 | Value is 2, swap with `e` (index 5) | `[0, 0, 2, 1, 1, 2]` |
| 1 | 0 | 0 | 4 | 0 | Value is 0, swap with `s` (index 0), increment `i` and `s` | `[0, 0, 2, 1, 1, 2]` |
| 2 | 1 | 1 | 4 | 0 | Value is 0, swap with `s` (index 1), increment `i` and `s` | `[0, 0, 2, 1, 1, 2]` |
| 3 | 2 | 2 | 4 | 2 | Value is 2, swap with `e` (index 4), decrement `e` | `[0, 0, 1, 1, 2, 2]` |
| 4 | 2 | 2 | 3 | 1 | Value is 1, just increment `i` | `[0, 0, 1, 1, 2, 2]` |
| 5 | 3 | 2 | 3 | 1 | Value is 1, just increment `i` | `[0, 0, 1, 1, 2, 2]` |
| 6 | 4 | 2 | 3 | 2 | Loop terminates because `i` (4) is greater than `e` (3) | `[0, 0, 1, 1, 2, 2]` |

### Case 2: Short array with mixed values (Example 2)
Input: `nums = [2, 0, 1]`

| Step | `i` | `s` | `e` | `nums[i]` | Action | Array State (`nums`) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Initial | 0 | 0 | 2 | 2 | Value is 2, swap with `e` (index 2), decrement `e` | `[1, 0, 2]` |
| 1 | 0 | 0 | 1 | 1 | Value is 1, just increment `i` | `[1, 0, 2]` |
| 2 | 1 | 0 | 1 | 0 | Value is 0, swap with `s` (index 0), increment `i` and `s` | `[0, 1, 2]` |
| 3 | 2 | 1 | 1 | 2 | Loop terminates because `i` (2) is greater than `e` (1) | `[0, 1, 2]` |

## Time & Space Complexity

- **Time:** O(n) — The scanner pointer `i` starts at 0 and moves forward one step at a time until it meets the end pointer `e`. Every single element in the array is looked at and processed at most once. Therefore, the time it takes grows directly in proportion to the number of elements `n`.
- **Space:** O(1) — We sort the array completely in-place. We only created a few extra integer variables (`s`, `e`, `i`, `n`) to keep track of indices, which takes a fixed, tiny amount of memory no matter how large the array gets.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already fully optimal. 
- **Time-wise:** To sort any array, you inherently must examine every single element at least once. That sets a hard speed limit of O(n). Since our solution visits every element in a single pass, it hits the absolute fastest theoretical time limit possible.
- **Space-wise:** Because the problem demands that we sort the array in-place without using extra data structures like auxiliary arrays, O(1) constant space is the absolute best we can achieve.

## Edge Cases Handled

- **Already sorted array (e.g., `[0, 0, 1, 1, 2, 2]`):** The pointers move smoothly across the array, skipping 1s and leaving 0s and 2s in their correct places without doing unnecessary disruptions.
- **Reverse sorted array (e.g., `[2, 2, 1, 1, 0, 0]`):** The 2s are correctly swept to the back and the 0s are swept to the front via swaps.
- **Array with only one type of color (e.g., `[1, 1, 1]`):** The pointer `i` simply walks through all elements, finds 1s, increments past them, and finishes safely without errors.
- **Minimum constraint size (`n = 1`):** With only one element, `i = 0` and `e = 0`. The loop runs for one check, sees that `i <= e` is true, handles the single element, and terminates cleanly without going out of bounds.
