![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2036.97%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.50%25)-green?style=for-the-badge)

---

## Problem Explained

You are given a list of numbers called `nums` and a goal number called `target`. Your job is to find two different numbers in `nums` that add up to `target`, and return their position numbers (indices) in the list.

You can assume that every input has **exactly one correct pair**, and you cannot use the exact same position twice. You can return the two index numbers in any order.

For example, if `nums = [2, 7, 11, 15]` and `target = 9`:
- Look at index `0` (value `2`) and index `1` (value `7`).
- Their sum is `2 + 7 = 9`.
- The answer is `[0, 1]`.

---

## Intuition

The standard brute-force idea is to test every possible pair of numbers until you find the right sum. 

Imagine standing in front of a row of boxed items. You pick up the first box, keep hold of it, and then check it against every other box down the line one by one. If none of those pair up to make the goal sum, you put the first box down, pick up the second box, and repeat the process for all remaining boxes. As soon as a match is found, you stop and return their positions.

---

## Approach

Here is how the provided code works step-by-step:

* `int n = nums.size();`  
  This calculates the total number of elements in `nums` and stores it in variable `n`.

* `for( int i=0 ; i<n-1 ; i++ ){`  
  This outer loop picks the first number's index, starting at index `0` and going up to the second-to-last element (`n-2`).

* `for( int j=i+1 ; j<n ; j++ ){`  
  This inner loop picks the second number's index, starting immediately after `i` (`j = i + 1`) and checking every remaining element up to the end of the array. Starting at `i + 1` ensures we do not compare a number with itself or re-check pairs we already looked at.

* `if( nums[i]+nums[j] == target ){`  
  This checks whether the sum of the element at index `i` and the element at index `j` matches `target`.

* `return {i,j};`  
  If the sum equals `target`, the code immediately returns a vector with indices `i` and `j`.

* `return {};`  
  This acts as a fallback to return an empty array if no pair is found (though the problem guarantees a valid answer always exists).

---

## Dry Run

### Case 1: Typical case (`nums = [2, 7, 11, 15]`, `target = 9`)

| `i` | `j` | `nums[i]` | `nums[j]` | `nums[i] + nums[j]` | Action |
|---|---|---|---|---|---|
| `0` | `1` | `2` | `7` | `9` | Matches `target` (9). Returns `{0, 1}` immediately. |

---

### Case 2: Matching pair later in array (`nums = [3, 2, 4]`, `target = 6`)

| `i` | `j` | `nums[i]` | `nums[j]` | `nums[i] + nums[j]` | Action |
|---|---|---|---|---|---|
| `0` | `1` | `3` | `2` | `5` | No match. Increment `j`. |
| `0` | `2` | `3` | `4` | `7` | No match. Inner loop finishes. Increment `i`. |
| `1` | `2` | `2` | `4` | `6` | Matches `target` (6). Returns `{1, 2}`. |

---

## Time & Space Complexity

* **Time Complexity:** **O(n^2)**  
  The code uses two nested loops. In the worst case, it compares almost every element with every other element, taking about `(n * (n - 1)) / 2` steps.

* **Space Complexity:** **O(1)**  
  The code uses a constant amount of extra memory regardless of how large `nums` is.

---

### Can this be improved?

**Yes.** We can reduce the time complexity to **O(n)** using a **hash map** (an `unordered_map` in C++).

#### Why and how the improvement works:
Instead of running a second loop to search for the matching number, we can remember numbers we have already seen. 

For any number `curr` at index `i`, its required pair is `complement = target - curr`. 

As we iterate through the list once:
1. We check if `complement` is already in our map of seen numbers.
2. If it is present, we immediately have both indices: the stored index of `complement` and the current index `i`.
3. If it is not present, we put `curr` and its index `i` into our map so future numbers can find it.

Because looking up an item in a hash map takes **O(1)** average time, we only need a single pass through the array.

#### Code Snippet for the Optimized Approach:

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> seen; // Stores value -> index
        
        for (int i = 0; i < nums.size(); i++) {
            int complement = target - nums[i];
            
            // Check if required complement was already seen
            if (seen.count(complement)) {
                return {seen[complement], i};
            }
            
            // Store current number and its index
            seen[nums[i]] = i;
        }
        
        return {};
    }
};
```

* **Improved Time Complexity:** **O(n)** — We traverse the list once, and map operations take **O(1)** average time.
* **Improved Space Complexity:** **O(n)** — The map stores up to `n` elements in memory.
* **Theoretical Best:** **O(n)** time is optimal because every element must be inspected at least once to find the solution. The hash map approach achieves this theoretical limit.

---

## Edge Cases Handled

* **Minimum Array Size (`nums.length = 2`):** Works correctly. The outer loop runs for `i = 0`, and the inner loop runs for `j = 1`.
* **Duplicate Values (`nums = [3, 3]`, `target = 6`):** Works correctly. The inner loop starts at `j = i + 1`, so identical values at different positions are treated as distinct elements.
* **Negative Numbers (`nums = [-3, 4, 3]`, `target = 0`):** Works correctly because standard arithmetic handles negative values seamlessly.
* **Large Numbers:** Standard integer calculations run safely within integer limits.
