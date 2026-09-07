![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2037.07%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.78%25)-green?style=for-the-badge)

---

## Problem Explained

Imagine you have a list of numbers, and someone gives you a specific goal sum (called the **target**). Your job is to find two different numbers in that list that add up to equal that **target**. 

Once you find them, you need to return their positions in the list (their **indices**, starting from zero). 

For example, if your list is [2, 7, 11, 15] and your **target** is 9, you look at the numbers and see that 2 plus 7 equals 9. Because 2 is at position 0 and 7 is at position 1, you return the answer [0, 1]. The problem guarantees that there is always exactly one correct pair waiting to be found.

## Intuition

The most straightforward way to solve this is to look at every possible pair of numbers in the list and check if they add up to the **target**. 

For someone visiting this for the first time, this means taking the first number and adding it to every number that comes after it. If it does not equal the **target**, you move to the second number and repeat the process. 

For someone returning to this solution months later, this is the brute-force nested loop approach. It does not require any fancy data structures; it just checks everything manually until it finds a match.

## Approach

Here is a step-by-step walk-through of how the code works:

* `int n = nums.size();`: This gets the total number of items in our list and stores it in the variable `n` so we know how far to loop.
* `for( int i=0 ; i<n-1 ; i++ )`: This outer loop sets up our first pointer `i`, starting at the very first element and stopping right before the last element because we need at least one element after it to form a pair.
* `for( int j=i+1 ; j<n ; j++ )`: This inner loop sets up our second pointer `j`, starting right after `i` and going all the way to the end of the list. This ensures we never pair a number with itself or check the same pair twice.
* `if( nums[i]+nums[j] == target )`: This checks if the sum of the number at index `i` and the number at index `j` equals our **target**.
* `return {i,j};`: If the sum matches the **target**, the code immediately packages both indices into a list and returns them, ending the function.
* `return {};`: If the loops finish completely without ever finding a match, this acts as a safety net to return an empty list (though the problem guarantees a solution exists).

## Dry Run

### Case 1: Typical case
Inputs: `nums = [2, 7, 11, 15]`, `target = 9`, `n = 4`

| i | j | nums[i] + nums[j] | Target Check (== 9) | Action |
|---|---|---|---|---|
| 0 | 1 | 2 + 7 = 9 | True | Match found! Returns `{0, 1}` |

### Case 2: Second example from problem
Inputs: `nums = [3, 2, 4]`, `target = 6`, `n = 3`

| i | j | nums[i] + nums[j] | Target Check (== 6) | Action |
|---|---|---|---|---|
| 0 | 1 | 3 + 2 = 5 | False | No match, move inner loop pointer |
| 0 | 2 | 3 + 4 = 7 | False | No match, outer loop moves to next `i` |
| 1 | 2 | 2 + 4 = 6 | True | Match found! Returns `{1, 2}` |

## Time & Space Complexity

**Time:** O(n^2) — using a nested loop where the outer loop runs N times and the inner loop runs roughly N times on average, creating N * N total checks.
**Space:** O(1) — using a fixed number of variables (`n`, `i`, `j`), requiring no extra memory that grows with the input size.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, it can be significantly improved. Right now, we waste time repeatedly looking at numbers we have already checked by using an inner loop. 

We can speed this up by using a **hash table** (a data structure that lets you look up values instantly, called `unordered_map` in C++). As we walk through the list just once, we can ask: "What is the missing number I need to reach my target?" (which is `target - current_number`). Instead of scanning the list again to find that missing number, we can check our hash table to see if we have already passed it. If it is there, we instantly have our answer. If it is not there, we save our current number and its index into the hash table and move on.

Here is what the optimized code looks like using this idea:

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

- `unordered_map<int, int> seen;` creates our hash table to store numbers we have visited and their indices.
- `int complement = target - nums[i];` calculates the exact number we need to find to reach our target.
- `if (seen.count(complement))` checks if that required number has already been seen in O(1) average time.
- `seen[nums[i]] = i;` saves the current number and its index for future lookups if no match was found yet.

**Improved time complexity:** O(n) — because we only loop through the array once, and hash table lookups take O(1) time on average.
**Improved space complexity:** O(n) — because in the worst-case scenario, we store every element in the hash table.
**Theoretical best possible complexity:** O(n) time and O(n) space. The improved version reaches this optimal limit.

## Edge Cases Handled

* **Two elements only:** The constraints state `nums.length >= 2`. The loops handle this by running once (`i=0`, `j=1`).
* **Negative numbers:** The logic uses standard addition and subtraction, so negative numbers like `target = -5` with `nums = [-2, -3]` are calculated correctly.
* **Duplicates:** If the array contains duplicate numbers (like `[3, 3]` with target 6), the nested loops check each position independently, correctly returning `[0, 1]` because they use index positions `i` and `j` rather than raw values.
