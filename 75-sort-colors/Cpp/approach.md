![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.75%25)-yellow?style=for-the-badge)

---

## Problem Explained

The problem asks us to sort an array called `nums` containing only three numbers: 0, 1, and 2. 

These numbers represent colors:
* **0** represents Red
* **1** represents White
* **2** represents Blue

We must rearrange the array in-place so that all 0s come first, followed by all 1s, and then all 2s at the end. Rearranging **in-place** means we must modify the given array directly without creating a new array. We are also not allowed to use standard built-in sorting functions.

**Example:**
* **Input:** `nums = [2, 0, 2, 1, 1, 0]`
* **Output:** `[0, 0, 1, 1, 2, 2]`

---

## Intuition

Since there are only three distinct values (0, 1, and 2), we do not need a full sorting algorithm like Merge Sort or Quick Sort. Instead, we can divide the array into three regions:

1. A **left region** for all 0s.
2. A **middle region** for all 1s.
3. A **right region** for all 2s.

This is famously known as the **Dutch National Flag algorithm**. We use three pointers to maintain these boundaries:
* `s` (start) marks where the next 0 should go on the left.
* `e` (end) marks where the next 2 should go on the right.
* `i` scans elements from left to right.

When `i` encounters a 0, we swap it to the `s` region. When `i` encounters a 2, we swap it to the `e` region. When `i` encounters a 1, we leave it in place because 1s belong in the middle.

---

## Approach

Here is how the logic works step-by-step:

* `int n= nums.size();`: Stores the total number of elements in `nums`.
* `int s=0 , e= n-1;`: Sets `s` at index 0 to place 0s at the start, and `e` at the last index (`n - 1`) to place 2s at the end.
* `int i=0;`: Sets the scanning pointer `i` to index 0 to begin examining elements.
* `while( i<=e)`: Continues looping as long as `i` has not passed `e`. We stop at `e` because elements past `e` are already correctly placed 2s.
* `if( nums[i]==0)`: Checks if the current element at index `i` is 0.
* `swap( nums[i] , nums[s]);`: Swaps the 0 at `i` with whatever is at index `s`, putting the 0 into the left region.
* `i++;s++;`: Moves `s` right because the spot for 0 is filled. Moves `i` right because the value swapped into `i` is guaranteed to be in the correct relative place.
* `else if( nums[i]==2)`: Checks if the current element at index `i` is 2.
* `swap( nums[i] , nums[e]);`: Swaps the 2 at `i` with the element at index `e`, putting the 2 into the right region.
* `e--;`: Moves `e` left because a 2 was placed. Note that `i` is **not** incremented here because the newly swapped element coming from `e` has not been checked yet.
* `else`: Triggers when `nums[i]` is 1.
* `i++;`: Leaves the 1 in place and advances `i` to inspect the next element.

---

## Dry Run

### Case 1: Typical case with mixed colors
**Input:** `nums = [2, 0, 2, 1, 1, 0]`

| Step | `i` | `s` | `e` | Array `nums` | Action |
|---|---|---|---|---|---|
| Start | 0 | 0 | 5 | `[2, 0, 2, 1, 1, 0]` | Initial state |
| 1 | 0 | 0 | 5 | `[0, 0, 2, 1, 1, 2]` | `nums[0] == 2`: Swap `nums[0]` with `nums[5]`, `e` becomes 4 |
| 2 | 0 | 0 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[0] == 0`: Swap `nums[0]` with `nums[0]`, `s` becomes 1, `i` becomes 1 |
| 3 | 1 | 1 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[1] == 0`: Swap `nums[1]` with `nums[1]`, `s` becomes 2, `i` becomes 2 |
| 4 | 2 | 2 | 4 | `[0, 0, 1, 1, 2, 2]` | `nums[2] == 2`: Swap `nums[2]` with `nums[4]`, `e` becomes 3 |
| 5 | 2 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[2] == 1`: Leave in place, `i` becomes 3 |
| 6 | 3 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[3] == 1`: Leave in place, `i` becomes 4 |

Loop ends because `i` (4) is now greater than `e` (3). Final array is `[0, 0, 1, 1, 2, 2]`.

### Case 2: Short array with three unique elements
**Input:** `nums = [2, 0, 1]`

| Step | `i` | `s` | `e` | Array `nums` | Action |
|---|---|---|---|---|---|
| Start | 0 | 0 | 2 | `[2, 0, 1]` | Initial state |
| 1 | 0 | 0 | 2 | `[1, 0, 2]` | `nums[0] == 2`: Swap `nums[0]` with `nums[2]`, `e` becomes 1 |
| 2 | 0 | 0 | 1 | `[1, 0, 2]` | `nums[0] == 1`: Leave in place, `i` becomes 1 |
| 3 | 1 | 0 | 1 | `[0, 1, 2]` | `nums[1] == 0`: Swap `nums[1]` with `nums[0]`, `s` becomes 1, `i` becomes 2 |

Loop ends because `i` (2) is now greater than `e` (1). Final array is `[0, 1, 2]`.

---

## Time & Space Complexity

* **Time Complexity:** **O(n)** — We traverse the array in a single pass. In every iteration of the `while` loop, either `i` increases by 1 or `e` decreases by 1. Therefore, the loop runs at most `n` times, where `n` is the number of elements in `nums`.
* **Space Complexity:** **O(1)** — We modify the input array in-place using only three integer variable pointers (`s`, `e`, `i`). No extra dynamic memory is allocated.

**Is this solution optimal?**
Yes, this code is already fully optimal. Any algorithm sorting an array must examine each element at least once, which requires a minimum of **O(n)** time. Rearranging elements in-place requires a minimum of **O(1)** space. Because this solution achieves **O(n)** time in a single pass and **O(1)** space, no further time or space improvements are possible.

---

## Edge Cases Handled

* **Single Element Array (`nums = [0]`):** The loop condition `i <= e` starts with `i = 0` and `e = 0`. It evaluates the element once and terminates cleanly without out-of-bounds access.
* **Array with Only One Color (`nums = [1, 1, 1]`):** Pointer `i` simply increments across the array without making unnecessary swaps.
* **Already Sorted Array (`nums = [0, 1, 2]`):** The algorithm processes each boundary condition without altering the sorted order.
* **Reverse Sorted Array (`nums = [2, 2, 0, 0]`):** Correctly places 2s at the right and swaps 0s to the left without infinite loops.
* **Missing Colors (`nums = [0, 2, 0]`):** Works seamlessly even when one of the colors (like 1) is completely absent from the array.
