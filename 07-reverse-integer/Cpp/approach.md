![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.22%25)-yellow?style=for-the-badge)

---

## Problem Explained

The problem asks you to take a 32-bit signed integer `x` and turn its digits around in reverse order. 

For example:
* If `x = 123`, reversing the digits gives `321`.
* If `x = -123`, the negative sign stays at the front, giving `-321`.
* If `x = 120`, reversing the digits gives `021`, which simplifies to `21`.

There is one major catch: computer memory for a standard 32-bit integer can only hold values from `-2^31` (`-2147483648`) up to `2^31 - 1` (`2147483647`). If reversing `x` results in a number outside this exact range, you must return `0`. You are also strictly forbidden from using larger 64-bit integer types (like `long long` in C++) to temporarily hold the number.

## Intuition

To reverse a number without converting it to a text string, you can pluck digits off the end of `x` one by one and build a new number.

1. **How to pluck the last digit:** Taking `x % 10` gives you the last digit of `x`.
2. **How to append that digit:** Multiplying your reversed result by 10 shifts all existing digits one place to the left, opening up the ones place to add the new digit.
3. **How to remove the last digit:** Integer division `x / 10` removes the last digit from `x`.

**The Overflow Trick:** 
Because we cannot store a number larger than `INT_MAX` (`2147483647`), we must check if our new number will overflow *before* we actually multiply by 10. 

Instead of checking `check * 10 > INT_MAX` (which would overflow and crash or wrap around if true), we divide both sides by 10 and check `check > INT_MAX / 10`. If `check` is already larger than `INT_MAX / 10`, multiplying it by 10 will definitely breach the limit. The same logic applies to negative numbers using `INT_MIN / 10`.

## Approach

* `int check = 0;` — Create a variable named `check` and set it to `0`. This variable holds our reversed number as we build it digit by digit.
* `while( x != 0)` — Start a loop that runs until `x` becomes `0`. This strips every digit from `x` from right to left.
* `if( check > INT_MAX/10 || check < INT_MIN/10)` — Check if doing the next math step will push `check` beyond the maximum or minimum 32-bit integer limits. If `check` is already greater than `INT_MAX / 10` or smaller than `INT_MIN / 10`, return `0` immediately to prevent overflow.
* `check = check *10 + x % 10 ;` — Extract the last digit of `x` using `x % 10`, shift `check`'s existing digits one position left by multiplying by 10, and add the new digit to the end.
* `x /= 10 ;` — Divide `x` by 10 to strip off its last digit.
* `return check;` — Once `x` hits `0`, all digits have been reversed. Return `check`.

## Dry Run

### Case 1: Positive number with trailing zero (`x = 120`)

| Step | `x` | `check` | Action |
| :--- | :--- | :--- | :--- |
| Start | `120` | `0` | Initialize `check = 0`. |
| Step 1 | `12` | `0` | Boundary check passes (`0`). Pluck `0` (`120 % 10`). `check` becomes `0 * 10 + 0 = 0`. Divide `x` by 10 (`12`). |
| Step 2 | `1` | `2` | Boundary check passes (`0`). Pluck `2` (`12 % 10`). `check` becomes `0 * 10 + 2 = 2`. Divide `x` by 10 (`1`). |
| Step 3 | `0` | `21` | Boundary check passes (`2`). Pluck `1` (`1 % 10`). `check` becomes `2 * 10 + 1 = 21`. Divide `x` by 10 (`0`). |
| End | `0` | `21` | Loop ends because `x == 0`. Return `21`. |

### Case 2: Negative number (`x = -123`)

| Step | `x` | `check` | Action |
| :--- | :--- | :--- | :--- |
| Start | `-123` | `0` | Initialize `check = 0`. |
| Step 1 | `-12` | `-3` | Boundary check passes (`0`). Pluck `-3` (`-123 % 10`). `check` becomes `0 * 10 + (-3) = -3`. Divide `x` by 10 (`-12`). |
| Step 2 | `-1` | `-32` | Boundary check passes (`-3`). Pluck `-2` (`-12 % 10`). `check` becomes `-3 * 10 + (-2) = -32`. Divide `x` by 10 (`-1`). |
| Step 3 | `0` | `-321` | Boundary check passes (`-32`). Pluck `-1` (`-1 % 10`). `check` becomes `-32 * 10 + (-1) = -321`. Divide `x` by 10 (`0`). |
| End | `0` | `-321` | Loop ends because `x == 0`. Return `-321`. |

## Time & Space Complexity

* **Time Complexity:** **O(1)** (or **O(log10(x))**). A 32-bit integer has at most 10 digits. The `while` loop runs at most 10 times regardless of how large `x` is, which takes constant time.
* **Space Complexity:** **O(1)**. The code uses only two integer variables (`x` and `check`), consuming a fixed amount of memory.

**Is this optimal?**
Yes, this solution is already fully optimal. You must look at every digit at least once to reverse the number, so you cannot use fewer operations. No further performance improvements are possible.

## Edge Cases Handled

* **Negative Numbers:** In C++, the `%` operator on a negative number returns a negative digit (for example, `-123 % 10 = -3`). The logic handles negative signs automatically without needing extra code.
* **Trailing Zeros:** Numbers like `120` naturally drop their leading zero when reversed, outputting `21` because `0 * 10 + 0` stays `0` during the first iteration.
* **Single-Digit Numbers:** Numbers like `7` or `-5` loop once and immediately return themselves.
* **Integer Overflow:** Numbers that would exceed `2147483647` or drop below `-2147483648` when reversed (such as `x = 1534236469`) safely trigger the boundary check and return `0` without causing undefined behavior.
