![Runtime](https://img.shields.io/badge/Runtime-36%20ms%20(beats%2030.01%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.13%20MB%20(beats%2071.02%25)-green?style=for-the-badge)

---

## Problem Explained

You are given a list of numbers called `nums` and a single goal number called `target`. Your job is to find **two distinct positions (indices)** in the list whose values add up to `target`. 

Key rules to keep in mind:
* Exactly one working pair exists in the list.
* You cannot use the same element twice (you cannot pick position `0` twice).
* You can return the two positions in any order.

**Example:**
If `nums = [2, 7, 11, 15]` and `target = 9`:
* `nums[0]` is `2`
* `nums[1]` is `7`
* `2 + 7 = 9`, which matches our target.
* The answer is `[0, 1]`.

---

## Intuition

The simplest way to solve this is by testing every pair of numbers. 

Imagine holding your left index finger on the first number, and using your right index finger to point to every number after it, checking if they add up to `target`. If none work, move your left finger to the second number and scan again with your right finger. 

You repeat this process until you find the exact pair that works.

---

## Approach

Here is how the code executes step-by-step:

* **Find array length:** Store the size of the `nums` array in variable `n`.
* **Outer loop:** Loop through the array using index `i` from `0` to `n - 2`. This holds the first number of the pair (`nums[i]`).
* **Inner loop:** Loop through the remaining array using index `j` from `i + 1` to `n - 1`. This looks at every number after `i` (`nums[j]`).
* **Check the sum:** If `nums[i] + nums[j] == target`, you found the pair. Immediately return `{i, j}`.
* **Fallback return:** If no pair is found by the end of the loops, return an empty list `{}`. *(Note: The problem guarantees a solution exists, so this fallback is just a safety measure for C++)*.

---

## Time & Space Complexity

* **Time Complexity:** **$O(n^2)$** — You are using two nested loops. In the worst case, you compare almost every element with every other element, leading to roughly $\frac{n^2}{2}$ checks.
* **Space Complexity:** **$O(1)$** — Memory stays constant because you only store a few basic integer variables (`n`, `i`, and `j`). No extra data structures are created.

### Can this be improved?

**Yes, significantly.** 

Instead of re-checking numbers with a second loop, you can use a **hash map** (a quick lookup table, `unordered_map` in C++). 

As you walk through the array, calculate the **complement** (the exact missing number needed to reach target: `target - current_number`). Check if that missing number is already stored in your map. If it is, you found your answer instantly. If not, save the current number and its index in the map for future steps.

#### Code Snippet (Optimized Approach):
```cpp
unordered_map<int, int> seen; // Stores value -> index

for (int i = 0; i < nums.size(); i++) {
    int complement = target - nums[i];
    
    // Check if the missing piece was already seen
    if (seen.count(complement)) {
        return {seen[complement], i};
    }
    
    // Save current number and index
    seen[nums[i]] = i;
}
return {};
```

* **Improved Complexity:**
  * **Time:** **$O(n)$** — You only pass through the array once. Looking up a value in a hash map takes **$O(1)$** time on average.
  * **Space:** **$O(n)$** — In the worst case, you store up to $n$ elements in the hash map.
* **Theoretical Best:** **$O(n)$ time** and **$O(n)$ space** is the optimal complexity for this problem. You must check each number at least once to know if it forms the sum, so you cannot get faster than $O(n)$ time.

---

## Edge Cases Handled

* **Minimum Array Size (`n = 2`):** The outer loop runs once (`i = 0`), and the inner loop runs once (`j = 1`). It correctly tests the only pair available.
* **Duplicate Numbers (`nums = [3, 3]`, `target = 6`):** Handled safely because the inner loop starts at `j = i + 1`. This guarantees two distinct positions are checked, preventing a single element from pairing with itself.
* **Negative Numbers (`nums = [-3, 4, 3]`, `target = 0`):** Basic addition handles negative values without extra code.
* **Solution at the Very End (`nums = [1, 2, 3, 4]`, `target = 7`):** The double loop fully checks all pairs, so it will reach `3` and `4` at the very end without missing them.
