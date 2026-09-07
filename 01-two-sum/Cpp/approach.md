![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2037.07%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.78%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a row of numbered boxes (an array of integers) and a specific goal number (the target). Your job is to find exactly **two** different boxes whose numbers add up to that target number. 

Once you find them, you need to return their position numbers (their indices). 

For example, if your boxes contain the numbers 2, 7, 11, and 15, and your target is 9, you look at the box with 2 and the box with 7. Since 2 plus 7 equals 9, you return their positions: 0 and 1. 

The rules say there will always be one correct answer, and you cannot use the same box twice.

---

## Intuition

The core idea here is the **brute force** method — which is a fancy way of saying "try every possible pair until you find the right one." 

If you are revisiting this, you might remember it as the nested loop approach. You pick the first number, and then you walk down the rest of the line checking every other number to see if the two add up to the target. If they don't, you move to the second number and repeat the check. 

While it is slow for large lists, it is very easy to write and understand because it mimics how a human would manually search through a list of numbers.

---

## Approach

Here is how the code executes step-by-step:

* `int n = nums.size();` — This calculates how many numbers are in the input list and stores that count in the variable **n**.
* `for( int i=0 ; i<n-1 ; i++ )` — This starts an outer loop using a pointer named **i**. It looks at numbers starting from the very first one up until the second-to-last one.
* `for( int j=i+1 ; j<n ; j++ )` — Inside the first loop, this starts a second loop using a pointer named **j**. It always looks at numbers that come *after* the one pointed to by **i**, ensuring we never check the same number against itself or repeat a pair backward.
* `if( nums[i]+nums[j] == target )` — This checks if the number at position **i** plus the number at position **j** equals our **target**.
* `return {i,j};` — If the math checks out, it immediately bundles positions **i** and **j** together and returns them as the final answer.
* `return {};` — If both loops finish running completely without ever finding a matching pair, this backup line returns an empty list (though the problem guarantees a valid answer always exists).

---

## Dry Run

### Case 1: Typical case (nums = [2,7,11,15], target = 9)

| i | j | nums[i] | nums[j] | nums[i] + nums[j] == 9? | Action |
|---|---|---------|---------|-------------------------|--------|
| 0 | 1 | 2       | 7       | Yes (2 + 7 = 9)         | Returns `{0, 1}` immediately |

### Case 2: Edge case with duplicate values (nums = [3,3], target = 6)

| i | j | nums[i] | nums[j] | nums[i] + nums[j] == 6? | Action |
|---|---|---------|---------|-------------------------|--------|
| 0 | 1 | 3       | 3       | Yes (3 + 3 = 6)         | Returns `{0, 1}` immediately |

---

## Time & Space Complexity

* **Time:** O(n^2) — using two nested loops that each scale with the size of the input list. The outer loop runs roughly n times, and the inner loop runs roughly n times for each step, leading to N * N total checks in the worst-case scenario.
* **Space:** O(1) — constant extra space, because we are only creating a couple of simple integer variables (**n**, **i**, **j**) regardless of how large the input list gets.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

No, this can be significantly improved. 

### How to Optimize

Right now, our inner loop wastes a lot of time re-checking numbers we have already looked at. To speed this up, we can use a **Hash Table** (called an `unordered_map` in C++). A hash table is like a digital phonebook where you can instantly look up a value without scanning the whole book page by page.

Instead of asking "does this number plus every future number equal the target?", we can flip the math around. As we walk through the list item by item, we can calculate what *complement* we need. For any number, its complement is simply `target minus current_number`. 

We ask our hash table: "Have I already seen this required complement earlier?" If no, we save the current number and its position into the hash table and move on. If yes, we instantly found our pair! This lets us solve the entire problem in a single pass through the list.

### Optimized Code Snippet

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> seen; // stores {number, its position}
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            if (seen.find(complement) != seen.end()) {
                return {seen[complement], i}; // found it! return old position and current position
            }
            seen[nums[i]] = i; // remember this number for the future
        }
        return {};
    }
};
```

* `unordered_map<int, int> seen;` creates our instant lookup table.
* `int complement = target - nums[i];` calculates the exact missing partner we need.
* `seen.find(complement)` checks our lookup table in O(1) average time to see if we've met that partner already.
* `seen[nums[i]] = i;` logs the current number so future numbers can check against it.

### Resulting Improved Complexity
* **Time:** O(n) — we only loop through the list once, and hash table lookups take O(1) time on average.
* **Space:** O(n) — in the worst case, we might store almost every number in our hash table before finding the match.

### Theoretical Best Complexity
O(n) time is the theoretical best possible complexity for this problem because you must look at every element in the array at least once to ensure you don't miss the answer. Our optimized hash table version successfully reaches this optimal limit.

---

## Edge Cases Handled

* **Minimum array size:** The constraints guarantee the array has at least 2 elements (`nums.length >= 2`), so the loops will always have valid bounds to check.
* **Duplicate numbers:** Handled correctly because the loops use strict index positioning (`j = i+1`), meaning the code treats identical numbers at different positions as unique entries (such as `[3, 3]` with target 6).
* **Negative numbers:** Handled correctly because basic integer addition and subtraction work identically with negative values (e.g., target - negative number becomes addition).
