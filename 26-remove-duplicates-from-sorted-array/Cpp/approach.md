![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-22.5%20MB%20(beats%2097.90%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbered cards laid out on a table from left to right. The numbers are already sorted from smallest to largest, but some numbers appear more than once. Your job is to tidy up the row by squishing the duplicate numbers together so that every unique number appears only once, in its original sorted order. 

Because we must do this **in-place** (meaning we rearrange the original array directly without creating a second array), any extra space at the end of our row after we finish doesn't matter. The problem asks us to return a single number, let us call it **k**, which tells us how many unique elements we successfully kept. The first **k** elements of our array must hold those unique values.

For example, if you start with the array `[1, 1, 2]`, after removing duplicates you want your array to start with `[1, 2]` and return **k = 2**.

---

## Intuition

The "aha" moment for this problem comes from a technique called **Two Pointers**. 

Because the array is already sorted, any duplicate numbers are guaranteed to sit right next to each other. We can use two markers to walk through the array at the same time:
* One marker, let us call it **i**, points to the last known unique number we have safely locked in place.
* The other marker, let us call it **j**, scouts ahead to look for new, unique numbers.

When the scout (**j**) finds a number that is different from our locked number (**i**), it means we found a fresh unique value. We then advance **i** forward to the next slot and swap or copy the new unique value into place. By the time the scout finishes checking the whole array, **i** will sit right at the index of our last unique number, and **i + 1** will give us our total count **k**.

---

## Approach

Here is how the code executes step-by-step:

* `int i=0,j=1;` — Creates two integer pointers starting at the very beginning of the array. The slow pointer **i** starts at index 0, and the fast scout pointer **j** starts right beside it at index 1.
* `while( j< nums.size()){` — Starts a loop that keeps running as long as the scout pointer **j** has not reached the end of the array.
* `if( nums[i] != nums[j]){` — Checks if the element at the scout pointer is different from the element at our last locked unique pointer.
* `swap( nums[++i],nums[j++]);` — If the values are different, we increment **i** first to open up a fresh slot, and then we swap the new unique value from **j** into that slot. Both pointers then move forward.
* `else{` — Triggered when the scout finds a duplicate (meaning `nums[i]` equals `nums[j]`).
* `j++;` — Skips over the duplicate by advancing only the scout pointer **j** forward.
* `return i+1;` — Once the loop finishes, **i** represents the zero-based index of our last unique element, so adding 1 gives us the total count of unique elements, **k**.

---

## Dry Run

### Case 1: Typical case with multiple duplicates
**Input:** `nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`

| Step | i | j | nums[i] | nums[j] | Action | Array State (nums) |
| :--- | :-: | :-: | :-: | :-: | :--- | :--- |
| Start | 0 | 1 | 0 | 0 | Initialize pointers | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` |
| 1 | 0 | 1 | 0 | 0 | Equal, increment j only | `[0, 0, 1, 1, 1, 2, 2, 3, 3, 4]` |
| 2 | 0 | 2 | 0 | 1 | Different, increment i, swap | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 3 | 1 | 3 | 1 | 1 | Equal, increment j only | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 4 | 1 | 4 | 1 | 1 | Equal, increment j only | `[0, 1, 0, 1, 1, 2, 2, 3, 3, 4]` |
| 5 | 1 | 5 | 1 | 2 | Different, increment i, swap | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 6 | 2 | 6 | 2 | 2 | Equal, increment j only | `[0, 1, 2, 1, 1, 0, 2, 3, 3, 4]` |
| 7 | 2 | 7 | 2 | 3 | Different, increment i, swap | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` |
| 8 | 3 | 8 | 3 | 3 | Equal, increment j only | `[0, 1, 2, 3, 1, 0, 2, 1, 3, 4]` |
| 9 | 3 | 9 | 3 | 4 | Different, increment i, swap | `[0, 1, 2, 3, 4, 0, 2, 1, 3, 1]` |

*End of loop. Returns `i + 1`, which is `4 + 1 = 5`. The first 5 elements are `[0, 1, 2, 3, 4]`.*

### Case 2: Short array with single duplicate
**Input:** `nums = [1, 1, 2]`

| Step | i | j | nums[i] | nums[j] | Action | Array State (nums) |
| :--- | :-: | :-: | :-: | :-: | :--- | :--- |
| Start | 0 | 1 | 1 | 1 | Initialize pointers | `[1, 1, 2]` |
| 1 | 0 | 1 | 1 | 1 | Equal, increment j only | `[1, 1, 2]` |
| 2 | 0 | 2 | 1 | 2 | Different, increment i, swap | `[1, 2, 1]` |

*End of loop. Returns `i + 1`, which is `1 + 1 = 2`. The first 2 elements are `[1, 2]`.*

---

## Time & Space Complexity

* **Time:** O(n) — The scout pointer **j** travels across the array of length **n** exactly once from left to right. Every comparison and swap takes constant time.
* **Space:** O(1) — We only use two integer variables (**i** and **j**) for tracking pointers, requiring no extra memory regardless of how large the input array grows.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this solution is already **fully optimal**. 
* **Time-wise**, you cannot solve this faster than O(n) because you must look at every element in the array at least once to determine if it is a duplicate. 
* **Space-wise**, O(1) is the absolute best possible memory usage because the problem requires modifying the array in-place without using supplementary data structures.

---

## Edge Cases Handled

* **Single-element array (e.g., `nums = [1]`):** The loop condition `j < nums.size()` evaluates to false immediately because `j` starts at 1 and size is 1. The code safely bypasses the loop and returns `i + 1`, which correctly gives 1.
* **All elements identical (e.g., `nums = [5, 5, 5, 5]`):** The scout pointer **j** increments all the way to the end while the `else` branch triggers every time. Pointer **i** stays at 0, and the function correctly returns 1.
* **No duplicates at all (e.g., `nums = [1, 2, 3]`):** Every element triggers the `if` condition, advancing **i** and swapping with **j** every step, returning the full length of the array.
* **Negative numbers included:** Because the algorithm relies strictly on relative inequality (`!=`) rather than absolute values or zero boundaries, negative integers like `-5` and `-1` are compared and sorted just like positive numbers.
