![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.85%25)-yellow?style=for-the-badge)

---

## Problem Explained

Imagine you have a messy line of colored objects. There are only three colors involved: red, white, and blue. Instead of words, the problem represents them with numbers:
- 0 means red
- 1 means white
- 2 means blue

You are given an array of these numbers, like `[2, 0, 2, 1, 1, 0]`. Your job is to sort them **in-place** (meaning you rearrange the items inside the existing list without making a copy of it) so that all the 0s come first, all the 1s come in the middle, and all the 2s go to the end. 

For example, if you start with `[2, 0, 2, 1, 1, 0]`, your final result must be `[0, 0, 1, 1, 2, 2]`. You cannot use any built-in sorting functions provided by the language; you have to figure out how to arrange them yourself.

## Intuition

The "aha" moment for this problem comes from treating it like a sorting game that you can solve by walking through the array just **once**. 

Instead of looking at the whole array multiple times or using a heavy sorting algorithm like Quicksort or Bubble Sort (which are too slow or use extra memory), we can use **three pointers** (markers that track positions in the array):
1. A pointer called `s` (for start) that keeps track of where the next 0 should go. It starts at the very beginning (index 0).
2. A pointer called `e` (for end) that keeps track of where the next 2 should go. It starts at the very end of the array.
3. A pointer called `i` (for iterator) that walks through the array from left to right, inspecting each element.

As `i` walks across the array, it checks what number it lands on:
- If it sees a **0**, it swaps that number with whatever is at the `s` pointer, then moves both `s` and `i` forward. This safely pushes 0s to the front.
- If it sees a **2**, it swaps that number with whatever is at the `e` pointer, then moves `e` backward. (Notice we don't move `i` forward yet, because the new number we just swapped from the end needs to be checked too!).
- If it sees a **1**, it’s already in the right neighborhood (the middle), so `i` just skips past it and moves forward.

By the time `i` passes `e`, everything is completely sorted.

## Approach

Here is the step-by-step breakdown of what the code does:

- `int n= nums.size();`: Finds the total number of items in the array and stores it in `n`.
- `int s=0 , e= n-1;`: Sets up the `s` pointer at the beginning (index 0) and the `e` pointer at the very end of the array (index `n - 1`).
- `int i=0;`: Sets up our moving pointer `i` at the beginning of the array to start inspecting elements.
- `while( i<=e){`: Starts a loop that keeps running as long as our moving pointer `i` hasn't crossed or met the end pointer `e`.
- `if( nums[i]==0){`: Checks if the element currently pointed to by `i` is a 0 (red).
- `swap( nums[i] , nums[s]);`: If it is a 0, swaps it with the element at the `s` pointer so the 0 moves toward the front.
- `i++;s++;`: Moves both the scanning pointer `i` and the start pointer `s` one step forward.
- `else if( nums[i]==2){`: Checks if the element currently pointed to by `i` is a 2 (blue).
- `swap( nums[i] , nums[e]);`: If it is a 2, swaps it with the element at the `e` pointer so the 2 moves toward the back.
- `e--;`: Moves the end pointer `e` one step backward. (We do not move `i` here because we need to inspect the value we just swapped into position `i`).
- `else{`: Handles the remaining case where the element is neither 0 nor 2 (meaning it must be a 1).
- `i++;`: Leaves the 1 in place and simply moves our scanning pointer `i` forward.

## Dry Run

### Case 1: Typical case with mixed colors
Input: `nums = [2, 0, 2, 1, 1, 0]`

| i | s | e | nums[i] | Action | Array State After Step |
|---|---|---|---------|--------|------------------------|
| 0 | 0 | 5 | 2 | Value is 2. Swap nums[0] and nums[5]. Decrement e. | `[0, 0, 2, 1, 1, 2]` |
| 0 | 0 | 4 | 0 | Value is 0. Swap nums[0] and nums[0]. Increment i and s. | `[0, 0, 2, 1, 1, 2]` |
| 1 | 1 | 4 | 0 | Value is 0. Swap nums[1] and nums[1]. Increment i and s. | `[0, 0, 2, 1, 1, 2]` |
| 2 | 2 | 4 | 2 | Value is 2. Swap nums[2] and nums[4]. Decrement e. | `[0, 0, 1, 1, 2, 2]` |
| 2 | 2 | 3 | 1 | Value is 1. Increment i. | `[0, 0, 1, 1, 2, 2]` |
| 3 | 2 | 3 | 1 | Value is 1. Increment i. | `[0, 0, 1, 1, 2, 2]` |
| 4 | 2 | 3 | 2 | Loop terminates because i (4) is greater than e (3). | `[0, 0, 1, 1, 2, 2]` |

### Case 2: Short array with three colors
Input: `nums = [2, 0, 1]`

| i | s | e | nums[i] | Action | Array State After Step |
|---|---|---|---------|--------|------------------------|
| 0 | 0 | 2 | 2 | Value is 2. Swap nums[0] and nums[2]. Decrement e. | `[1, 0, 2]` |
| 0 | 0 | 1 | 1 | Value is 1. Increment i. | `[1, 0, 2]` |
| 1 | 0 | 1 | 0 | Value is 0. Swap nums[1] and nums[0]. Increment i and s. | `[0, 1, 2]` |
| 2 | 1 | 1 | 2 | Loop terminates because i (2) is greater than e (1). | `[0, 1, 2]` |

## Time & Space Complexity

- **Time:** O(n) — The code uses a single loop where the pointer `i` moves from the left and `e` moves from the right. They meet in the middle after visiting each element in the array at most once, making the time scale linearly with the size of the array (`n`).
- **Space:** O(1) — The code only creates a few integer variables (`s`, `e`, `i`) to keep track of indices. It performs all swaps directly inside the existing array without using any extra data structures, meaning memory usage stays completely constant no matter how large the input array gets.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already fully optimal. 

- **Time complexity optimality:** To sort or categorize an array, you at least have to look at every element once. That takes O(n) time. Since this algorithm solves the problem in a single pass (O(n)), it cannot be made any faster.
- **Space complexity optimality:** The problem asks to sort the array **in-place**. Using O(1) auxiliary space means we are using zero extra storage, which is the absolute minimum possible. 

No further improvements can be made to the complexity.

## Edge Cases Handled

- **Already sorted array (e.g., `[0, 1, 2]`):** The pointers move smoothly across the array, see that items are already in their correct zones, and leave them untouched.
- **Reverse sorted array (e.g., `[2, 1, 0]`):** Effectively handled because 2s are continuously pushed to the back, 0s to the front, and 1s settle in the middle.
- **Array with only one type of element (e.g., `[1, 1, 1]`):** The pointer `i` simply walks through all elements, recognizes they are 1s, and increments without performing unnecessary swaps.
- **Minimum constraint size (`n = 1`):** When the array has only one element, `i = 0` and `e = 0`. The loop runs for one iteration (`i <= e`), sees the single element, performs no swaps, and exits safely without crashing.
