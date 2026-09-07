![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.21%25)-yellow?style=for-the-badge)

---

## Problem Explained

Imagine you have a regular whole number, like 123. The problem asks you to flip its digits backward so it becomes 321. 

If the number is negative, like -123, the negative sign stays in front, and the digits flip to become -321. 

There is an important safety rule: standard 32-bit computers can only hold whole numbers roughly between -2,147,483,648 and 2,147,483,647. If flipping the digits makes the number grow too big or too small to fit in that range, you must give up and return 0 instead. Also, you are not allowed to cheat by using extra-large 64-bit containers to catch those oversized numbers.

## Intuition

The core trick is peeling off the last digit of the number one by one, using basic math, and building the reversed number from scratch. 

Think about how you strip digits off a number in real life. If you take 123, you can use the remainder operator (percent sign) to grab the last digit (3). Then you divide the number by 10 to shrink it (leaving 12). 

To build the reverse, you take your growing answer, multiply it by 10 to shift all its current digits one spot to the left, and then drop that new digit onto the end. 

The "aha" moment for handling the size limit without a 64-bit container is stopping *before* you overflow. If your current built number is already bigger than 214,748,364 (which is INT_MAX / 10), multiplying it by 10 in the next step will definitely blast past the limit. Checking this ahead of time keeps you safe and legal.

## Approach

* `int check = 0;`: Creates a variable named **check** starting at 0. This variable will hold our reversed number as we build it digit by digit.
* `while( x != 0)`: Starts a loop that keeps running as long as **x** still has digits left to process.
* `if( check > INT_MAX/10 || check < INT_MIN/10)`: Checks if our running number **check** is already too large or too small. If it is, multiplying it by 10 on the next line would crash our integer limits, so we immediately return 0.
* `check = check *10 + x % 10 ;`: Multiplies our current **check** by 10 to make room, then grabs the last digit of **x** using `x % 10` and adds it to the end.
* `x /= 10 ;`: Divides **x** by 10 to chop off the last digit we just used, moving us on to the next digit.
* `return check;`: Sends back our finished, fully reversed number once the loop finishes.

## Dry Run

### Case 1: Typical case

Input: x = 123

| x | check | Action |
|---|---|---|
| 123 | 0 | Loop starts. check is safe. check becomes `0 * 10 + 3` (3). x becomes 12. |
| 12 | 3 | check is safe. check becomes `3 * 10 + 2` (32). x becomes 1. |
| 1 | 32 | check is safe. check becomes `32 * 10 + 1` (321). x becomes 0. |
| 0 | 321 | Loop ends because x is 0. Returns 321. |

### Case 2: Negative number with trailing zero

Input: x = -120

| x | check | Action |
|---|---|---|
| -120 | 0 | Loop starts. check is safe. check becomes `0 * 10 + (-120 % 10)` (-0). x becomes -12. |
| -12 | 0 | check is safe. check becomes `0 * 10 + (-12 % 10)` (-2). x becomes -1. |
| -1 | -2 | check is safe. check becomes `-2 * 10 + (-1 % 10)` (-21). x becomes 0. |
| 0 | -21 | Loop ends because x is 0. Returns -21. |

## Time & Space Complexity

**Time:** O(log(x)) — The number of steps depends directly on how many digits are in **x**. Since a 32-bit integer has a maximum of 10 digits, the loop runs at most 10 times, making it effectively constant time. 
**Space:** O(1) — We only use a couple of standard variables (**check**), which takes up a fixed, tiny amount of memory no matter how large **x** is.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this code is already fully optimal. It runs in minimal time (touching each digit only once) and uses constant space. No further improvement is possible.

## Edge Cases Handled

* **Negative numbers:** The modulo operator in C++ keeps the negative sign attached to the remainder (e.g., `-123 % 10` is `-3`), which naturally carries negative values through the reversal process.
* **Trailing zeros:** Numbers ending in zero (like 120) have their trailing zero moved to the front during math operations (becoming 021, which evaluates as 21).
* **Boundary overflow near the limit:** The pre-check against `INT_MAX / 10` and `INT_MIN / 10` successfully catches numbers that are about to exceed 32-bit limits *before* the overflow actually happens.
* **Single-digit numbers:** Numbers between -9 and 9 skip the loop logic or run it just once, cleanly returning themselves.
