![Runtime](https://img.shields.io/badge/Runtime-35%20ms%20(beats%2037.07%25)-orange?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-14.2%20MB%20(beats%2075.78%25)-green?style=for-the-badge)

---

## Problem Explained

You are given a list of integers named `nums` and a single integer named `target`. Your job is to find two different positions (indices) in `nums` whose values add up to `target`.

Key rules to keep in mind:
- Every input guarantees exactly **one** correct pair.
- You **cannot** use the exact same element position twice.
- You can return the two index numbers in any order.

**Example:**
If `nums = [2, 7, 11, 15]` and `target = 9`:
- Look at index `0` (value `2`) and index `1` (value `7`).
- `2 + 7 = 9`, which matches `target`.
- The answer is `[0, 1]`.

---

## Intuition

The standard **brute force** idea is simple: check every possible pair of numbers until you find one that adds up to `target`.

Think of it like testing combinations on a lock. You fix the first number, then try pairing it with every number that comes after it. If none of those work, you move to the second number and check every number after that. Because a solution is guaranteed to exist, this step-by-step checking will eventually hit the target.

---

## Approach

Here is how the C++ code carries out this process:

- `int n = nums.size();`  
  Calculates total count of numbers in `nums` and saves it in `n`.
- `for( int i=0 ; i<n-1 ; i++ )`  
  Starts the outer loop. Variable `i` represents the index of the first number. It stops at `n-2` because the second number must come after it.
- `for( int j=i+1 ; j<n ; j++ )`  
  Starts the inner loop. Variable `j` represents the index of the second number. It begins at `i + 1` so you never test an element against itself.
- `if( nums[i]+nums[j] == target )`  
  Adds the number at index `i` and the number at index `j`. It checks if this sum equals `target`.
- `return {i,j};`  
  Returns the index pair inside a vector as soon as a match is found, ending the function immediately.
- `return {};`  
  Returns an empty vector as a fallback if no answer is found (required for compiler return paths).

---

## Dry Run

### Case 1: Standard case with multiple checks (`nums = [3, 2, 4]`, `target = 6`)

| Step | `i` | `j` | `nums[i]` | `nums[j]` | Sum (`nums[i] + nums[j]`) | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `0` | `1` | `3` | `2` | `5` | `5 != 6`. Keep checking inner loop. |
| 2 | `0` | `2` | `3` | `4` | `7` | `7 != 6`. Inner loop ends. |
| 3 | `1` | `2` | `2` | `4` | `6` | `6 == 6`. Target matched! Return `{1, 2}`. |

---

### Case 2: Edge case with duplicate values (`nums = [3, 3]`, `target = 6`)

| Step | `i` | `j` | `nums[i]` | `nums[j]` | Sum (`nums[i] + nums[j]`) | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `0` | `1` | `3` | `3` | `6` | `6 == 6`. Target matched! Return `{0, 1}`. |

---

## Time & Space Complexity

- **Time Complexity:** **O(n^2)** — The nested loops check pairs of elements. In the worst case, you perform roughly `(n * (n - 1)) / 2` checks.
- **Space Complexity:** **O(1)** — Memory usage remains constant because the code uses only a few simple integer variables (`n`, `i`, `j`).

---

### Can We Improve It?

**Yes.** The time complexity can be improved to **O(n)** using a **hash map** (in C++, `std::unordered_map`).

#### Why the Hash Map Optimization Works
When standing at a number `x` at index `i`, we already know the exact value we need to reach `target`: `needed = target - x`.

Instead of running a second loop to search for `needed`, we can keep track of numbers we have already seen in a map. As we walk through `nums` once:
1. We calculate `needed = target - nums[i]`.
2. We check if `needed` is already stored in our map.
3. If it is in the map, we instantly know its stored index, and we return `{map[needed], i}`.
4. If it is not in the map, we save `nums[i]` with its index `i` into the map and move forward.

#### Key Code Snippet (Optimized Approach)

```cpp
unordered_map<int, int> seen;
for (int i = 0; i < nums.size(); i++) {
    int needed = target - nums[i];
    if (seen.count(needed)) {
        return {seen[needed], i};
    }
    seen[nums[i]] = i;
}
return {};
```

- `unordered_map<int, int> seen;` — Stores each number as a key and its index as the value.
- `int needed = target - nums[i];` — Calculates the missing complement needed to make `target`.
- `seen.count(needed)` — Looks up `needed` in constant average time **O(1)**.

#### Improved Complexity
- **Time Complexity:** **O(n)** — We iterate through `nums` only once. Map lookups take **O(1)** average time.
- **Space Complexity:** **O(n)** — Storing up to `n` elements in the hash map uses linear extra space.

#### Theoretical Best
**O(n) time** is the theoretical best performance because you must inspect each element at least once to know its value. The hash map solution reaches this optimal complexity.

---

## Edge Cases Handled

- **Smallest Array Size (`n = 2`):** The bounds `i < n - 1` (evaluates to `i < 1`) and `j = i + 1` work cleanly without index errors.
- **Duplicate Elements:** Arrays like `[3, 3]` work because `j` starts at `i + 1`, allowing two identical numbers at different indices to be picked.
- **Negative Numbers:** The arithmetic `nums[i] + nums[j]` handles negative numbers correctly (e.g., `-3 + 11 = 8`).
- **Large Values:** Values up to `10^9` fit within standard 32-bit signed integers, so no integer overflow occurs during arithmetic operations.
