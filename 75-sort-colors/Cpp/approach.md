![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.85%25)-yellow?style=for-the-badge)

---

## Problem Explained

The problem asks us to sort an array called `nums` that contains only three values: `0`, `1`, and `2`. These numbers represent colors:
* `0` represents red
* `1` represents white
* `2` represents blue

We must rearrange the array in-place so that all `0`s come first, followed by all `1`s, and finally all `2`s. "In-place" means we must modify the existing array directly without creating a new array. We are also not allowed to use built-in sort functions.

For example, if the input is `nums = [2, 0, 2, 1, 1, 0]`, the sorted output should be `[0, 0, 1, 1, 2, 2]`.

## Intuition

This problem is a classic algorithm puzzle known as the **Dutch National Flag** problem (invented by Edsger Dijkstra). 

Since there are only three unique values (`0`, `1`, and `2`), we can divide the array into three sections:
* A **left section** for all `0`s.
* A **middle section** for all `1`s.
* A **right section** for all `2`s.

We use three pointers:
1. `s` (start): track where the next `0` should be placed.
2. `e` (end): track where the next `2` should be placed.
3. `i` (current): scan through the array from left to right.

As `i` moves through the array:
* If we see a `0`, we swap it with the position `s` (moving it to the left section) and advance both `s` and `i`.
* If we see a `2`, we swap it with position `e` (moving it to the right section) and shrink the right section by moving `e` backward. Crucially, we **do not** advance `i` immediately, because the value swapped from `e` into index `i` hasn't been checked yet.
* If we see a `1`, it is already in the correct middle position, so we just move `i` forward.

## Approach

* `int n= nums.size();`: Calculates the length of the array so we know where the array ends.
* `int s=0 , e= n-1;`: Sets pointer `s` to the beginning (index `0`) to mark the boundary for `0`s, and pointer `e` to the last index (`n - 1`) to mark the boundary for `2`s.
* `int i=0;`: Initializes pointer `i` at index `0` to iterate through the array.
* `while( i<=e)`: Runs the loop as long as `i` has not passed pointer `e`. Any index beyond `e` is already filled with settled `2`s.
* `if( nums[i]==0)`: Checks if the current element is `0`.
* `swap( nums[i] , nums[s]);`: Swaps the `0` at `i` with whatever is at `s`, placing the `0` into the left red section.
* `i++;s++;`: Advances `s` forward (expanding the `0` section) and advances `i` to look at the next element.
* `else if( nums[i]==2)`: Checks if the current element is `2`.
* `swap( nums[i] , nums[e]);`: Swaps the `2` at `i` with whatever is at `e`, placing the `2` into the right blue section.
* `e--;`: Moves `e` backward to shrink the unknown section. Notice `i` is not incremented here because the newly swapped value brought to index `i` must still be evaluated.
* `else`: Handles the remaining case where `nums[i]` is `1`.
* `i++;`: Leaves the `1` in place (in the white section) and moves `i` to the next position.

## Dry Run

### Case 1: `nums = [2, 0, 2, 1, 1, 0]`

Initial state: `n = 6`, `s = 0`, `e = 5`, `i = 0`

| Step | `i` | `s` | `e` | `nums` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 0 | 5 | `[2, 0, 2, 1, 1, 0]` | `nums[0]` is 2. Swap `nums[0]` with `nums[5]`, decrement `e`. |
| 1 | 0 | 0 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[0]` is 0. Swap `nums[0]` with `nums[0]`, increment `s` and `i`. |
| 2 | 1 | 1 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[1]` is 0. Swap `nums[1]` with `nums[1]`, increment `s` and `i`. |
| 3 | 2 | 2 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[2]` is 2. Swap `nums[2]` with `nums[4]`, decrement `e`. |
| 4 | 2 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[2]` is 1. Increment `i`. |
| 5 | 3 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[3]` is 1. Increment `i`. |
| End | 4 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | Loop terminates because `i (4) > e (3)`. |

### Case 2: `nums = [2, 0, 1]`

Initial state: `n = 3`, `s = 0`, `e = 2`, `i = 0`

| Step | `i` | `s` | `e` | `nums` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 0 | 2 | `[2, 0, 1]` | `nums[0]` is 2. Swap `nums[0]` with `nums[2]`, decrement `e`. |
| 1 | 0 | 0 | 1 | `[1, 0, 2]` | `nums[0]` is 1. Increment `i`. |
| 2 | 1 | 0 | 1 | `[1, 0, 2]` | `nums[1]` is 0. Swap `nums[1]` with `nums[0]`, increment `s` and `i`. |
| End | 2 | 1 | 1 | `[0, 1, 2]` | Loop terminates because `i (2) > e (1)`. |

## Time & Space Complexity

* **Time Complexity:** **O(n)** — We iterate through the array using the `i` pointer at most `n` times. Every step either advances `i` or decreases `e`. Thus, the algorithm makes a single pass over the input array.
* **Space Complexity:** **O(1)** — We only use three integer variables (`s`, `e`, `i`) to keep track of indices. The array is modified directly in-place, requiring no extra memory allocations.

**Is this optimal?**
Yes, this code is already optimal. 

To sort an unsorted array, any algorithm must look at each element at least once, establishing a theoretical minimum time complexity of **O(n)**. Furthermore, since the array must be modified in-place, the theoretical minimum extra space complexity is **O(1)**. Since this algorithm achieves both **O(n)** time and **O(1)** space in a single pass, no further asymptotic improvements are possible.

## Edge Cases Handled

* **Single Element Array (`nums = [0]`):** `s = 0`, `e = 0`, `i = 0`. The loop runs once, sees the value, does not crash, and exits cleanly.
* **Already Sorted Array (`nums = [0, 1, 2]`):** Pointer `i` moves smoothly through without making unnecessary swaps that distort order.
* **Reverse Sorted Array (`nums = [2, 1, 0]`):** Swaps elements into opposite ends, correctly placing `2` at the end and `0` at the front.
* **Array with Only One Distinct Color (`nums = [1, 1, 1]` or `[0, 0, 0]`):** Handles arrays where missing values (like no `2`s or no `0`s) occur without index out-of-bounds errors.
* **Array with Duplicate Numbers Only (`nums = [2, 2, 0, 0]`):** Correctly moves all boundary pointers until all duplicate values gather at their respective ends.
