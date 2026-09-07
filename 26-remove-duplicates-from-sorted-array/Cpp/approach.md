![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.92%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

You are given a list of integers called `nums` that is already sorted from smallest to largest. Some numbers in the list appear more than once.

Your goal is to modify the list directly so that every unique number appears only once at the beginning of the list, keeping their original sorted order. You must return `k`, which is the total count of these unique numbers.

You are required to do this **in-place**, meaning you modify the original list directly without allocating extra space for a second array. Whatever values remain in the list after the first `k` positions do not matter.

For example:
* Input: `nums = [1, 1, 2]`
* Output: `k = 2`, with `nums = [1, 2, _]`

## Intuition

Since the list is already sorted, identical numbers sit right next to each other.

To separate duplicates from unique values, we use two trackers (often called **pointers**):
1. Tracker `i` stays at the index of the last confirmed unique value.
2. Tracker `j` scans forward through the array to look for new values.

Whenever tracker `j` spots a value different from `nums[i]`, we move `i` forward by one slot and put that new value there. If tracker `j` sees a duplicate, it simply moves past it. This effectively shifts all unique numbers to the front of the array.

## Approach

* `int i=0,j=1;` — Start `i` at index 0 (pointing to the first element, which is always unique) and `j` at index 1 (the next element to inspect).
* `while( j< nums.size()){` — Loop through the array until tracker `j` reaches the end.
* `if( nums[i] != nums[j]){` — Compare the value at index `j` to our last unique value at index `i` to check if we found a new number.
* `swap( nums[++i],nums[j++]);` — If the numbers do not match, advance `i` by 1 to open up the next unique position (`++i`), swap the new number into `nums[i]`, and then advance `j` by 1 (`j++`).
* `else{ j++; }` — If the numbers match, `nums[j]` is a duplicate. Advance `j` by 1 to keep looking without moving `i`.
* `return i+1;` — Since array indexes start at 0, index `i` represents the position of the last unique element. Therefore, `i + 1` is the total count of unique elements.

## Dry Run

### Case 1: Standard input with a single duplicate (`nums = [1, 1, 2]`)

| Step | `i` | `j` | `nums[i]` | `nums[j]` | Current `nums` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 1 | 1 | 1 | `[1, 1, 2]` | Initialize pointers |
| 1 | 0 | 1 | 1 | 1 | `[1, 1, 2]` | Match found (`nums[0] == nums[1]`). Increment `j` to 2. |
| 2 | 0 | 2 | 1 | 2 | `[1, 2, 1]` | Difference found (`nums[0] != nums[2]`). Increment `i` to 1, swap `nums[1]` and `nums[2]`, increment `j` to 3. |
| End | 1 | 3 | - | - | `[1, 2, 1]` | Loop stops (`j == 3`). Return `i + 1 = 2`. |

### Case 2: Longer input with multiple duplicates (`nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`)

| Step | `i` | `j` | `nums[i]` | `nums[j]` | Current `nums` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| Start | 0 | 1 | 0 | 0 | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` | Initialize pointers |
| 1 | 0 | 1 | 0 | 0 | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` | Match found. Increment `j` to 2. |
| 2 | 0 | 2 | 0 | 1 | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` | Difference found. Increment `i` to 1, swap `nums[1]` and `nums[2]`, increment `j` to 3. |
| 3 | 1 | 3 | 1 | 1 | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` | Match found. Increment `j` to 4. |
| 4 | 1 | 4 | 1 | 1 | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` | Match found. Increment `j` to 5. |
| 5 | 1 | 5 | 1 | 2 | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` | Difference found. Increment `i` to 2, swap `nums[2]` and `nums[5]`, increment `j` to 6. |
| 6 | 2 | 6 | 2 | 2 | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` | Match found. Increment `j` to 7. |
| 7 | 2 | 7 | 2 | 3 | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` | Difference found. Increment `i` to 3, swap `nums[3]` and `nums[7]`, increment `j` to 8. |
| 8 | 3 | 8 | 3 | 3 | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` | Match found. Increment `j` to 9. |
| 9 | 3 | 9 | 3 | 4 | `[0, 1, 2, 3, 4, 0, 2, 1, 3, 1]` | Difference found. Increment `i` to 4, swap `nums[4]` and `nums[9]`, increment `j` to 10. |
| End | 4 | 10 | - | - | `[0, 1, 2, 3, 4, ...]` | Loop stops (`j == 10`). Return `i + 1 = 5`. |

## Time & Space Complexity

**Time Complexity:** O(N) — where N is the length of `nums`. The `j` pointer moves through the array from left to right exactly once.

**Space Complexity:** O(1) — memory remains constant because we modify the original vector in-place without creating auxiliary data structures.

**Is this optimal?**
Yes, this code is already optimal. 
* To detect duplicates in an unsorted or sorted array, you must look at every element at least once, requiring at least O(N) time.
* Modifying the input in-place without extra storage uses O(1) extra space.

No algorithm can improve upon O(N) time and O(1) space for this problem.

*(Note: While swapping elements works, using plain assignment `nums[++i] = nums[j++]` instead of `swap(...)` avoids unnecessary write operations back to duplicate slots. However, Big-O complexity remains identical).*

## Edge Cases Handled

* **Single-element array (`nums = [1]`):** The `while` loop condition (`j < nums.size()`) evaluates to false immediately because `j = 1` is not less than 1. The code safely returns `i + 1 = 1`.
* **Array with all duplicate values (`nums = [2, 2, 2]`):** Pointer `i` remains at index 0 while pointer `j` scans all the way to the end. The function returns `1`, leaving `nums[0] = 2`.
* **Array with no duplicates (`nums = [1, 2, 3, 4]`):** At every step, `nums[i] != nums[j]` triggers. Pointer `i` advances steadily right alongside pointer `j`, returning the original array length.
* **Negative numbers (`nums = [-5, -5, -2, 0]`):** Equality and inequality checks function identically regardless of whether values are positive or negative.
