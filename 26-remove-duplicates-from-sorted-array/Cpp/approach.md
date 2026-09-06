![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.89%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given a list of integers called `nums` that is already sorted from smallest to largest. Some numbers might appear more than once.

Your task is to remove all duplicate numbers so that each unique number appears only once. You must do this **in-place**, which means you cannot create a new array or copy of the list. You must modify the existing array directly.

After removing duplicates:
- The unique numbers must stay at the very front of the array in their original sorted order.
- You must return `k`, which is the total count of unique numbers.
- Anything left in the array beyond the first `k` elements does not matter.

**Example:**
If `nums = [1, 1, 2]`, there are 2 unique numbers: `1` and `2`. You update `nums` so its first two spots are `[1, 2]`, and return `k = 2`.

---

## Intuition

Because the array is already **sorted**, all duplicate values sit right next to each other. 

We can solve this using the **Two Pointers** technique. Imagine two readers pointing at the list:
1. **Slow pointer (`i`)**: Keeps track of where the last known unique element is stored.
2. **Fast pointer (`j`)**: Scans ahead through the array to discover new, unseen numbers.

Whenever the fast pointer `j` finds a number that is different from the number at `i`, we know we found a brand new unique value! We then increment `i` to move to the next available spot and place the new value there.

---

## Approach

Here is step-by-step how the code executes the two-pointer idea:

- `int i=0,j=1;`: We initialize `i` at index 0 because the very first element is always unique. We initialize `j` at index 1 to start searching for the next unique element.
- `while( j< nums.size())`: We start a loop that runs until `j` reaches the end of the array.
- `if( nums[i] != nums[j])`: We check if the element at `j` is different from the element at `i`.
- `swap( nums[++i],nums[j++]);`: If they are different, we increment `i` first (`++i`) to target the next spot, swap the new value at `j` into that spot, and then increment `j` (`j++`) to keep scanning forward.
- `else`: If `nums[i]` and `nums[j]` are equal, `nums[j]` is a duplicate.
- `j++;`: We skip the duplicate by moving `j` one index forward without moving `i`.
- `return i+1;`: When the loop finishes, `i` is the index of the last unique element. Since indices start at 0, the total count of unique elements `k` is `i + 1`.

---

## Dry Run

### Example 1: `nums = [1, 1, 2]`

| Step | `i` | `j` | `nums[i]` | `nums[j]` | Action | `nums` state |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 1 | 1 | 1 | Elements match (`nums[0] == nums[1]`). Skip duplicate. | `[1, 1, 2]` |
| 1 | 0 | 2 | 1 | 2 | Different (`1 != 2`). Increment `i` to 1, swap `nums[1]` & `nums[2]`, increment `j` to 3. | `[1, 2, 1]` |
| End | 1 | 3 | - | - | Loop terminates (`j == 3`). Return `i + 1 = 2`. | `[1, 2, 1]` |

---

### Example 2: `nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`

| Step | `i` | `j` | `nums[i]` | `nums[j]` | Action | `nums` state |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 1 | 0 | 0 | Match. `j++`. | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 0 | 2 | 0 | 1 | Different. Increment `i` to 1, swap `nums[1]` & `nums[2]`, `j++`. | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 2 | 1 | 3 | 1 | 1 | Match. `j++`. | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 3 | 1 | 4 | 1 | 1 | Match. `j++`. | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 4 | 1 | 5 | 1 | 2 | Different. Increment `i` to 2, swap `nums[2]` & `nums[5]`, `j++`. | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 5 | 2 | 6 | 2 | 2 | Match. `j++`. | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 6 | 2 | 7 | 2 | 3 | Different. Increment `i` to 3, swap `nums[3]` & `nums[7]`, `j++`. | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` |
| 7 | 3 | 8 | 3 | 3 | Match. `j++`. | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` |
| 8 | 3 | 9 | 3 | 4 | Different. Increment `i` to 4, swap `nums[4]` & `nums[9]`, `j++`. | `[0, 1, 2, 3, 4, 0, 2, 1, 3, 1]` |
| End | 4 | 10 | - | - | Loop terminates (`j == 10`). Return `i + 1 = 5`. | `[0, 1, 2, 3, 4, ...]` |

---

## Time & Space Complexity

- **Time Complexity:** **O(N)** — where N is the length of the `nums` array. The fast pointer `j` travels through the array from start to end exactly once.
- **Space Complexity:** **O(1)** — Memory usage is constant because we modify the input array directly and only store two integer pointers (`i` and `j`).

### Is this optimal?
**Yes, this is optimal.**
- **Time:** We must examine every element at least once to determine if it is a duplicate, so lower than O(N) time is impossible.
- **Space:** The problem requires modifying the array in-place, so O(1) extra space is the absolute best possible limit.

---

## Edge Cases Handled

- **Array with length 1 (e.g., `nums = [7]`):** The loop condition `j < nums.size()` (1 < 1) evaluates to false immediately. The function returns `i + 1 = 1`, which is correct.
- **Array with all identical values (e.g., `nums = [2, 2, 2, 2]`):** The condition `nums[i] != nums[j]` is never met. Pointer `j` moves to the end while `i` stays at 0. The function returns `1`, leaving `2` as the only unique element.
- **Array with no duplicates (e.g., `nums = [1, 2, 3, 4]`):** Every step finds a different number. Pointer `i` advances on every iteration, and the function returns the full array length.
- **Negative numbers (e.g., `nums = [-10, -10, -3, 0, 5]`):** The equality checks (`!=`) compare integer values directly, so negative numbers work smoothly without any special handling.
