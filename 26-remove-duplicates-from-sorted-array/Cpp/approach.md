![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.92%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a line of playing cards sorted from smallest to largest. Some of those cards are exact duplicates. The goal is to clean up the line so that every unique number appears only once, while keeping the original sorted order intact.

Because this has to happen **in-place** (meaning you are modifying the original array directly in computer memory without making a fresh copy), there is a catch. You cannot just shrink the array's physical size. Instead, you must shift all the unique elements to the front of the array. 

Whatever leftover trash values or duplicate numbers are left at the end of the array do not matter. The system only cares about two things:
1. The first **k** elements of your array must contain your cleaned-up, unique list in sorted order.
2. Your function must return **k**, which is the total count of those unique elements.

For example, if your input is `[1, 1, 2]`, you should rearrange it so the first two slots hold `1` and `2`, and your function returns `2`.

---

## Intuition

The "aha" moment for this problem comes from a common pattern called **Two Pointers**. 

Because the input array is already **sorted**, all identical numbers are guaranteed to sit right next to each other. That means we do not need to look ahead through the whole array every time we see a number. We just need to compare adjacent elements.

We use two markers, or pointers:
* Pointer `i` marks the last known position of a truly unique element.
* Pointer `j` acts as our scout, scanning ahead through the array to find the next fresh, non-duplicate number.

Whenever our scout (`j`) finds a number that is different from our last unique spot (`i`), it means we found a new unique value! We increment `i` to make space, and swap the new unique value into that spot. By the time our scout reaches the end of the array, all unique elements have been neatly packed into the front of the array, bounded by index `i`.

---

## Approach

Here is how the code executes step-by-step:

* `int i=0,j=1;`: Initializes our two pointers. Pointer `i` starts at the very first element (index `0`), and pointer `j` starts right next to it at index `1` to begin scanning.
* `while( j< nums.size()){`: Opens a loop that keeps our scout pointer `j` moving forward as long as it stays within the bounds of the array size.
* `if( nums[i] != nums[j]){`: Compares the element at our unique marker `i` with the element at our scout pointer `j` to see if they are different numbers.
* `swap( nums[++i],nums[j++]);`: Triggers when `j` finds a new, unique number. It increments `i` first so we move to the next open slot, and then swaps the value at the new `i` with the value at `j`, advancing `j` forward in the same breath.
* `else{ j++; }`: Triggers when `nums[i]` and `nums[j]` are equal (meaning `j` has stumbled onto another duplicate). It leaves `i` alone and just moves `j` forward to look for the next candidate.
* `return i+1;`: Exits the loop once `j` hits the end of the array. It returns `i + 1` because `i` is a zero-based index, meaning a total count requires adding `1`.

---

## Dry Run

### Case 1: Typical case with duplicates
Input: `nums = [1, 1, 2]`

| Step | i | j | nums[i] | nums[j] | Action | Array State |
| :--- | :-: | :-: | :-: | :-: | :--- | :--- |
| Start | 0 | 1 | 1 | 1 | `nums[0] == nums[j]`, so it's a duplicate. | `[1, 1, 2]` |
| Loop 1 (Else) | 0 | 2 | 1 | 2 | `j` increments. Now `j = 2`. | `[1, 1, 2]` |
| Loop 2 (If) | 1 | 2 | 1 | 2 | `nums[0] != nums[2]` (`1 != 2`). Swap `++i` (index 1) with `j` (index 2). `i` becomes 1, `j` becomes 3. | `[1, 2, 1]` |
| End | 1 | 3 | - | - | `j` reaches size 3, loop ends. Returns `i + 1` = `2`. | `[1, 2, 1]` |

---

### Case 2: Array with many sequential duplicates
Input: `nums = [0, 0, 1, 1, 2]`

| Step | i | j | nums[i] | nums[j] | Action | Array State |
| :--- | :-: | :-: | :-: | :-: | :--- | :--- |
| Start | 0 | 1 | 0 | 0 | `nums[0] == nums[1]`. Duplicate. | `[0, 0, 1, 1, 2]` |
| Step 1 (Else) | 0 | 2 | 0 | 1 | `j` increments to 2. | `[0, 0, 1, 1, 2]` |
| Step 2 (If) | 1 | 2 | 0 | 1 | `nums[0] != nums[2]`. Swap `++i` (index 1) with `j` (index 2). `i` becomes 1, `j` becomes 3. | `[0, 1, 0, 1, 2]` |
| Step 3 (Else) | 1 | 3 | 1 | 1 | `nums[1] == nums[3]`. Duplicate. `j` increments to 4. | `[0, 1, 0, 1, 2]` |
| Step 4 (If) | 2 | 4 | 1 | 2 | `nums[1] != nums[4]`. Swap `++i` (index 2) with `j` (index 4). `i` becomes 2, `j` becomes 5. | `[0, 1, 2, 1, 0]` |
| End | 2 | 5 | - | - | `j` reaches size 5, loop ends. Returns `i + 1` = `3`. | `[0, 1, 2, 1, 0]` |

---

## Time & Space Complexity

* **Time:** O(N) — where N is the number of elements in the array. The scout pointer `j` travels across the array exactly once from start to finish.
* **Space:** O(1) — constant space. We are rearranging the elements right inside the original array and only tracking two integer variables (`i` and `j`), regardless of how large the input array gets.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this solution is **already completely optimal**. 
* You cannot achieve a better time complexity than O(N) because you must look at every element in the array at least once to know whether it is a duplicate. 
* You cannot achieve a better space complexity than O(1) because the problem specifically requires modifying the array in-place without allocating extra storage data structures.

---

## Edge Cases Handled

* **Minimum size array (`nums.length = 1`):** The loop condition `j < nums.size()` evaluates to `1 < 1`, which is false. The loop is skipped entirely, and the function immediately returns `0 + 1 = 1`, which is correct.
* **All elements are duplicates (`[1, 1, 1, 1]`):** The scout pointer `j` will march through every element, hitting the `else` branch every time. `i` will stay at `0`, and the function will correctly return `1`.
* **No duplicates at all (`[1, 2, 3, 4]`):** Every step will hit the `if` branch, swapping elements with themselves (or adjacent slots) and incrementing both `i` and `j` in lockstep until the end. It returns the full array length.
* **Negative numbers (`[-3, -1, -1, 0, 2]`):** Because the solution relies purely on numerical inequality checks (`!=`), negative numbers, zero, and positive numbers are handled seamlessly.
