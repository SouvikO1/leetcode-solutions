![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.03%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The problem asks us to determine if a given integer is a **palindrome**. 

A palindrome is a number that reads the exact same way forwards and backwards. For example, the number 121 is a palindrome because reading it from left to right gives 121, and reading it from right to left also gives 121. 

However, numbers like minus 121 are not palindromes. Reading minus 121 from left to right gives minus 121, but reading it from right to left gives 121 followed by a minus sign, which is not the same. Similarly, the number 10 is not a palindrome because reversing its digits gives 01, which is just 1.

## Intuition

The core idea to check if a number is a palindrome is to **reverse the entire number** and compare the reversed version to the original. If both numbers are identical, then the original number is a palindrome.

To reverse a number using math:
- We can extract the last digit of a number by taking the remainder when divided by 10 (using the modulo operator).
- We can remove that last digit from the number by dividing it by 10.
- As we extract each digit, we build our reversed number by multiplying our current running total by 10 and adding the new digit.

There is one important detail: negative numbers can never be palindromes because of the minus sign at the front. We can filter those out immediately.

## Approach

- `if( x < 0)`: Checks if the input number is negative. If it is, the code immediately returns `false` because a negative sign at the beginning prevents it from ever matching its reversed self.
- `long n = x;`: Saves a copy of the original input number in a variable named `n` so we can compare it later, because the variable `x` will be completely destroyed during the reversal process.
- `long rev = 0 ;`: Declares a variable named `rev` to hold the reversed number, starting it at 0. It uses the `long` data type to prevent any potential number overflow issues during the math.
- `while ( x != 0)`: Starts a loop that continues as long as there are still digits left in `x` to process.
- `rev = rev * 10 + x % 10;`: Takes the current reversed number `rev`, multiplies it by 10 to shift its digits to the left, and adds the last digit of `x` (which is `x % 10`) to the end.
- `x /= 10;`: Removes the last digit from `x` by performing integer division by 10, shrinking `x` step by step until it reaches 0.
- `if( rev == n )`: Compares the fully reversed number stored in `rev` against our saved original number stored in `n`.
- `return true;`: Returns true if `rev` equals `n`, meaning the number is a palindrome.
- `return false;`: Returns false if `rev` does not equal `n`, meaning the number is not a palindrome.

## Dry Run

### Case 1: Typical case (x = 121)

| `x` | `n` | `rev` | Action |
| --- | --- | --- | --- |
| 121 | 121 | 0 | Initial state. Not negative. |
| 12 | 121 | 1 | Loop 1: `rev` becomes 0 * 10 + 1 = 1. `x` becomes 12. |
| 1 | 121 | 12 | Loop 2: `rev` becomes 1 * 10 + 2 = 12. `x` becomes 1. |
| 0 | 121 | 121 | Loop 3: `rev` becomes 12 * 10 + 1 = 121. `x` becomes 0. Loop ends. |
| 0 | 121 | 121 | `rev` (121) equals `n` (121). Returns `true`. |

### Case 2: Edge case with a negative number (x = -121)

| `x` | `n` | `rev` | Action |
| --- | --- | --- | --- |
| -121 | unassigned | unassigned | Initial state. `x < 0` is true. Returns `false` immediately. |

## Time & Space Complexity

**Time:** O(log10(n)) — The while loop runs once for every single digit in the number `x`. Since the number of digits in an integer grows proportionally to the base-10 logarithm of its value, the time complexity scales with the number of digits.
**Space:** O(1) — We only use a few variables (`n`, `rev`) that take up a constant amount of extra memory, regardless of how large the input number is.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this code is already optimal in terms of both time and space complexity. 

- **Time Optimality:** To determine if a number is a palindrome, you fundamentally have to inspect its digits. You cannot avoid processing the digits at least once, meaning you cannot beat a linear pass proportional to the number of digits.
- **Space Optimality:** We are only using a couple of primitive variables for tracking math, meaning we use zero extra data structures and achieve constant space complexity. 

No further improvements to time or space complexity are possible.

## Edge Cases Handled

- **Negative numbers:** Handled correctly by the initial check, instantly returning `false` since a minus sign means it cannot be a palindrome.
- **Single-digit numbers:** Handled correctly because a single digit loops once, `rev` matches `n`, and it returns `true`.
- **Numbers ending in zero (like 10):** Handled correctly because reversing 10 produces 1, which does not match 10, returning `false`.
- **Large boundary numbers:** Handled safely by using `long` variables for `n` and `rev`, preventing arithmetic overflow issues when numbers get close to the maximum integer limits.
