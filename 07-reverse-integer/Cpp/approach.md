![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.21%25)-yellow?style=for-the-badge)

---

## Problem Explained

The problem asks you to take a 32-bit signed integer `x` and reverse its digits. 

Here are the main rules:
* If `x` is positive, the reversed number stays positive (for example, `123` becomes `321`).
* If `x` is negative, the negative sign stays at the front (for example, `-123` becomes `-321`).
* Trailing zeros disappear when reversed because numbers cannot start with zero (for example, `120` becomes `21`).
* A 32-bit signed integer can only store values between `-2^31` (-2,147,483,648) and `2^31 - 1` (2,147,483,647). If reversing the digits creates a number outside this range, you must return `0`.
* You are **not allowed** to use 64-bit integers (like `long long` in C++) to temporarily store larger numbers. Everything must be checked using 32-bit integers.

## Intuition

To reverse a number without converting it into a string, you can pluck digits off the end one by one using basic math:
1. `x % 10` gets the last digit of `x`.
2. `check * 10 + digit` adds that digit to the end of your new reversed number.
3. `x / 10` removes the last digit from `x`.

The key challenge is preventing **overflow** (when a number becomes too large to fit in a 32-bit integer limit). 

Since we multiply our result (`check`) by `10` in every step, we must check if `check * 10` will break the allowed limit **before** we perform the multiplication. 

If `check` is already greater than `INT_MAX / 10` (the maximum limit divided by 10), multiplying it by 10 will definitely overflow. The same logic applies to negative numbers: if `check` is less than `INT_MIN / 10`, multiplying it by 10 will underflow.

## Approach

* `int check = 0;`: Initialize a variable named `check` to `0`. This variable will build and store our reversed integer.
* `while( x != 0)`: Start a loop that runs as long as there are still digits left in `x` to process.
* `if( check > INT_MAX/10 || check < INT_MIN/10)`: Check if multiplying `check` by `10` in the next step would go past the 32-bit bounds. **INT_MAX** is the largest allowed integer (2,147,483,647) and **INT_MIN** is the smallest (-2,147,483,648).
* `return 0;`: If an overflow or underflow is about to happen, stop immediately and return `0`.
* `check = check *10 + x % 10 ;`: Shift the existing digits in `check` left by one place (by multiplying by 10) and append the last digit of `x` (obtained using `x % 10`).
* `x /= 10 ;`: Remove the last digit from `x` using integer division by 10.
* `return check;`: Once `x` becomes `0`, exit the loop and return the complete reversed integer stored in `check`.

## Dry Run

### Case 1: Typical positive number (`x = 123`)

| Step | x | check | Condition (x != 0) | Overflow Check | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 123 | 0 | True | Safe (`0` is within limits) | `check = 0 * 10 + 3` -> `3`. `x` becomes `12`. |
| 2 | 12 | 3 | True | Safe (`3` is within limits) | `check = 3 * 10 + 2` -> `32`. `x` becomes `1`. |
| 3 | 1 | 32 | True | Safe (`32` is within limits) | `check = 32 * 10 + 1` -> `321`. `x` becomes `0`. |
| 4 | 0 | 321 | False | N/A | Loop ends. Return `check` (`321`). |

### Case 2: Number with trailing zeros (`x = 120`)

| Step | x | check | Condition (x != 0) | Overflow Check | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 120 | 0 | True | Safe (`0` is within limits) | `check = 0 * 10 + 0` -> `0`. `x` becomes `12`. |
| 2 | 12 | 0 | True | Safe (`0` is within limits) | `check = 0 * 10 + 2` -> `2`. `x` becomes `1`. |
| 3 | 1 | 2 | True | Safe (`2` is within limits) | `check = 2 * 10 + 1` -> `21`. `x` becomes `0`. |
| 4 | 0 | 21 | False | N/A | Loop ends. Return `check` (`21`). |

## Time & Space Complexity

**Time Complexity:** O(1) — A 32-bit integer has at most 10 digits. The loop will run at most 10 times, regardless of how large `x` is. This means it runs in constant time.

**Space Complexity:** O(1) — The code only uses two integer variables (`x` and `check`). It uses a fixed amount of memory regardless of the input.

**Is this optimal?**
Yes, this code is already fully optimal. You must inspect every digit at least once to reverse a number. Since a 32-bit integer has a fixed maximum of 10 digits, doing this in O(1) time and O(1) space is the theoretical best possible performance.

## Edge Cases Handled

* **Negative Numbers (e.g., `x = -123`):** C++ handles negative modulo arithmetic cleanly (`-123 % 10` evaluates to `-3`). The negative sign is preserved automatically without extra logic.
* **Numbers Ending in Zero (e.g., `x = 120`):** The trailing zero is processed first (`0 * 10 + 0 = 0`), which drops leading zeros naturally from the final output (`21`).
* **Integer Overflow / Underflow:** If reversing a number causes it to exceed `2^31 - 1` or drop below `-2^31`, the condition `check > INT_MAX/10 || check < INT_MIN/10` catches it early and safely returns `0`.
* **Single Digit Numbers & Zero (e.g., `x = 0` or `x = 7`):** The loop runs at most once and returns the original number correctly.
