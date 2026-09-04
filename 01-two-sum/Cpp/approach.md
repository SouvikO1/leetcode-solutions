![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2036.82%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.18%20MB%20(beats%2075.34%25)-green?style=for-the-badge)

---

## Problem Explained

You are given a list of integers called `nums` and a single goal integer called `target`. Your task is to find two numbers in `nums` that add up to `target`. Once you find them, you must return their positions in the list (their indices).

Key rules to keep in mind:
* You cannot use the exact same position twice to make the sum.
* There will always be exactly one correct pair of positions that works.
* You can return the two positions in any order (for example, `[0, 1]` or `[1, 0]`).

**Example:**
If `nums = [2, 7, 11, 15]` and `target = 9`:
* Position 0 has `2`.
* Position 1 has `7`.
* `2 + 7 = 9`, which matches the target `9`.
* The answer is `[0, 1]`.

---

## Intuition

The standard way to tackle this is to check every possible pair of numbers until we find a pair that adds up to the goal.

Imagine holding the first number with your left hand, and then using your right hand to point to every number after it one by one, checking if they add up to `target`. If none work, move your left hand to the second number and check all the numbers after it with your right hand. Repeating this guarantees you will eventually test every possible pair without repeating work or testing a position against itself.

---

## Approach

Here is step-by-step how the code runs:

* `int n = nums.size();`: Count how many total numbers are in the `nums` list and store that total in the variable `n`.
* `for( int i=0 ; i<n-1 ; i++ )`: Start a outer loop with an index variable `i`. This represents our first number's position. It starts at position `0` and goes up to the second-to-last item (`n - 1`).
* `for( int j=i+1 ; j<n ; j++ )`: Start an inner loop with an index variable `j`. This represents our second number's position. It always starts at `i + 1` (the position right after `i`) and goes to the very end of the array. This keeps us from comparing an element with itself or re-testing pairs we already checked.
* `if( nums[i]+nums[j] == target )`: Check if the number at position `i` plus the number at position `j` equals our target value.
* `return {i,j};`: If the numbers add up to `target`, stop running immediately and return a vector containing the two indices `{i, j}`.
* `return {};`: If the loops finish and no pair was found, return an empty list. (This line exists for safety, though the problem guarantees a pair will always exist).

---

## Dry Run

### Case 1: First attempt matches (`nums = [2, 7, 11, 15]`, `target = 9`)

| `i` | `j` | `nums[i]` | `nums[j]` | `nums[i] + nums[j]` | Action |
|---|---|---|---|---|---|
| 0 | 1 | 2 | 7 | 9 | `9 == 9` is true. Return `{0, 1}` immediately. |

---

### Case 2: Match found deeper in array (`nums = [3, 2, 4]`, `target = 6`)

| `i` | `j` | `nums[i]` | `nums[j]` | `nums[i] + nums[j]` | Action |
|---|---|---|---|---|---|
| 0 | 1 | 3 | 2 | 5 | `5 == 6` is false. Move to next `j`. |
| 0 | 2 | 3 | 4 | 7 | `7 == 6` is false. Outer loop moves to `i = 1`. |
| 1 | 2 | 2 | 4 | 6 | `6 == 6` is true. Return `{1, 2}`. |

---

## Time & Space Complexity

* **Time Complexity:** **O(n^2)** — The nested loops check up to `n * (n - 1) / 2` pairs in the worst case. As the input size `n` grows, the operations grow quadratically.
* **Space Complexity:** **O(1)** — We only store a few simple integer variables (`n`, `i`, `j`). Memory usage stays constant regardless of array size.

### Can this be improved?

**Yes, it can be improved to O(n) time complexity.**

Right now, for every number `x`, we loop through the remaining numbers to see if its match (`target - x`) exists. We can replace that inner loop lookup with a **hash table** (in C++, an `unordered_map`). A hash table lets us check if a value exists almost instantly in **O(1)** time.

Instead of comparing pairs:
1. Walk through the array once.
2. For each number, calculate `needed = target - current_number`.
3. Check if `needed` is already saved in our hash table.
4. If it is, we found our pair!
5. If it isn't, save the current number and its index into the hash table and keep moving forward.

Here is how the key part of the optimized code looks:

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

* `unordered_map<int, int> seen;`: Creates a fast lookup table that maps a number to its index position.
* `int complement = target - nums[i];`: Calculates the exact matching value we need to find.
* `if (seen.count(complement))`: Instantly checks if we have already encountered that needed number earlier in the list.
* `seen[nums[i]] = i;`: Remembers the current number and index for future elements to find.

### Improved Complexity
* **Time Complexity:** **O(n)** — We pass through the list only once. Looking up items in `unordered_map` takes O(1) time on average.
* **Space Complexity:** **O(n)** — In the worst case, we store up to `n` elements inside the hash table.

**Theoretical Best:** **O(n)** time is the absolute theoretical limit because you must look at every number at least once to know if it participates in the solution. The hash table approach reaches this optimal bound.

---

## Edge Cases Handled

* **Minimum array length (2 elements):** The code runs correctly when `nums` has only 2 items. The loops execute exactly once for `i = 0` and `j = 1`.
* **Duplicate values in `nums`:** For `nums = [3, 3]` and `target = 6`, `i` will be `0` and `j` will be `1`. The code correctly compares two different positions with the same value without using index `0` twice.
* **Negative numbers:** The logic handles negative values naturally (for example `nums = [-3, 4, 3]` and `target = 0`), because arithmetic addition works seamlessly with negative integers in standard C++.
