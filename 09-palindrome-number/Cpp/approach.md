![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.01%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

This problem asks us to look at an integer number (let's call it x) and decide if it is a **palindrome**. 

A palindrome is something that reads the exact same way forwards and backwards. For words, think of "radar" or "noon". For numbers, think of 121 or 12321. 

If we reverse the digits of a palindrome number, the new number looks identical to the original one. The problem wants us to return true if the number is a palindrome, and false if it is not. Negative numbers like -121 are never palindromes because the minus sign ends up at the back (121-) when reversed.

## Intuition

The core idea is to **reverse the integer** mathematically and then compare the reversed version to the original. 

If we take a number like 121, we can peel off its digits one by one from the right side using basic math (division and remainder). As we peel them off, we build a new number that is the reverse of the original. 

There is one important trap: reversing a number can sometimes make it grow too large to fit inside a standard integer container. To prevent this overflow bug, we store our reversed number inside a larger data type (a `long` instead of an `int`). Once the loop finishes, we just check if our reversed number matches our original number.

## Approach

Here is the step-by-step breakdown of how the code works:

- `if( x < 0)`: Checks if the input number is negative. If it is, it immediately returns false because negative numbers can never be palindromes due to the trailing minus sign.
- `long n = x;`: Saves a copy of the original input number into a larger variable named `n` so we can compare it later, since our original variable `x` will be destroyed during the loop.
- `long rev = 0 ;`: Creates a variable named `rev` and sets it to zero. This variable will hold our reversed number as we build it.
- `while ( x != 0)`: Starts a loop that keeps running as long as `x` still has digits left to process.
- `rev = rev * 10 + x % 10;`: Takes our current `rev` value, multiplies it by 10 to shift all existing digits one place to the left, and adds the last digit of `x` (found using `x % 10`) to the end.
- `x /= 10;`: Cuts off the last digit of `x` by dividing it by 10, shrinking `x` so we can process the next digit in the next loop pass.
- `if( rev == n )`: Compares our fully built reversed number (`rev`) against our saved original number (`n`).
- `return true;`: Returns true if both numbers match, meaning the input was a palindrome.
- `return false;`: Returns false if the numbers do not match.

## Dry Run

### Case 1: Typical case (x = 121)

| Step | x | rev | Action |
| --- | --- | --- | --- |
| Start | 121 | 0 | Save `n = 121`, enter loop. |
| Loop 1 | 12 | 1 | `rev` becomes `0 * 10 + 1 = 1`. `x` becomes `121 / 10 = 12`. |
| Loop 2 | 1 | 12 | `rev` becomes `1 * 10 + 2 = 12`. `x` becomes `12 / 10 = 1`. |
| Loop 3 | 0 | 121 | `rev` becomes `12 * 10 + 1 = 121`. `x` becomes `1 / 10 = 0`. Loop ends. |
| Compare | 0 | 121 | `rev` equals `n` (121 equals 121). Returns `true`. |

### Case 2: Negative number (x = -121)

| Step | x | rev | Action |
| --- | --- | --- | --- |
| Start | -121 | 0 | Hits `if(x < 0)` check right away. Returns `false` immediately. |

## Time & Space Complexity

- **Time:** O(log 10 of x) — The code runs a loop that divides the number by 10 on every single step. The number of steps is proportional to the number of digits in x, which grows logarithmically with the size of x.
- **Space:** O(1) — We only use a few extra variables (`n` and `rev`), taking up a constant amount of memory no matter how large the input number is.

**Is this already the most optimal possible complexity, or can it be improved?**

This code is already optimal in terms of Big-O time and space complexity. 

However, there is a minor mathematical optimization often used for this problem: instead of reversing the *entire* number, you can reverse just the *half* of the number. Once your reversed half is greater than or equal to the remaining part of `x`, you stop the loop. This avoids the need for a `long` variable because it prevents integer overflow entirely, and it saves a bit of CPU time for very large numbers. 

Here is what that optimized loop looks like:

```cpp
int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}
return (x == rev || x == rev / 10);
```

- `while (x > rev)`: Stops the loop halfway through the number instead of going all the way to zero.
- `x == rev`: Handles numbers with an even number of digits (like 1221, where `x` becomes 12 and `rev` becomes 12).
- `x == rev / 10`: Handles numbers with an odd number of digits (like 121, where `x` becomes 1 and `rev` becomes 12, so dividing `rev` by 10 strips out the middle digit for a clean comparison).

- **Resulting improved complexity:** Still O(log n) time and O(1) space, but with half the loop iterations and no need for `long` data types.
- **Theoretical best possible complexity:** O(log n) time and O(1) space, because you must inspect every digit of the number at least once to know if it is a palindrome. This solution reaches that absolute limit.

## Edge Cases Handled

- **Negative numbers:** Handled by the very first guard clause, instantly returning false.
- **Single-digit numbers (0 through 9):** Handled correctly because the loop runs once, `rev` matches `n`, and it returns true.
- **Numbers ending in zero (like 10):** Handled correctly because 10 reverses to 01 (which is 1), and 1 does not equal 10, returning false.
- **Large boundary numbers:** Handled safely without crashing because the use of `long` prevents integer overflow when reversing numbers near the maximum size limit of standard 32-bit integers.
