![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.03%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

A **palindrome** is a number (or a word) that reads the exact same way forward and backward. For example, the number 121 is a palindrome because reading it from left to right gives 121, and reading it from right to left also gives 121. 

This problem asks us to look at any given whole number (called an integer) and determine if it is a palindrome. If it is, we return true. If it is not, we return false. 

Negative numbers like -121 are never palindromes. When read backward, the minus sign ends up at the right end (like 121-), which does not match the original number.

## Intuition

The core idea behind this solution is to physically reverse the digits of the number using simple math, and then compare the reversed version to the original number. If they match, it is a palindrome.

To reverse a number mathematically, we can peel off its last digit one by one using the remainder operator (percent sign). Each time we pull off a digit, we add it to our running reversed number, shifting the existing digits of our reversed number one place to the left by multiplying it by 10. 

Because reversing a number can sometimes make it grow larger than the maximum limit of a standard whole number, we store our copies inside a larger data type called `long` to prevent crashing.

## Approach

* `if( x < 0)`: Checks if the input number is negative. Since negative numbers cannot be palindromes because of the trailing minus sign, the code immediately returns false and stops running.
* `long n = x;`: Saves the original value of `x` into a larger variable called `n` so we can compare it later, because our main `x` variable will be completely destroyed as we peel away its digits.
* `long rev = 0 ;`: Creates a variable called `rev` to hold our reversed number, starting it at zero.
* `while ( x != 0)`: Starts a loop that keeps running as long as `x` still has digits left to process.
* `rev = rev * 10 + x % 10;`: Takes our current `rev` value, multiplies it by 10 to shift all existing digits one place to the left, and adds the last digit of `x` (found using `x % 10`) onto the end.
* `x /= 10;`: Removes the last digit from `x` by dividing it by 10 and throwing away any decimal remainder, shrinking `x` closer to zero.
* `if( rev == n )`: Compares our fully built reversed number against the original saved number `n` after the loop finishes.
* `return true;`: Sends back true if the numbers match, meaning the input is a palindrome.
* `return false;`: Sends back false if the numbers do not match, meaning the input is not a palindrome.

## Dry Run

### Case 1: Typical palindrome case (x = 121)

| `x` (start of loop) | `x % 10` (last digit) | `rev` (updated) | `x` (after division) | Action |
| :--- | :--- | :--- | :--- | :--- |
| 121 | 1 | 1 | 12 | Loop 1: Pulls off 1, shifts `rev`, shrinks `x` |
| 12 | 2 | 12 | 1 | Loop 2: Pulls off 2, shifts `rev`, shrinks `x` |
| 1 | 1 | 121 | 0 | Loop 3: Pulls off 1, shifts `rev`, shrinks `x` |

*After loop ends:* `rev` is 121 and `n` is 121. They match, so the function returns true.

### Case 2: Negative number edge case (x = -121)

| `x` (start of loop) | `x % 10` (last digit) | `rev` (updated) | `x` (after division) | Action |
| :--- | :--- | :--- | :--- | :--- |
| -121 | N/A | N/A | N/A | Initial guard catches `x < 0` instantly |

*After guard check:* The function immediately returns false without entering the loop.

## Time & Space Complexity

* **Time:** O(d) where d is the number of digits in the integer. The while loop runs exactly once for every single digit in the number. Because any standard integer has a fixed maximum number of digits (at most 10 digits for 32-bit integers), this runs in constant time relative to the value of x, commonly simplified as O(1).
* **Space:** O(1) — constant space. The code only creates a couple of extra variables (`n` and `rev`), and the amount of memory used never grows no matter how large the input number is.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this code is already optimal. 

The time complexity is O(1) because integers have a hard size limit, and the space complexity is O(1) because we use a fixed number of variables. It is impossible to go faster than looking at the digits, and it is impossible to use less extra memory. No further improvements are possible.

## Edge Cases Handled

* **Negative numbers:** Handled right at the beginning by the guard clause returning false, since numbers like -121 cannot be palindromes.
* **Single-digit numbers:** Numbers from 0 to 9 loop only once, compare successfully to themselves, and return true.
* **Numbers ending in zero:** Numbers like 10 fail because reversing 10 gives 01 (which evaluates to 1), and 1 does not equal 10. The loop finishes safely without dividing by zero.
* **Large numbers near maximum limits:** Handled safely because the code uses `long` variables, preventing data overflow errors when the reversed number gets large.
