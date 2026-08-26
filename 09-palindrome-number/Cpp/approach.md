![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.42%20MB%20(beats%2092.22%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The problem asks whether a given integer reads the same backward as forward. A number with this property is called a **palindrome**.

If reading the number from left to right gives the exact same sequence of characters as reading it from right to left, you return `true`. Otherwise, you return `false`.

For example:
* `121` is a palindrome because reversing it still gives `121`.
* `-121` is not a palindrome because reversing it gives `121-` due to the minus sign.
* `10` is not a palindrome because reversing it gives `01` (which is `1`).

## Intuition

To check if a number is a palindrome without converting it into a text string, you can reverse the number mathematically. 

You can extract the last digit of a number using modulo 10 (`x % 10`), append that digit to a new running total, and then drop the last digit from the original number using division by 10 (`x / 10`). Repeat this process until all digits are extracted. 

If the reversed result matches the original starting number, it is a palindrome.

## Approach

Here is how the code works step-by-step:

* `if( x < 0){ return false; }`: Checks if the number is negative. Negative numbers are never palindromes because the negative sign sits at the front, but would end up at the back if reversed.
* `long n = x;`: Stores a copy of the original number `x` inside `n`. We need this because `x` will be destroyed as we pull its digits off one by one. We use a 64-bit integer (`long`) to avoid integer overflow issues when reversing very large numbers.
* `long rev = 0 ;`: Initializes `rev` to `0`. This variable will hold our reversed number as we build it.
* `while ( x != 0)`: Starts a loop that runs until all digits are processed and `x` becomes `0`.
* `rev = rev * 10 + x % 10;`: Gets the last digit of `x` using `x % 10`. Shifts existing digits in `rev` one place to the left by multiplying by 10, then adds the extracted digit.
* `x /= 10;`: Removes the last digit from `x` by dividing it by 10.
* `if( rev == n ){ return true; } else { return false; }`: Compares the fully reversed number `rev` with our saved original number `n`. If they are identical, it returns `true`; otherwise, it returns `false`.

## Dry Run

### Case 1: Positive palindrome (`x = 121`)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Initial | `121` | `121` | `0` | `x >= 0`, proceed to copy `x` into `n`. |
| Loop 1 | `12` | `121` | `1` | Extract `121 % 10 = 1`. `rev` becomes `0 * 10 + 1 = 1`. `x` becomes `12`. |
| Loop 2 | `1` | `121` | `12` | Extract `12 % 10 = 2`. `rev` becomes `1 * 10 + 2 = 12`. `x` becomes `1`. |
| Loop 3 | `0` | `121` | `121` | Extract `1 % 10 = 1`. `rev` becomes `12 * 10 + 1 = 121`. `x` becomes `0`. |
| End | `0` | `121` | `121` | Loop ends because `x == 0`. `rev == n` (`121 == 121`), so return `true`. |

### Case 2: Positive non-palindrome (`x = 10`)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Initial | `10` | `10` | `0` | `x >= 0`, proceed to copy `x` into `n`. |
| Loop 1 | `1` | `10` | `0` | Extract `10 % 10 = 0`. `rev` becomes `0 * 10 + 0 = 0`. `x` becomes `1`. |
| Loop 2 | `0` | `10` | `1` | Extract `1 % 10 = 1`. `rev` becomes `0 * 10 + 1 = 1`. `x` becomes `0`. |
| End | `0` | `10` | `1` | Loop ends because `x == 0`. `rev != n` (`1 != 10`), so return `false`. |

## Time & Space Complexity

* **Time Complexity:** O(log10(x)) — In each step of the loop, we divide `x` by 10. The number of iterations equals the total number of digits in `x`, which is roughly log10(x).
* **Space Complexity:** O(1) — Uses a constant amount of extra memory (`n` and `rev`).

### Optimization

Can this solution be improved? **Yes, slightly.**

While the current algorithm runs quickly, it reverses the entire number and relies on a 64-bit integer (`long`) to prevent arithmetic overflow (when a reversed number exceeds standard 32-bit integer limits).

We can optimize this by **reversing only half of the number**. 

Since a palindrome is symmetric, the second half of the number reversed should equal the first half. We can stop reversing digits as soon as the reversed number `rev` becomes greater than or equal to the remaining half of `x`.

This approach:
1. Cuts the number of loop steps in half.
2. Completely avoids memory overflow without needing a 64-bit `long` type.

Here is the key updated logic:

```cpp
// Negative numbers or numbers ending in 0 (except 0 itself) cannot be palindromes
if (x < 0 || (x % 10 == 0 && x != 0)) {
    return false;
}

int rev = 0;
// Stop when we reach or cross the middle of the number
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

// For even length digits: x == rev (e.g., 1221 -> x = 12, rev = 12)
// For odd length digits: x == rev / 10 (e.g., 12321 -> x = 12, rev = 123)
return x == rev || x == rev / 10;
```

* `if (x < 0 || (x % 10 == 0 && x != 0))`: Any non-zero number ending in `0` cannot be a palindrome because no positive number starts with `0`.
* `while (x > rev)`: Loop stops right when `rev` gains as many (or more) digits as `x`.
* `return x == rev || x == rev / 10;`: For odd-length numbers, `rev / 10` discards the middle digit which does not affect palindrome balance.

### Resulting Complexities

* **Improved Time Complexity:** O(log10(x)) — We only process half the digits.
* **Improved Space Complexity:** O(1) — Standard 32-bit integers are used with zero risk of overflow.
* **Theoretical Best:** O(log10(x)) time and O(1) space. The half-reversal method reaches this absolute limit because you must inspect at least half the digits to verify symmetry.

## Edge Cases Handled

* **Negative numbers (e.g., `-121`):** Handled immediately at the beginning (`x < 0`), returning `false`.
* **Single-digit numbers (e.g., `0` through `9`):** Handled correctly because the loop runs once and returns `true`.
* **Numbers ending with zero (e.g., `10`, `100`):** Reverses correctly to `1` which does not equal the original number, returning `false`.
* **Large numbers near maximum integer limit:** Uses `long` to store the reversed value, preventing integer overflow errors.
