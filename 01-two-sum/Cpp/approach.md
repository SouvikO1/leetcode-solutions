![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2036.97%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.50%25)-green?style=for-the-badge)

---

## Problem Explained

You are given a list of integers called `nums` and a single goal integer called `target`. Your task is to find two different numbers in `nums` that add up to `target`. 

Once you find them, you must return their position numbers (their 0-based indices) as a pair.

- You cannot use the exact same element twice (meaning you cannot pick the number at index 0 and add it to itself).
- Every input is guaranteed to have exactly one valid answer.
- You can return the two indices in any order.

**Example:**
If `nums = [2, 7, 11, 15]` and `target = 9`:
- `nums[0]` is `2`
- `nums[1]` is `7`
- `2 + 7 = 9`, which equals `target`.
- Output: `[0, 1]`

---

## Intuition

The direct way to solve this is to test every possible pair of numbers until you find the right sum. 

Imagine holding the first number in your hand. You then look at every other number in the list one by one to see if they add up to `target`. If none work, you move to the second number and check all remaining numbers after it. You keep repeating this process until you find the matching pair.

---

## Approach

Here is how the code works step-by-step:

- `int n = nums.size();`: Measures how many total numbers are in `nums` and stores that count in `n`.
- `for( int i=0 ; i<n-1 ; i++ )`: Starts an outer loop picking the first candidate index `i`. It runs from `0` up to the second-to-last index `n - 2`.
- `for( int j=i+1 ; j<n ; j++ )`: Starts an inner loop picking the second candidate index `j`. It always begins at `i + 1` so that we never check a number against itself or repeat previously tested pairs.
- `if( nums[i]+nums[j] == target )`: Adds the value at index `i` (`nums[i]`) to the value at index `j` (`nums[j]`) and checks if their total matches `target`.
- `return {i,j};`: Immediately stops the function and returns the indices `{i, j}` as soon as a matching pair is found.
- `return {};`: Returns an empty list as a fallback if no pair is found (this line is never reached based on problem constraints).

---

## Dry Run

### Case 1: Typical case (`nums = [2, 7, 11, 15]`, `target = 9`)

| Step | `i` | `j` | `nums[i]` | `nums[j]` | `nums[i] + nums[j]` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 0 | 1 | 2 | 7 | 9 | Sum matches `target`. Return `{0, 1}`. |

---

### Case 2: Edge case with duplicate values (`nums = [3, 3]`, `target = 6`)

| Step | `i` | `j` | `nums[i]` | `nums[j]` | `nums[i] + nums[j]` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 0 | 1 | 3 | 3 | 6 | Sum matches `target`. Return `{0, 1}`. |

---

## Time & Space Complexity

- **Time Complexity:** **O(n^2)** — The code uses two nested loops. In the worst case, it compares almost every pair. For `n` elements, it does roughly `(n * (n - 1)) / 2` operations, which simplifies to `O(n^2)`.
- **Space Complexity:** **O(1)** — No extra data structures are created. Memory usage stays constant regardless of how large `nums` gets.

### Can this be improved?

**Yes.** We can improve the time complexity to **O(n)** by trading some memory space.

Instead of checking all pairs with two loops, we can use a **hash table** (an `unordered_map` in C++), which stores key-value pairs for fast lookup.

**How the logic works:**
For any number `x`, the value needed to reach `target` is `target - x` (we call this the **complement**). 
As we walk through `nums` once:
1. Calculate `complement = target - nums[i]`.
2. Check if `complement` is already stored in our hash map.
3. If it is in the map, we instantly get its index and return it along with `i`.
4. If it is not in the map, store the current number `nums[i]` and its index `i` into the map, then move to the next number.

**Key changed lines:**

```cpp
unordered_map<int, int> seen; // Stores value -> index

for (int i = 0; i < nums.size(); i++) {
    int complement = target - nums[i]; // The number we need
    
    if (seen.count(complement)) {
        return {seen[complement], i}; // Found the pair
    }
    
    seen[nums[i]] = i; // Save current number and index for future checks
}
```

- `unordered_map<int, int> seen;`: Creates a hash map mapping values to their array index.
- `int complement = target - nums[i];`: Calculates the exact value needed to reach `target`.
- `seen.count(complement)`: Checks if the needed value was seen previously in constant time `O(1)`.

**Resulting improved complexity:**
- **Time Complexity:** **O(n)** — We loop through `nums` only once, performing fast `O(1)` map lookups.
- **Space Complexity:** **O(n)** — In the worst case, we store up to `n` elements in the hash table.

**Theoretical best possible:** **O(n)** time complexity, because every element must be inspected at least once. The improved hash map version achieves this theoretical limit.

---

## Edge Cases Handled

- **Duplicate Values:** Handled correctly (e.g., `nums = [3, 3]`, `target = 6`). Because the inner loop starts at `j = i + 1`, the code safely compares index 0 and index 1 without comparing an index against itself.
- **Negative Numbers:** Works smoothly (e.g., `nums = [-3, 4, 3]`, `target = 0`). Standard integer addition handles negative values automatically.
- **Minimum Array Size:** Works for arrays with only two elements (`nums.length == 2`). The loops run exactly once (`i = 0`, `j = 1`) and return the answer without out-of-bounds errors.
