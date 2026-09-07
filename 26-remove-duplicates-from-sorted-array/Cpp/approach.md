![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.92%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbers, and someone arranged them neatly from smallest to largest. Some numbers show up more than once. The goal is to clean up this list *in-place* — meaning you rearrange the existing list directly without making a second copy of it. 

You need to shift all the unique numbers to the very front of the list, keeping them in their original sorted order. After you do that, you must return how many unique numbers you found (let us call this count **k**). Whatever random leftover numbers happen to sit at the end of the list past index **k - 1** do not matter; the system will only check the first **k** spots.

For example, if you start with `[1, 1, 2]`, you want to modify it so the front part becomes `[1, 2]`, and your function returns `2`.

---

## Intuition

The core trick here is using **two pointers** (variables that keep track of positions in the array) to scan through the list at the same time. 

Because the array is already sorted, any duplicate numbers will always sit right next to each other. We can set up one pointer, **i**, to mark the last known unique number we have safely locked in at the front. We set up a second pointer, **j**, to scout ahead and explore the rest of the array. 

When **j** finds a brand new number that is different from what **i** is pointing at, we know we have found a fresh unique value. We increment **i** to open up a new slot, and then we swap or copy that fresh value into **i**'s spot. If **j** sees a duplicate, it just marches forward alone. By the time **j** reaches the end of the array, all unique numbers have been neatly packed at the front up to index **i**, and **i + 1** gives us our total count **k**.

---

## Approach

Here is how the code builds this logic step-by-step:

- `int i=0,j=1;`: We start **i** at index `0` because the very first element in a sorted array is always unique by default. We start **j** at index `1` to begin scanning right next to it.
- `while( j< nums.size()){`: We run a loop as long as our scouting pointer **j** stays inside the bounds of the array.
- `if( nums[i] != nums[j]){`: We compare the element at our unique-tracker **i** with the element at our scout **j**. If they are different, it means the scout has found a new unique number.
- `swap( nums[++i],nums[j++]);`: Because we found a new unique number, we first move **i** one step forward (`++i`) to point to the next available empty slot, and then we swap that new unique value from **j** into **i**. We also advance **j** (`j++`) so it keeps scouting.
- `else{ j++; }`: If the numbers match, it means **j** has found a duplicate. We leave **i** alone and only advance **j** (`j++`) to skip past it.
- `return i+1;`: Once the loop finishes scanning the whole array, **i** sits at the index of the last unique element. Since indices start at zero, we add `1` to **i** to return the true total count of unique elements.

---

## Dry Run

### Case 1: Typical case with multiple duplicates
Input: `nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`

| i | j | nums[i] | nums[j] | Action | Array State (nums) |
|---|---|---------|---------|--------|--------------------|
| 0 | 1 | 0 | 0 | `nums[i] == nums[j]`, increment **j** | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` |
| 0 | 2 | 0 | 1 | `nums[i] != nums[j]`, increment **i**, swap elements, increment **j** | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 3 | 1 | 1 | `nums[i] == nums[j]`, increment **j** | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 4 | 1 | 1 | `nums[i] == nums[j]`, increment **j** | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 5 | 1 | 2 | `nums[i] != nums[j]`, increment **i**, swap elements, increment **j** | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 2 | 6 | 2 | 2 | `nums[i] == nums[j]`, increment **j** | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 2 | 7 | 2 | 3 | `nums[i] != nums[j]`, increment **i**, swap elements, increment **j** | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` |
| 3 | 8 | 3 | 3 | `nums[i] == nums[j]`, increment **j** | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` |
| 3 | 9 | 3 | 4 | `nums[i] != nums[j]`, increment **i**, swap elements, increment **j** | `[0, 1, 2, 3, 4, 0, 2, 1, 3, 1]` |

Loop ends because **j** reaches the end of the array. Returns `i + 1`, which is `4 + 1 = 5`. The first 5 elements are `[0, 1, 2, 3, 4]`.

### Case 2: Short array with simple duplicate
Input: `nums = [1, 1, 2]`

| i | j | nums[i] | nums[j] | Action | Array State (nums) |
|---|---|---------|---------|--------|--------------------|
| 0 | 1 | 1 | 1 | `nums[i] == nums[j]`, increment **j** | `[1, 1, 2]` |
| 0 | 2 | 1 | 2 | `nums[i] != nums[j]`, increment **i**, swap elements, increment **j** | `[1, 2, 1]` |

Loop ends because **j** reaches the end of the array. Returns `i + 1`, which is `1 + 1 = 2`. The first 2 elements are `[1, 2]`.

---

## Time & Space Complexity

- **Time:** O(n) — where **n** is the number of elements in the array. The scouting pointer **j** traverses the entire array exactly once from left to right, performing constant-time comparisons and swaps at each step.
- **Space:** O(1) — constant space. We only use two integer variables (**i** and **j**) for tracking positions, modifying the original array in-place without allocating any extra data structures.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already fully optimal. 

- **Time complexity:** You must look at every element in the array at least once to determine if it is a duplicate, which inherently requires O(n) time. You cannot go faster than linear time.
- **Space complexity:** The problem explicitly requires modifying the array in-place, meaning using O(1) extra memory is the gold standard. 

No further improvements in time or space are possible.

---

## Edge Cases Handled

- **Array with all unique elements (e.g., [1, 2, 3]):** The condition `nums[i] != nums[j]` is always true, so **i** and **j** march forward together step-by-step, leaving the array untouched and correctly returning the full length.
- **Array with all identical elements (e.g., [5, 5, 5, 5]):** The scout **j** finds matches all the way through, meaning **i** stays at `0` while **j** runs to the end. The function correctly returns `1`.
- **Minimum allowed array size (length of 1):** The loop condition `j < nums.size()` evaluates to `1 < 1`, which is false from the start. The loop is bypassed entirely, and it immediately returns `i + 1`, which evaluates to `0 + 1 = 1`. This is completely correct.
- **Negative numbers:** Because the algorithm relies entirely on relative comparisons (`!=`), it handles negative numbers, mixed positive/negative ranges, and zeros seamlessly.
