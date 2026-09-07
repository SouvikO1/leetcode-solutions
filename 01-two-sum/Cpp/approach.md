![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2037.07%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.78%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbers, like `[2, 7, 11, 15]`, and a goal number, like `9`. The problem asks you to find two numbers in that row that add up to your goal number. 

Once you find them, you need to return their positions (their **indices**, starting from `0`). For `[2, 7, 11, 15]` and a target of `9`, the numbers `2` and `7` add up to `9`. Their positions are `0` and `1`. So the answer is `[0, 1]`. 

You are guaranteed that there is always exactly one correct pair, and you are not allowed to use the exact same position twice.

## Intuition

The "brute force" idea is to look at every possible pair of numbers in the array. You pick the first number, then check every other number that comes after it to see if they add up to the target. 

If they do not, you move to the second number and check all the numbers after it. You keep doing this until you find a pair that works. It is like checking every handshake in a room one by one. It is simple to write, but it does a lot of repetitive work.

## Approach

Here is how the code walks through the array step-by-step:

* `int n = nums.size();` — This gets the total count of elements in the `nums` array and saves it in `n` so we know how far to loop.
* `for( int i=0 ; i<n-1 ; i++ )` — This starts an outer loop with index `i`. It stops one spot before the end because the last number has no pairs coming after it.
* `for( int j=i+1 ; j<n ; j++ )` — This starts an inner loop with index `j`. It always starts right after `i` to avoid checking the same pair twice or pairing a number with itself.
* `if( nums[i]+nums[j] == target )` — This checks if the number at position `i` plus the number at position `j` equals our target number.
* `return {i,j};` — If the sum matches the target, this immediately returns the two positions as a pair.
* `return {};` — If the loops finish completely without ever returning inside the if statement, this safety net returns an empty result (though the problem guarantees a solution exists, so this line is never actually reached).

## Dry Run

### Case 1: Typical case (`nums = [2, 7, 11, 15]`, `target = 9`)

| `i` | `j` | `nums[i]` | `nums[j]` | Sum (`nums[i] + nums[j]`) | Action |
| --- | --- | --- | --- | --- | --- |
| `0` | `1` | `2` | `7` | `9` | Matches target (`9 == 9`). Returns `{0, 1}`. |

### Case 2: Second example (`nums = [3, 2, 4]`, `target = 6`)

| `i` | `j` | `nums[i]` | `nums[j]` | Sum (`nums[i] + nums[j]`) | Action |
| --- | --- | --- | --- | --- | --- |
| `0` | `1` | `3` | `2` | `5` | Does not match target (`5 != 6`). Keep going. |
| `0` | `2` | `3` | `4` | `7` | Does not match target (`7 != 6`). Keep going. |
| `1` | `2` | `2` | `4` | `6` | Matches target (`6 == 6`). Returns `{1, 2}`. |

## Time & Space Complexity

**Time:** O(n^2) — using a nested loop where the outer loop runs N times and the inner loop runs roughly N / 2 times on average, resulting in about N * N / 2 operations.
**Space:** O(1) — using a constant amount of extra memory regardless of the input size, since we only store a few variables like `n`, `i`, and `j`.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

No, the time complexity can be improved. 

Instead of checking every pair with nested loops, we can use a **hash table** (a data structure that lets us look up values instantly). As we walk through the array once, we can calculate the **complement** for each number (which is `target - nums[i]`). We check if we have already seen that complement earlier. If we have not, we save the current number and its index in our hash table and move on. The moment we encounter a number whose complement is already in the hash table, we instantly know we found our pair.

Here is what the optimized code looks like:

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> seen;
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            if (seen.count(complement)) {
                return {seen[complement], i};
            }
            seen[nums[i]] = i;
        }
        return {};
    }
};
```

* `unordered_map<int, int> seen;` creates a hash table to store numbers we have visited and their positions.
* `int complement = target - nums[i];` figures out what other number we need to reach the target.
* `if (seen.count(complement))` checks in instant time if we have already met that needed number.
* `seen[nums[i]] = i;` saves the current number so future steps can find it.

**Resulting improved complexity:**
- **Time:** O(n) — we only loop through the array once, and hash table lookups take O(1) time on average.
- **Space:** O(n) — in the worst case, we store every element of the array in the hash table.

**Theoretical best possible complexity:**
O(n) time is the theoretical best possible complexity for this problem because we must at least look at each element in the array once to know what it is. The improved version reaches this optimal time limit.

## Edge Cases Handled

* **Duplicate values (e.g. `[3, 3]`, target `6`):** Handled correctly because the inner loop checks positions one by one. The first `3` at index `0` pairs with the second `3` at index `1`.
* **Negative numbers (e.g. `[-1, -2, -3]`, target `-5`):** Handled correctly because addition and equality checks work the same way with negative numbers.
* **Minimum array size (length of 2):** Handled correctly because the outer loop runs when `i = 0` and the inner loop checks `j = 1`, covering the only possible pair.
