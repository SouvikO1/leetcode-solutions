![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.89%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given an array of integers named `nums` that is already sorted in order from smallest to largest. Some of the numbers in this list appear more than once.

Your task is to modify the array **in-place** (meaning you must alter the original array directly without creating a new copy) so that every unique number appears only once at the beginning of the array. The original sorted order of these unique numbers must be preserved.

After placing all unique numbers at the front, you return `k`, which is the total count of unique numbers. Anything in the array located after index `k - 1` does not matter and can be ignored.

For example, if `nums = [1, 1, 2]`:
* The unique values are `1` and `2`.
* You rearrange `nums` so the first two elements are `[1, 2]`.
* You return `k = 2`.

---

## Intuition

Because the list is already sorted, identical numbers are always grouped right next to each other. You do not need to look across the whole array to find duplicates; you only need to look at adjacent values.

To solve this in one pass, you use two markers called **pointers**:
* **Slow pointer (`i`):** Marks the location of the last unique value you have locked in place.
* **Fast pointer (`j`):** Scans forward through the list looking for the next unique number.

Whenever the fast pointer `j` finds a value that is different from the unique value at `i`, you move `i` forward one spot and place the newly found value there. If `j` sees a duplicate value, it simply skips over it.

---

## Approach

Here is how the code executes step-by-step:

* `int i=0,j=1;` — Initialize pointer `i` at index `0` (the first element is always unique) and pointer `j` at index `1` (to start scanning for new values).
* `while( j< nums.size())` — Start a loop that runs until `j` reaches the end of the array.
* `if( nums[i] != nums[j])` — Compare the last confirmed unique value at `nums[i]` with the current scanned value at `nums[j]`.
* `swap( nums[++i],nums[j++]);` — If the values are different, pre-increment `i` (`++i`) to move to the next empty unique slot, swap the new value at index `j` into index `i`, and post-increment `j` (`j++`) to continue scanning.
* `else` — If `nums[i]` is equal to `nums[j]`, the value at `j` is a duplicate.
* `j++;` — Increment `j` by `1` to skip the duplicate without moving `i`.
* `return i+1;` — Once the loop finishes, return `i + 1`. Since `i` is a zero-based index, `i + 1` represents the count `k` of unique elements found.

---

## Dry Run

### Case 1: Short array with one duplicate (`nums = [1, 1, 2]`)

| `i` | `j` | `nums` | Action |
|---|---|---|---|
| 0 | 1 | `[1, 1, 2]` | `nums[0]` (1) equals `nums[1]` (1). Duplicate found. Increment `j` to 2. |
| 0 | 2 | `[1, 1, 2]` | `nums[0]` (1) != `nums[2]` (2). New unique found! `++i` becomes 1. Swap `nums[1]` and `nums[2]`. `j` becomes 3. |
| 1 | 3 | `[1, 2, 1]` | `j` reached `nums.size()` (3). Loop ends. Return `i + 1` = 2. |

---

### Case 2: Longer array with multiple duplicates (`nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`)

| `i` | `j` | `nums` | Action |
|---|---|---|---|
| 0 | 1 | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` | `nums[0]` (0) == `nums[1]` (0). Duplicate. Increment `j` to 2. |
| 0 | 2 | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` | `nums[0]` (0) != `nums[2]` (1). Unique found! Swap `nums[1]` and `nums[2]`. `i` becomes 1, `j` becomes 3. |
| 1 | 3 | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` | `nums[1]` (1) == `nums[3]` (1). Duplicate. Increment `j` to 4. |
| 1 | 4 | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` | `nums[1]` (1) == `nums[4]` (1). Duplicate. Increment `j` to 5. |
| 1 | 5 | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` | `nums[1]` (1) != `nums[5]` (2). Unique found! Swap `nums[2]` and `nums[5]`. `i` becomes 2, `j` becomes 6. |
| 2 | 6 | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` | `nums[2]` (2) == `nums[6]` (2). Duplicate. Increment `j` to 7. |
| 2 | 7 | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` | `nums[2]` (2) != `nums[7]` (3). Unique found! Swap `nums[3]` and `nums[7]`. `i` becomes 3, `j` becomes 8. |
| 3 | 8 | `[0, 1, 2, 3, 1, 0, 2, 2, 3, 4]` | `nums[3]` (3) == `nums[8]` (3). Duplicate. Increment `j` to 9. |
| 3 | 9 | `[0, 1, 2, 3, 1, 0, 2, 2, 3, 4]` | `nums[3]` (3) != `nums[9]` (4). Unique found! Swap `nums[4]` and `nums[9]`. `i` becomes 4, `j` becomes 10. |
| 4 | 10 | `[0, 1, 2, 3, 4, 0, 2, 2, 3, 1]` | `j` reached `nums.size()` (10). Loop ends. Return `i + 1` = 5. |

---

## Time & Space Complexity

* **Time Complexity:** **O(N)** — where N is the length of the `nums` array. Pointer `j` moves forward by 1 step in every iteration and inspects each element exactly once.
* **Space Complexity:** **O(1)** — Memory usage is constant because modifications are performed in-place using only two integer pointers (`i` and `j`).

### Can it be improved?

The time complexity **O(N)** and space complexity **O(1)** are already theoretically optimal. We must look at every number at least once to identify duplicates, and modifying the array in-place uses minimum possible space.

However, a small micro-optimization can be made to improve real-world CPU speed. Instead of swapping values (`swap(nums[++i], nums[j++])`), we can directly overwrite the value (`nums[++i] = nums[j++]`). Swapping performs three memory operations (read, write, write), while direct assignment performs only two (read, write).

```cpp
if (nums[i] != nums[j]) {
    nums[++i] = nums[j++];
}
```

* **Resulting Complexity:** **O(N)** Time, **O(1)** Space.
* **Theoretical Best Possible:** **O(N)** Time, **O(1)** Space. The current approach already achieves this theoretical limit.

---

## Edge Cases Handled

* **Single Element Array (`nums = [1]`):** The loop condition `j < nums.size()` evaluates to `1 < 1` (false), skipping the loop entirely. It correctly returns `i + 1 = 1`.
* **No Duplicates (`nums = [1, 2, 3]`):** Pointer `j` steps through every index, updating `i` on every step. The array remains unchanged and returns `3`.
* **All Duplicate Elements (`nums = [1, 1, 1, 1]`):** Pointer `j` scans to the end without ever updating `i`. The function correctly returns `1`.
* **Negative Numbers (`nums = [-10, -10, -5, 0]`):** Relational check `nums[i] != nums[j]` handles signed integer values naturally without issues.
