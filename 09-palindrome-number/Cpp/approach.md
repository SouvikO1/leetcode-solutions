![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.03%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

This problem asks us to determine if a given whole number is a **palindrome**. 

A palindrome is a number that reads the exact same way forwards and backwards. For example, the number 121 reads as 121 from left to right, and also 121 from right to left. So it is a palindrome. 

On the other hand, a number like -121 is not a palindrome. Reading it backwards gives 121-, which does not match. Single-digit numbers are always palindromes because they read the same in both directions.

## Intuition

The core idea to check if a number is a palindrome is to **reverse the number** completely and compare the reversed version to the original number. If they are equal, it is a palindrome.

To reverse a number using math, we can repeatedly pull off its last digit using the modulo operator (which gives the remainder of a division by 10) and build a new reversed number digit by digit. 

Before we do any math, there is a quick shortcut: any negative number can never be a palindrome because of the minus sign at the front. So we can instantly return false for any number less than zero.

## Approach

- `if( x < 0)`: Checks if the input number is negative. If it is, it cannot be a palindrome, so the code immediately returns `false`.
- `long n = x;`: Saves a permanent copy of the original input number in a variable named `n` because the variable `x` will be destroyed while we reverse it.
- `long rev = 0 ;`: Creates a variable named `rev` starting at zero to hold our newly reversed number as we build it.
- `while ( x != 0){`: Starts a loop that keeps running as long as there are still digits left in `x` to process.
- `rev = rev * 10 + x % 10;`: Takes the current reversed number, multiplies it by 10 to shift its digits to the left, and adds the last digit of `x` (obtained via `x % 10`) onto the end.
- `x /= 10;`: Removes the last digit we just processed from `x` by dividing it by 10 and dropping the remainder.
- `if( rev == n ){`: After the loop finishes, compares our fully reversed number `rev` against our saved original number `n`.
- `return true;`: Executes if `rev` and `n` match, meaning the number reads the same forwards and backwards.
- `return false;`: Executes if `rev` and `n` do not match, meaning the number is not a palindrome.

## Dry Run

### Case 1: Typical case (`x = 121`)

| `x` (before loop / step) | `n` | `rev` (before step) | `rev * 10 + x % 10` (Action) | `x` (after step) |
|---|---|---|---|---|
| 121 | 121 | 0 | `0 * 10 + 1 = 1`, `rev` becomes 1 | 12 |
| 12 | 121 | 1 | `1 * 10 + 2 = 12`, `rev` becomes 12 | 1 |
| 1 | 121 | 12 | `12 * 10 + 1 = 121`, `rev` becomes 121 | 0 |

*End of loop:* `rev` is 121 and `n` is 121. They are equal, so the code returns `true`.

### Case 2: Negative number (`x = -121`)

| `x` | `n` | `rev` | Action |
|---|---|---|---|
| -121 | uninitialized | uninitialized | The initial check `x < 0` triggers immediately because `-121 < 0`. The code returns `false` right away without running the loop. |

## Time & Space Complexity

- **Time:** O(log10(x)) — The time complexity depends on the number of digits in `x`. Since we divide `x` by 10 in every loop iteration, the loop runs roughly once for every digit, which grows logarithmically with the size of the number.
- **Space:** O(1) — We only store a few variables (`n`, `rev`, and a shrinking `x`), using a constant amount of extra memory regardless of how large the input number is.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already optimal for time and space complexity. 

The time complexity cannot be beaten because we must look at every digit at least once to determine if the number is a palindrome. The space complexity is already at the absolute minimum (O(1)) because we only use a few primitive variables and do not allocate any data structures that grow with the input.

## Edge Cases Handled

- **Negative numbers:** Handled immediately by returning `false` since a minus sign ruins symmetry.
- **Single-digit numbers:** Handled correctly because the loop runs just once, `rev` matches `n`, and it returns `true`.
- **Numbers ending in zero (like 10):** Handled correctly because reversing 10 gives 1, which does not match 10, returning `false`.
- **Large boundary numbers:** Handled safely by using `long` types for `n` and `rev` to prevent potential math overflow during the reversal process.
