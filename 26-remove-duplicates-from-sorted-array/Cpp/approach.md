![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.90%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given a list of integers called `nums` that is already sorted from smallest to largest. Some numbers appear more than once. Your job is to remove the duplicates directly inside `nums` (**in-place**), so that each unique number appears only once at the beginning of the list.

You must keep the original sorted order of the unique numbers. After doing this, return `k`, which is the total count of unique numbers.

The numbers remaining after the first `k` positions in `nums` do not matter. 

**Example:**
* Input: `nums = [1, 1, 2]`
* Output: return `2`, and modify `nums` so its first 2 elements are `[1, 2]`.

---

## Intuition

Because `nums` is already sorted, all identical numbers are grouped right next to each other. 

Instead of building a brand-new list, we use two tracking positions (called **pointers**):
1. **Slow pointer (`i`):** Marks the index of the last confirmed unique number.
2. **Fast pointer (`j`):** Scans forward through the list looking for a new, different number.

Whenever `j` spots a number that is different from `nums[i]`, we know we found a new unique value. We step `i` forward by one position and place this new value there. If `j` sees a duplicate, it simply moves past it.

---

## Approach

Here is how the algorithm works line by line:

* `` `int i=0,j=1;` `` — Initialize the slow pointer `i` at index `0` (the first element is always unique) and the fast pointer `j` at index `1` to scan the rest of the array.
* `` `while( j< nums.size())` `` — Start a loop that runs until `j` reaches the end of the `nums` vector.
* `` `if( nums[i] != nums[j])` `` — Check if the current element at `j` is different from the last recorded unique element at `i`.
* `` `swap( nums[++i],nums[j++]);` `` — If a new unique element is found, move `i` forward by one (`++i`), swap the new unique value from position `j` into this new slot `i`, and move `j` forward to the next element (`j++`).
* `` `else` `` / `` `j++;` `` — If `nums[j]` is equal to `nums[i]`, it is a duplicate. Move `j` forward by one to skip it while keeping `i` where it is.
* `` `return i+1;` `` — Since array indices start at `0`, the total count of unique elements found is `i + 1`.

---

## Dry Run

### Case 1: Short array with duplicates (`nums = [1, 1, 2]`)

| `i` | `j` | `nums[i]` | `nums[j]` | Action | `nums` state |
|---|---|---|---|---|---|
| 0 | 1 | 1 | 1 | Duplicate (`1 == 1`). Increment `j`. | `[1, 1, 2]` |
| 0 | 2 | 1 | 2 | Unique (`1 != 2`). Increment `i` to 1, swap `nums[1]` and `nums[2]`, increment `j` to 3. | `[1, 2, 1]` |
| 1 | 3 | — | — | Loop ends (`j == nums.size()`). Return `i + 1 = 2`. | `[1, 2, 1]` |

---

### Case 2: Larger array with multiple duplicates (`nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`)

| `i` | `j` | `nums[i]` | `nums[j]` | Action | `nums` state |
|---|---|---|---|---|---|
| 0 | 1 | 0 | 0 | Duplicate (`0 == 0`). Increment `j`. | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` |
| 0 | 2 | 0 | 1 | Unique (`0 != 1`). Increment `i` to 1, swap `nums[1]` & `nums[2]`, increment `j` to 3. | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 3 | 1 | 1 | Duplicate (`1 == 1`). Increment `j`. | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 4 | 1 | 1 | Duplicate (`1 == 1`). Increment `j`. | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 5 | 1 | 2 | Unique (`1 != 2`). Increment `i` to 2, swap `nums[2]` & `nums[5]`, increment `j` to 6. | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 2 | 6 | 2 | 2 | Duplicate (`2 == 2`). Increment `j`. | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 2 | 7 | 2 | 3 | Unique (`2 != 3`). Increment `i` to 3, swap `nums[3]` & `nums[7]`, increment `j` to 8. | `[0, 1, 2, 3, 1, 0, 2, 0, 3, 4]` |
| 3 | 8 | 3 | 3 | Duplicate (`3 == 3`). Increment `j`. | `[0, 1, 2, 3, 1, 0, 2, 0, 3, 4]` |
| 3 | 9 | 3 | 4 | Unique (`3 != 4`). Increment `i` to 4, swap `nums[4]` & `nums[9]`, increment `j` to 10. | `[0, 1, 2, 3, 4, 0, 2, 0, 3, 1]` |
| 4 | 10 | — | — | Loop ends (`j == nums.size()`). Return `i + 1 = 5`. | `[0, 1, 2, 3, 4, ...]` |

---

## Time & Space Complexity

* **Time Complexity:** **O(N)** — where N is the length of `nums`. The pointer `j` starts at index 1 and moves right by 1 step in every single iteration of the loop. It touches each element in the array exactly once.
* **Space Complexity:** **O(1)** — no extra data structures are created. All operations are done directly inside the input vector `nums`.

**Is this optimal?**
Yes, this code is already at the optimal Big-O complexity. 
* We must inspect every element at least once to know if it is a duplicate, making **O(N)** the fastest possible time complexity.
* The problem forces an in-place solution, making **O(1)** memory optimal.

*Note on code efficiency:* Replacing `swap(nums[++i], nums[j++])` with direct assignment `nums[++i] = nums[j++]` avoids swapping values back and forth. The problem statement says values beyond index `k - 1` can be ignored, so overwriting is sufficient and slightly faster than swapping. However, both approaches share the exact same O(N) time and O(1) space bounds.

---

## Edge Cases Handled

* **Single element array (`nums.length == 1`):** `j` starts at 1, so the `while` loop condition `j < nums.size()` evaluates to false immediately. The function directly returns `i + 1 = 1`, which is correct.
* **No duplicates present (`nums = [1, 2, 3]`):** Every step finds `nums[i] != nums[j]`. The pointer `i` advances on every iteration alongside `j`, preserving all elements and returning the full array size.
* **All elements are duplicates (`nums = [5, 5, 5, 5]`):** `nums[i] != nums[j]` is never true. `j` scans all the way to the end without changing `i`. The function correctly returns `1`.
* **Negative numbers (`nums = [-10, -10, -5, 0]`):** The array is sorted, and the logic relies only on inequality checks (`!=`). Negative signs do not break the comparison logic.
