![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.67%25)-yellow?style=for-the-badge)

---

## Problem Explained

You are given an array of numbers called `nums`. Every number in this array is either a `0`, `1`, or `2`. These numbers represent colors:
* **0** represents Red
* **1** represents White
* **2** represents Blue

Your goal is to sort the array in-place so that all `0`s come first, followed by all `1`s, and finally all `2`s. 

"In-place" means you must rearrange the existing array directly without creating a new array. You are also explicitly forbidden from using built-in sorting functions.

For example, if you start with `[2, 0, 1]`, your function should modify it to become `[0, 1, 2]`.

---

## Intuition

This problem is a classic puzzle known as the **Dutch National Flag algorithm**. 

Imagine dividing the array into three sections:
1. **Left section:** Holds all the `0`s (Red).
2. **Middle section:** Holds all the `1`s (White).
3. **Right section:** Holds all the `2`s (Blue).

To organize the array in a single pass, we use three pointers:
* `s` (start pointer): Marks where the next `0` should go.
* `e` (end pointer): Marks where the next `2` should go.
* `i` (current pointer): Scans through the array from left to right.

As `i` moves across the array:
* If it sees a **0**, it swaps it to the left side (`s`) and moves both `s` and `i` forward.
* If it sees a **2**, it swaps it to the right side (`e`) and moves `e` backward. Crucially, we **do not** move `i` forward yet because the item swapped from `e` back to position `i` hasn't been checked yet!
* If it sees a **1**, it leaves it in place and simply moves `i` forward, allowing `1`s to naturally gather in the middle.

---

## Approach

Here is how the algorithm works step-by-step:

* `int n= nums.size();`: Stores the size of the array in `n`.
* `int s=0 , e= n-1;`: Initializes pointer `s` at index 0 (where `0`s belong) and pointer `e` at the last index (where `2`s belong).
* `int i=0;`: Sets our scanning pointer `i` at the beginning of the array.
* `while( i<=e)`: Keeps processing elements until `i` passes `e`. Anything past `e` is already a confirmed `2`.
* `if( nums[i]==0)`: Checks if the current element is `0`.
* `swap( nums[i] , nums[s]); i++;s++;`: Swaps the found `0` into the left region at index `s`. Increments `s` to expand the `0` region and increments `i` to examine the next element.
* `else if( nums[i]==2)`: Checks if the current element is `2`.
* `swap( nums[i] , nums[e]); e--;`: Swaps the found `2` into the right region at index `e`. Decrements `e` to shrink the unsorted region from the right. We do **not** increment `i` here because the newly swapped value at index `i` needs to be inspected on the next iteration.
* `else`: Catches the remaining case where `nums[i]` is `1`.
* `i++;`: Since `1`s belong in the middle, no swap is needed. Just increment `i` to move forward.

---

## Dry Run

### Case 1: `nums = [2, 0, 2, 1, 1, 0]`

| Step | `i` | `s` | `e` | Current `nums` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 0 | 5 | `[2, 0, 2, 1, 1, 0]` | Initial setup. |
| 1 | 0 | 0 | 5 | `[2, 0, 2, 1, 1, 0]` | `nums[0] == 2`. Swap `nums[0]` with `nums[5]`. Decrement `e` to 4. |
| 2 | 0 | 0 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[0] == 0`. Swap `nums[0]` with `nums[0]`. Increment `i` to 1, `s` to 1. |
| 3 | 1 | 1 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[1] == 0`. Swap `nums[1]` with `nums[1]`. Increment `i` to 2, `s` to 2. |
| 4 | 2 | 2 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[2] == 2`. Swap `nums[2]` with `nums[4]`. Decrement `e` to 3. |
| 5 | 2 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[2] == 1`. Increment `i` to 3. |
| 6 | 3 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[3] == 1`. Increment `i` to 4. |
| End | 4 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | Loop terminates because `i > e` (4 > 3). |

### Case 2: `nums = [2, 0, 1]`

| Step | `i` | `s` | `e` | Current `nums` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 0 | 2 | `[2, 0, 1]` | Initial setup. |
| 1 | 0 | 0 | 2 | `[2, 0, 1]` | `nums[0] == 2`. Swap `nums[0]` with `nums[2]`. Decrement `e` to 1. |
| 2 | 0 | 0 | 1 | `[1, 0, 2]` | `nums[0] == 1`. Increment `i` to 1. |
| 3 | 1 | 0 | 1 | `[1, 0, 2]` | `nums[1] == 0`. Swap `nums[1]` with `nums[0]`. Increment `i` to 2, `s` to 1. |
| End | 2 | 1 | 1 | `[0, 1, 2]` | Loop terminates because `i > e` (2 > 1). |

---

## Time & Space Complexity

* **Time Complexity:** **O(n)** — We traverse the array of length `n` in a single pass. Each element is inspected and placed in its correct section at most once or twice.
* **Space Complexity:** **O(1)** — We only use a constant amount of extra memory for three integer variables (`s`, `e`, and `i`), modifying the input vector directly.

**Is this optimal?**
Yes, this code is already optimal. Any algorithm sorting an unorganized list of elements must look at each element at least once, making **O(n)** the theoretical minimum time complexity. Using **O(1)** extra space is also the minimum possible memory consumption since no additional arrays are allocated.

---

## Edge Cases Handled

* **Single-element array (e.g., `[0]`):** `s` and `e` both equal `0`. The loop runs once, identifies the element, does no bad swaps, and terminates immediately.
* **Array with only one color present (e.g., `[0, 0, 0]` or `[2, 2, 2]`):** For all `0`s, `s` and `i` move together without breaking logic. For all `2`s, `e` decrements on every step until `i > e`, safely avoiding index out-of-bounds errors.
* **Already sorted input (e.g., `[0, 1, 2]`):** Elements stay in their original positions while pointers adjust cleanly.
* **Reverse sorted input (e.g., `[2, 1, 0]`):** The algorithm safely swaps extreme elements into their designated partitions without infinite loops.
