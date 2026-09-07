![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.23%25)-yellow?style=for-the-badge)

---

## Problem Explained

The problem asks you to take a 32-bit signed integer `x` and reverse its digits. 

For example:
* If `x = 123`, reversing the digits gives `321`.
* If `x = -123`, the negative sign stays at the front, giving `-321`.
* If `x = 120`, dropping the leading zero in the reversed result gives `21`.

**The main catch:** standard 32-bit signed integers can only hold values from `-2^31` (-2,147,483,648) up to `2^31 - 1` (2,147,483,647). If reversing `x` causes the number to go above or below this range, you must return `0`. You are also explicitly forbidden from using 64-bit integer variables (like `long long` in C++) to hold larger numbers temporarily.

---

## Intuition

To reverse a number without converting it to text, you can pull off its digits one by one from right to left using basic math:

1. **Extract the last digit:** `x % 10` gives you the rightmost digit.
2. **Remove the last digit:** `x / 10` chops off the rightmost digit.
3. **Build the new number:** Multiply your running total by `10` (shifting its digits left) and add the extracted digit.

The core challenge is catching **overflow** before it actually happens. 

If your running answer is already greater than `INT_MAX / 10` (which is `214748364`), multiplying it by 10 will immediately exceed the maximum 32-bit limit (`2147483647`). The same logic applies to negative numbers with `INT_MIN / 10`. By checking this boundary *right before* multiplying by 10, you prevent overflow safely without needing extra memory or bigger variable types.

---

## Approach

* `int check = 0;`: Initialize `check` to `0`. This variable will hold our reversed number as we build it digit by digit.
* `while( x != 0)`: Start a loop that runs as long as `x` still has remaining digits to process.
* `if( check > INT_MAX/10 || check < INT_MIN/10)`: Check if multiplying `check` by 10 in the next step will push it beyond the allowable 32-bit range (`-2147483648` to `2147483647`).
* `return 0;`: If `check` is already past those safety thresholds, multiplying by 10 would cause an overflow or underflow, so stop immediately and return `0`.
* `check = check *10 + x % 10 ;`: Shift the existing digits in `check` one position to the left (by multiplying by 10), then append the rightmost digit of `x` (obtained via `x % 10`).
* `x /= 10 ;`: Chop off the rightmost digit of `x` using integer division so the next iteration can process the next digit.
* `return check;`: Once all digits are processed and `x` becomes `0`, return the finalized reversed number.

---

## Dry Run

### Case 1: Typical positive integer (x = 123)

| Step | `x` | `check` | Action |
| --- | --- | --- | --- |
| Start | `123` | `0` | Loop condition `123 != 0` is true. `0` is within bounds. |
| 1 | `12` | `3` | `123 % 10` gives `3`. `check` becomes `0 * 10 + 3 = 3`. `x` becomes `12`. |
| 2 | `1` | `32` | `12 % 10` gives `2`. `check` becomes `3 * 10 + 2 = 32`. `x` becomes `1`. |
| 3 | `0` | `321` | `1 % 10` gives `1`. `check` becomes `32 * 10 + 1 = 321`. `x` becomes `0`. |
| End | `0` | `321` | Loop ends because `x == 0`. Returns `321`. |

### Case 2: Negative integer (x = -123)

| Step | `x` | `check` | Action |
| --- | --- | --- | --- |
| Start | `-123` | `0` | Loop condition `-123 != 0` is true. `0` is within bounds. |
| 1 | `-12` | `-3` | `-123 % 10` gives `-3`. `check` becomes `0 * 10 + (-3) = -3`. `x` becomes `-12`. |
| 2 | `-1` | `-32` | `-12 % 10` gives `-2`. `check` becomes `-3 * 10 + (-2) = -32`. `x` becomes `-1`. |
| 3 | `0` | `-321` | `-1 % 10` gives `-1`. `check` becomes `-32 * 10 + (-1) = -321`. `x` becomes `0`. |
| End | `0` | `-321` | Loop ends because `x == 0`. Returns `-321`. |

---

## Time & Space Complexity

* **Time:** **O(1)** (Constant Time) — The loop runs once per digit. Since a standard 32-bit signed integer has at most 10 digits, the loop runs 10 times at most, regardless of the input value.
* **Space:** **O(1)** (Constant Space) — Only one extra variable (`check`) is stored in memory, requiring a tiny, fixed amount of space.

### Is this solution optimal?

**Yes, this code is already optimal.** 

To reverse an integer, you must examine every digit at least once, which takes time proportional to the number of digits (at most 10 iterations). Storing a single variable takes minimum memory. Neither the time nor space complexity can be improved further.

---

## Edge Cases Handled

* **Negative Numbers:** In C++, the modulo operator `%` preserves the negative sign (for example, `-123 % 10` is `-3`). The code processes negative numbers naturally without needing special negative sign handling.
* **Numbers Ending in Zero:** Inputs like `120` leave a trailing zero when reversed (`021`). Because we build `check` mathematically (`0 * 10 + 2 = 2`), leading zeros are naturally dropped, correctly producing `21`.
* **Overflow / Underflow Limits:** Inputs that reverse into numbers larger than `2147483647` or smaller than `-2147483648` (such as `1534236469`) hit the boundary guard `check > INT_MAX / 10` and safely return `0`.
* **Single Digit & Zero:** Inputs like `0` or `7` immediately complete their loop and return the input unchanged.
