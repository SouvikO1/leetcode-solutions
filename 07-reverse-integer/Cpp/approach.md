![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.22%25)-yellow?style=for-the-badge)

---

## Problem Explained

The goal is to take a 32-bit signed integer `x` and reverse its digits. 

For example:
* If `x = 123`, the output is `321`.
* If `x = -123`, the output is `-321`.
* If `x = 120`, reversing the digits gives `021`, which simplifies to `21`.

There is a key constraint: if reversing the digits produces a number that falls outside the 32-bit signed integer range (from `-2^31` to `2^31 - 1`, or `-2147483648` to `2147483647`), you must return `0`. You are also not allowed to store 64-bit integers (`long long` in C++) to temporarily hold larger numbers. Everything must be checked within standard 32-bit integer limits.

## Intuition

To reverse a number mathematically, we peel off its last digit one by one and attach it to a new growing number. 

1. We get the last digit of `x` using remainder: `x % 10`.
2. We add this digit to our answer `check` after shifting `check`'s existing digits one place to the left: `check = check * 10 + digit`.
3. We remove the last digit from `x` using division: `x = x / 10`.

The main hurdle is preventing **integer overflow** (when a calculation becomes too large to fit in a 32-bit integer) *before* it happens. 

If `check` is already greater than `INT_MAX / 10` (which is `214748364`), multiplying `check` by 10 will immediately exceed `INT_MAX` (`2147483647`). The same logic applies to negative numbers with `INT_MIN / 10`. By checking `check > INT_MAX / 10` or `check < INT_MIN / 10` before multiplying, we safely stop and return `0` if an overflow is about to happen.

## Approach

* `int check = 0;`: Creates a variable named `check` set to `0`. This variable will build and store our reversed integer.
* `while( x != 0){`: Starts a loop that continues as long as `x` still has digits left to process. This works for both positive and negative inputs.
* `if( check > INT_MAX/10 || check < INT_MIN/10){ return 0; }`: Checks whether multiplying `check` by 10 in the next step will go outside the allowed 32-bit range. If so, it immediately returns `0`.
* `check = check *10 + x % 10 ;`: Shifts the existing digits in `check` to the left by multiplying by 10, then appends the last digit of `x` (extracted via `x % 10`).
* `x /= 10 ;`: Removes the last digit from `x` by dividing it by 10.
* `return check;`: Returns the final reversed integer after all digits of `x` have been moved to `check`.

## Dry Run

### Case 1: Negative number (`x = -123`)

| Step | `x` | `check` | Action |
| :--- | :--- | :--- | :--- |
| Start | `-123` | `0` | Loop starts since `x != 0`. |
| Loop 1 | `-12` | `-3` | Check passed (`0`). `check = 0 * 10 + (-3) = -3`. `x = -123 / 10 = -12`. |
| Loop 2 | `-1` | `-32` | Check passed (`-3`). `check = -3 * 10 + (-2) = -32`. `x = -12 / 10 = -1`. |
| Loop 3 | `0` | `-321` | Check passed (`-32`). `check = -32 * 10 + (-1) = -321`. `x = -1 / 10 = 0`. |
| End | `0` | `-321` | Loop ends (`x == 0`). Returns `-321`. |

### Case 2: Number with trailing zero (`x = 120`)

| Step | `x` | `check` | Action |
| :--- | :--- | :--- | :--- |
| Start | `120` | `0` | Loop starts since `x != 0`. |
| Loop 1 | `12` | `0` | Check passed (`0`). `check = 0 * 10 + 0 = 0`. `x = 120 / 10 = 12`. |
| Loop 2 | `1` | `2` | Check passed (`0`). `check = 0 * 10 + 2 = 2`. `x = 12 / 10 = 1`. |
| Loop 3 | `0` | `21` | Check passed (`2`). `check = 2 * 10 + 1 = 21`. `x = 1 / 10 = 0`. |
| End | `0` | `21` | Loop ends (`x == 0`). Returns `21`. |

## Time & Space Complexity

* **Time:** O(log10(x)) or O(1) — The number of loop iterations depends on the number of digits in `x`. Since a signed 32-bit integer has at most 10 digits, the loop runs at most 10 times.
* **Space:** O(1) — We only use a single integer variable (`check`) to store the result, requiring a constant amount of memory.

**Is this already the most optimal solution?**
Yes, this code is already fully optimal. Any algorithm that reads digits must look at each digit at least once, taking O(1) time (since digits are capped at 10) and O(1) space. No further optimizations in time or space complexity are possible.

## Edge Cases Handled

* **Negative Numbers (e.g., `x = -123`):** C++ handles modulo `%` and division `/` on negative numbers by keeping the negative sign. For example, `-123 % 10` gives `-3`, and `-123 / 10` gives `-12`. The logic handles negative numbers naturally without needing special cases.
* **Trailing Zeros (e.g., `x = 120`):** Reversing `120` gives `021`. The first iteration adds `0` to `check`. The second iteration multiplies `0 * 10` and adds `2`, cleanly dropping the unnecessary leading zero.
* **Overflow Outside Bounds (e.g., `x = 1534236469`):** Reversing this number produces `9646324351`, which is larger than `INT_MAX` (`2147483647`). The condition `check > INT_MAX/10` catches this on the 9th digit and returns `0` before an actual overflow crash happens.
* **Single-Digit Numbers and Zero (e.g., `x = 0` or `x = 7`):** The loop handles `0` by skipping immediately and returning `0`. Single digits run once and return the digit itself.
