![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2037.00%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.64%25)-green?style=for-the-badge)

---

## Problem Explained

The goal is to find two numbers in an array that add up to a specific sum, called **target**. 

Once you find those two numbers, you must return their **indices** (their 0-based positions in the array).

Rules to keep in mind:
* Exactly one valid answer exists for every input.
* You cannot use the same element at the same index twice.
* You can return the two indices in any order.

**Example:**
* Input: `nums = [2, 7, 11, 15]`, `target = 9`
* Logic: `nums[0]` is `2` and `nums[1]` is `7`. Since `2 + 7 = 9`, the answer is `[0, 1]`.

---

## Intuition

This solution uses a straightforward **brute force** idea. 

Imagine you are standing in front of a row of numbered boxes. You pick the first box, then walk down the line checking every remaining box to see if its number adds up to the target with your chosen box. If no box works, you go back, pick the second box, and test all the boxes after it. You repeat this process until you find the matching pair.

---

## Approach

Here is step-by-step how the code executes this idea:

* `int n = nums.size();`  
  Calculates the total number of elements in `nums` and stores it in `n`.
* `for( int i=0 ; i<n-1 ; i++ )`  
  Starts an outer loop using index `i` to pick the first number. It stops at `n - 2` (the second-to-last item) because there must be at least one number left after it to pair with.
* `for( int j=i+1 ; j<n ; j++ )`  
  Starts an inner loop using index `j` to pick the second number. It always begins right after `i` (at `i + 1`) and goes to the end of the array. This ensures we never compare an element with itself or re-check pairs we already looked at.
* `if( nums[i]+nums[j] == target )`  
  Checks if the value at index `i` plus the value at index `j` equals `target`.
* `return {i,j};`  
  If the sum matches `target`, it creates a list with `i` and `j` and immediately returns it as the final answer.
* `return {};`  
  Returns an empty list as a default backup if no solution is found (though the problem guarantees a solution will always exist).

---

## Dry Run

### Case 1: Standard case
Input: `nums = [2, 7, 11, 15]`, `target = 9`

| `i` | `nums[i]` | `j` | `nums[j]` | `nums[i] + nums[j]` | Action |
| --- | --- | --- | --- | --- | --- |
| 0 | 2 | 1 | 7 | 2 + 7 = 9 | Match found (`9 == 9`). Return `[0, 1]`. |

---

### Case 2: Solution not at the beginning
Input: `nums = [3, 2, 4]`, `target = 6`

| `i` | `nums[i]` | `j` | `nums[j]` | `nums[i] + nums[j]` | Action |
| --- | --- | --- | --- | --- | --- |
| 0 | 3 | 1 | 2 | 3 + 2 = 5 | No match (`5 != 6`). Continue. |
| 0 | 3 | 2 | 4 | 3 + 4 = 7 | No match (`7 != 6`). Inner loop ends. |
| 1 | 2 | 2 | 4 | 2 + 4 = 6 | Match found (`6 == 6`). Return `[1, 2]`. |

---

## Time & Space Complexity

* **Time Complexity:** O(n^2)  
  The outer loop runs up to `n` times. For each step of the outer loop, the inner loop can run up to `n` times. This means we check about `n * n / 2` pairs in the worst case.
* **Space Complexity:** O(1)  
  The algorithm uses a constant amount of extra memory. It only stores a few simple integer variables (`n`, `i`, and `j`).

### Can we improve this?

**Yes.** We can reduce the time complexity to **O(n)** by using a **Hash Table** (a fast lookup structure, called `std::unordered_map` in C++).

#### Why the Hash Table approach works:
Instead of re-scanning the array with a nested loop, we can ask a different question as we walk through the array once. 

For any number `x` at index `i`, we need a second number equal to `target - x` (its **complement**). If we keep track of numbers we have already seen in a map (mapping `number -> index`), we can check if the complement is already in our map in instant time.

* If `target - x` is in the map, we instantly have our pair!
* If not, we store `x` and its index `i` in the map, then move to the next item.

#### Optimized Code Snippet:
```cpp
unordered_map<int, int> seen;
for (int i = 0; i < nums.size(); i++) {
    int complement = target - nums[i];
    if (seen.count(complement)) {
        return {seen[complement], i};
    }
    seen[nums[i]] = i;
}
return {};
```

* `unordered_map<int, int> seen;` creates a hash table that stores values as keys and their array positions as values.
* `seen.count(complement)` checks if the required matching value was previously seen.
* `seen[nums[i]] = i;` saves the current number and index for future lookups.

#### Improved Complexities:
* **Improved Time Complexity:** O(n) — We traverse the array only once. Map lookups take O(1) time on average.
* **Improved Space Complexity:** O(n) — The map stores up to `n` elements in the worst case.
* **Theoretical Best:** **O(n) time** is the optimal complexity overall, because every number must be inspected at least once. The hash table approach reaches this theoretical limit.

---

## Edge Cases Handled

* **Duplicate Numbers in Input:** For an input like `nums = [3, 3]` and `target = 6`, the nested loops correctly pair index `0` and index `1` because `j` starts at `i + 1`.
* **Negative Numbers:** Works automatically for inputs with negative integers (e.g., `nums = [-3, 4, 3, 90]`, `target = 0`) because standard addition handles negative values natively.
* **Smallest Allowed Input:** The problem guarantees at least 2 elements (`nums.length >= 2`). For an array of size 2, `i = 0` and `j = 1` run exactly once, avoiding out-of-bounds errors.
