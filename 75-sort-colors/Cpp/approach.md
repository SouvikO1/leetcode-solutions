![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.85%25)-yellow?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of colored blocks. Each block is painted one of three colors: red, white, or blue. 

Instead of words, the problem uses numbers to represent these colors:
- **0** stands for red
- **1** stands for white
- **2** stands for blue

Right now, the blocks are mixed up in random order inside a list (an array). Your job is to rearrange them so that all the red blocks (0s) come first, all the white blocks (1s) come in the middle, and all the blue blocks (2s) go to the end. 

You must do this **in-place**, which means you rearrange the blocks directly inside the original list without creating a second list. You also cannot use any built-in sort functions. 

For example, if you start with the list `[2, 0, 2, 1, 1, 0]`, you need to change it so it ends up as `[0, 0, 1, 1, 2, 2]`.

---

## Intuition

If you think about sorting, a naive way is to look at every number, count how many 0s, 1s, and 2s you have, and then overwrite the list. But that takes two passes through the data. Can we do it in a single pass?

The "aha" moment comes from using **three pointers** (markers that track positions in the list) instead of just one or two. 

Think of it like sorting laundry into three piles on a table:
- A pointer called `s` marks where the next **red (0)** block should land. It starts at the very beginning of the list (`0`).
- A pointer called `e` marks where the next **blue (2)** block should land. It starts at the very end of the list (`n - 1`).
- A pointer called `i` is our scout, walking through the list from left to right, inspecting each block.

As our scout `i` looks at blocks, it swaps 0s over to the left side (where `s` is pointing) and swaps 2s over to the right side (where `e` is pointing). When `i` encounters a 1, it simply leaves it in the middle and moves on. By the time the scout finishes walking across the list, everything is cleanly partitioned into three sections without needing a second look.

---

## Approach

Here is how the code executes step-by-step:

- `int n= nums.size();` — Finds the total number of items in the list and stores it in `n`.
- `int s=0 , e= n-1;` — Creates the left boundary pointer `s` at index `0` for 0s, and the right boundary pointer `e` at index `n - 1` for 2s.
- `int i=0;` — Creates the scout pointer `i` at index `0` to scan through the list.
- `while( i<=e){` — Starts a loop that keeps running as long as the scout pointer `i` has not crossed or passed the right boundary pointer `e`.
- `if( nums[i]==0){` — Checks if the current block being looked at by the scout is a red block (0).
- `swap( nums[i] , nums[s]);` — Swaps the current block with whatever is sitting at the left boundary pointer `s`.
- `i++;s++;` — Moves both the scout pointer `i` and the left boundary pointer `s` one step to the right, because we know that position now holds a correctly placed 0.
- `else if( nums[i]==2){` — Checks if the current block being looked at by the scout is a blue block (2).
- `swap( nums[i] , nums[e]);` — Swaps the current block with whatever is sitting at the right boundary pointer `e`.
- `e--;` — Moves the right boundary pointer `e` one step to the left, because that position now holds a correctly placed 2. Note that we do **not** move the scout `i` yet, because the number we just swapped from the end needs to be inspected.
- `else{` — Handles the remaining case where the current block is a white block (1).
- `i++;` — Simply moves the scout pointer `i` forward by one, leaving the 1 safely in the middle.

---

## Dry Run

### Case 1: Typical case with mixed colors
**Input:** `nums = [2, 0, 2, 1, 1, 0]` (`n = 6`)

| `i` | `s` | `e` | `nums` state | Action |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 0 | 5 | `[2, 0, 2, 1, 1, 0]` | `nums[i]` is 2. Swap with `nums[e]`. Decrement `e`. |
| 0 | 0 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[i]` is 0. Swap with `nums[s]`. Increment `i` and `s`. |
| 1 | 1 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[i]` is 0. Swap with `nums[s]`. Increment `i` and `s`. |
| 2 | 2 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[i]` is 2. Swap with `nums[e]`. Decrement `e`. |
| 2 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[i]` is 1. Just increment `i`. |
| 3 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[i]` is 1. Just increment `i`. |
| 4 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | Loop ends because `i` (4) is greater than `e` (3). |

### Case 2: Short array with three elements
**Input:** `nums = [2, 0, 1]` (`n = 3`)

| `i` | `s` | `e` | `nums` state | Action |
| :--- | :--- | :--- | :--- | :--- |
| 0 | 0 | 2 | `[2, 0, 1]` | `nums[i]` is 2. Swap with `nums[e]`. Decrement `e`. |
| 0 | 0 | 1 | `[1, 0, 2]` | `nums[i]` is 1. Just increment `i`. |
| 1 | 0 | 1 | `[1, 0, 2]` | `nums[i]` is 0. Swap with `nums[s]`. Increment `i` and `s`. |
| 2 | 1 | 1 | `[0, 1, 2]` | Loop ends because `i` (2) is greater than `e` (1). |

---

## Time & Space Complexity

- **Time:** `O(n)` — The scout pointer `i` starts at the beginning and moves forward one step at a time until it meets the `e` pointer. Every element is visited at most once, taking linear time proportional to the size of the array `n`.
- **Space:** `O(1)` — We only created a few integer variables (`n`, `s`, `e`, `i`) to keep track of indices. No extra arrays or data structures were used, meaning it uses constant extra space.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already optimal. 

- **Time complexity:** To sort an array, you generally have to look at every element at least once, which takes `O(n)` time. Since our algorithm processes every element in a single pass, it cannot get any faster than `O(n)`.
- **Space complexity:** Because we rearrange the elements directly inside the input array, we use `O(1)` extra memory, which is the absolute minimum possible.

---

## Edge Cases Handled

- **Already sorted array (e.g., `[0, 0, 1, 1, 2, 2]`):** The pointers move smoothly across without making unnecessary disruptive swaps.
- **Reverse sorted array (e.g., `[2, 2, 1, 1, 0, 0]`):** The 2s are correctly pushed to the back and 0s to the front in a single pass.
- **Minimum constraint size (`n = 1`):** The loop condition `i <= e` (`0 <= 0`) runs once, sees a single element, and correctly terminates immediately without error.
- **Arrays missing one color (e.g., `[1, 1, 0, 0]`):** The missing color's pointer logic simply doesn't trigger, leaving the remaining colors correctly ordered.
