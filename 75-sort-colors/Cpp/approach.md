![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-11.7%20MB%20(beats%2048.70%25)-yellow?style=for-the-badge)

---

## Problem Explained

The problem asks you to reorder an array named `nums` containing numbers that represent colors:
- `0` represents red
- `1` represents white
- `2` represents blue

You need to sort these numbers in-place so that all `0`s come first, followed by all `1`s, and then all `2`s. 

"In-place" means you must rearrange the original array directly without creating a new copy of the array. You are also explicitly forbidden from using language built-in sort functions.

For example, if you are given `nums = [2, 0, 2, 1, 1, 0]`, the correct result is `[0, 0, 1, 1, 2, 2]`.

---

## Intuition

This problem is a classic puzzle known as the **Dutch National Flag problem**.

Because there are only three unique values (`0`, `1`, and `2`), we do not need a standard sorting algorithm like Quicksort or Bubble Sort. Instead, we can divide the array into three sections using three pointers:
1. A **start pointer** (`s`) to keep track of where the next `0` should go at the front.
2. An **end pointer** (`e`) to keep track of where the next `2` should go at the back.
3. A **current pointer** (`i`) to scan through the elements one by one.

The "aha" moment comes from how we handle each number as we scan:
- If we see a `0`, we swap it to the left boundary (`s`) and step both `s` and `i` forward.
- If we see a `2`, we swap it to the right boundary (`e`) and step `e` backward. **Crucially, we do not move `i` forward yet**, because the element swapped from the back has not been inspected yet.
- If we see a `1`, it is already in its middle area, so we just step `i` forward.

---

## Approach

Here is the exact step-by-step logic of the code:

- `int n= nums.size();`: Stores the total number of elements in the `nums` array.
- `int s=0 , e= n-1;`: Sets up two boundary pointers. `s` starts at index `0` (left side) for zeros, and `e` starts at index `n - 1` (right side) for twos.
- `int i=0;`: Initializes the scanner pointer `i` at the first element (index `0`).
- `while( i<=e)`: Continues the loop as long as the current scanner `i` has not moved past the right boundary `e`.
- `if( nums[i]==0)`: Checks if the current element is `0`.
- `swap( nums[i] , nums[s]); i++;s++;`: Swaps the current `0` into the left boundary position `s`. Then increments both `s` and `i` by `1` because we know position `s` now holds a `0`, and the value brought to position `i` is already known to be clean.
- `else if( nums[i]==2)`: Checks if the current element is `2`.
- `swap( nums[i] , nums[e]); e--;`: Swaps the current `2` into the right boundary position `e` and decrements `e` by `1`. Notice that `i` is **not** incremented here, because the element swapped from position `e` into position `i` has not been evaluated yet.
- `else`: Runs when the current element is `1`.
- `i++;`: Leaves the `1` in place in the middle section and moves the scanner pointer `i` one position forward.

---

## Dry Run

### Case 1: Typical case with all three colors (`nums = [2, 0, 2, 1, 1, 0]`)

Initial values: `s = 0`, `e = 5`, `i = 0`

| `i` | `s` | `e` | `nums` | Action |
| --- | --- | --- | --- | --- |
| 0 | 0 | 5 | `[2, 0, 2, 1, 1, 0]` | `nums[0]` is `2`. Swap `nums[0]` and `nums[5]`. Decrement `e` to `4`. |
| 0 | 0 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[0]` is `0`. Swap `nums[0]` and `nums[0]`. Increment `i` to `1`, `s` to `1`. |
| 1 | 1 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[1]` is `0`. Swap `nums[1]` and `nums[1]`. Increment `i` to `2`, `s` to `2`. |
| 2 | 2 | 4 | `[0, 0, 2, 1, 1, 2]` | `nums[2]` is `2`. Swap `nums[2]` and `nums[4]`. Decrement `e` to `3`. |
| 2 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[2]` is `1`. Increment `i` to `3`. |
| 3 | 2 | 3 | `[0, 0, 1, 1, 2, 2]` | `nums[3]` is `1`. Increment `i` to `4`. |

Loop ends because `i (4) > e (3)`. Final sorted array: `[0, 0, 1, 1, 2, 2]`.

---

### Case 2: Small array (`nums = [2, 0, 1]`)

Initial values: `s = 0`, `e = 2`, `i = 0`

| `i` | `s` | `e` | `nums` | Action |
| --- | --- | --- | --- | --- |
| 0 | 0 | 2 | `[2, 0, 1]` | `nums[0]` is `2`. Swap `nums[0]` and `nums[2]`. Decrement `e` to `1`. |
| 0 | 0 | 1 | `[1, 0, 2]` | `nums[0]` is `1`. Increment `i` to `1`. |
| 1 | 0 | 1 | `[1, 0, 2]` | `nums[1]` is `0`. Swap `nums[1]` and `nums[0]`. Increment `i` to `2`, `s` to `1`. |

Loop ends because `i (2) > e (1)`. Final sorted array: `[0, 1, 2]`.

---

## Time & Space Complexity

- **Time Complexity:** O(n) — The algorithm makes a single pass over the array of size `n`. In each loop step, either `i` moves right or `e` moves left, meaning we process each element at most once.
- **Space Complexity:** O(1) — The solution operates directly on the input vector and only uses three integer variables (`s`, `e`, `i`) to keep track of state, consuming constant extra memory.

**Is this already the most optimal possible complexity?**

Yes, this solution is fully optimal:
- **Time:** You must look at every element at least once to determine its color, making O(n) the lower bound for time complexity.
- **Space:** O(1) is the absolute minimum auxiliary space since no extra data structures are allocated.
- **Passes:** It answers the follow-up question by sorting the entire array in a **single pass**. No further performance improvements are possible.

---

## Edge Cases Handled

- **Single Element Array (`n = 1`):** For input like `nums = [0]`, `s` starts at `0`, `e` starts at `0`, `i` starts at `0`. The loop runs once, correctly leaves the element untouched, and terminates safely.
- **Array Already Sorted:** Inputs like `nums = [0, 1, 2]` work seamlessly. `0` swaps with itself, `1` advances `i`, and `2` swaps with itself, maintaining correct order.
- **Array with Only One Color:** Inputs containing all zeros (`[0, 0, 0]`), all ones (`[1, 1, 1]`), or all twos (`[2, 2, 2]`) execute without going out of bounds because `i <= e` constantly prevents out-of-range memory access.
- **Array with Missing Colors:** Inputs containing only two of the three colors (for example, only `0`s and `2`s like `[2, 0, 2, 0]`) are handled cleanly without needing special conditional checks.
