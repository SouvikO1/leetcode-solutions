![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.46%20MB%20(beats%2083.55%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to take a 32-bit signed integer `x` and reverse its digits. 

For example:
* If `x = 123`, reversing the digits gives `321`.
* If `x = -123`, reversing the digits gives `-321`.
* If `x = 120`, reversing the digits gives `21` ( execution drops the leading zero).

A standard 32-bit signed integer can only store values between **-2^31** (which is `-2,147,483,648`) and **2^31 - 1** (which is `2,147,483,647`). If reversing `x` produces a number larger or smaller than this range, the function must return `0`.

You are not allowed to use 64-bit integer types (like `long long` in C++) to store intermediate results. You must detect overflow using normal 32-bit integers.

---

## Intuition

To reverse a number mathematically without converting it to a string:
1. Extract the last digit of `x` using the modulo operator (`x % 10`).
2. Append that digit to your building answer variable (`check`) by doing `check * 10 + digit`.
3. Remove the last digit from `x` by dividing it by 10 (`x / 10`).
4. Repeat this process until `x` becomes `0`.

The key challenge is preventing overflow before it actually happens. Because multiplying `check` by 10 could overflow the maximum allowed 32-bit limit, you must check `check` **before** performing `check * 10`. If `check` is already greater than `INT_MAX / 10` (or smaller than `INT_MIN / 10`), doing another step will cause an overflow. In that case, safely stop and return `0`.

---

## Approach

Here is how the code works step-by-step:

* `int check = 0;`: Creates a variable named `check` set to `0`. This will store the reversed number as we build it digit by digit.
* `while( x != 0)`: Starts a loop that keeps running as long as there are remaining digits in `x`.
* `if( check > INT_MAX/10 || check < INT_MIN/10)`: Checks if multiplying `check` by 10 in the next line will spill outside the 32-bit integer limits (`INT_MAX` is `2,147,483,647` and `INT_MIN` is `-2,147,483,648`). If `check` is already larger than `214,748,364` or smaller than `-214,748,364`, multiplying by 10 is guaranteed to overflow.
* `return 0;`: Instantly returns `0` if an overflow condition is detected.
* `check = check * 10 + x % 10 ;`: Extracts the last digit of `x` with `x % 10`, shifts the current value of `check` left by one decimal place by multiplying by 10, and adds the extracted digit.
* `x /= 10 ;`: Drops the last digit from `x` using integer division so the next loop iteration can process the next digit.
* `return check;`: Returns the final reversed number after all digits have been processed.

---

## Dry Run

### Case 1: Typical positive input (`x = 123`)

| Step | `x` | `check` | Overflow Check (`check > INT_MAX/10 \|\| check < INT_MIN/10`) | Action |
| --- | --- | --- | --- | --- |
| Start | 123 | 0 | - | Loop begins because `x != 0`. |
| Step 1 | 123 | 0 | False (`0` is safe) | `check = 0 * 10 + (123 % 10) = 3`, then `x = 123 / 10 = 12`. |
| Step 2 | 12 | 3 | False (`3` is safe) | `check = 3 * 10 + (12 % 10) = 32`, then `x = 12 / 10 = 1`. |
| Step 3 | 1 | 32 | False (`32` is safe) | `check = 32 * 10 + (1 % 10) = 321`, then `x = 1 / 10 = 0`. |
| End | 0 | 321 | - | Loop stops because `x == 0`. Function returns `321`. |

### Case 2: Negative input (`x = -123`)

| Step | `x` | `check` | Overflow Check (`check > INT_MAX/10 \|\| check < INT_MIN/10`) | Action |
| --- | --- | --- | --- | --- |
| Start | -123 | 0 | - | Loop begins because `x != 0`. |
| Step 1 | -123 | 0 | False (`0` is safe) | `check = 0 * 10 + (-123 % 10) = -3`, then `x = -123 / 10 = -12`. |
| Step 2 | -12 | -3 | False (`-3` is safe) | `check = -3 * 10 + (-12 % 10) = -32`, then `x = -12 / 10 = -1`. |
| Step 3 | -1 | -32 | False (`-32` is safe) | `check = -32 * 10 + (-1 % 10) = -321`, then `x = -1 / 10 = 0`. |
| End | 0 | -321 | - | Loop stops because `x == 0`. Function returns `-321`. |

---

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — The loop processes one digit per iteration. Since a 32-bit integer has at most 10 digits, the loop runs a maximum of 10 times. This can also be considered **O(1)** time because the runtime is strictly capped by a small constant limit.
* **Space Complexity:** **O(1)** — Memory usage is constant because the code only uses a single integer variable `check` to build the result.

### Is this solution optimal?
**Yes, this code is already fully optimal.**
* You must look at every digit at least once to reverse the number, so you cannot do fewer than `O(log10(x))` operations.
* You are using `O(1)` memory, which is the minimum space possible.
* No further improvements in time or space complexity can be made.

---

## Edge Cases Handled

* **Negative Numbers:** C++ handles negative numbers properly with modulo operations (e.g., `-123 % 10` results in `-3`). The sign naturally carries through without needing separate logic.
* **Numbers Ending in Zero:** Inputs like `x = 120` yield `check = 21`. The trailing zero becomes a leading zero during arithmetic reversal, which automatically vanishes when stored in an integer.
* **Overflow Outside 32-bit Range:** Large inputs like `x = 1,534,236,469` would reverse to `9,646,324,351`, exceeding `INT_MAX`. The overflow guard catches this before `check` overflows and safely returns `0`.
* **Zero Input (`x = 0`):** The loop condition `x != 0` fails instantly, skipping the loop and returning `0` right away.
