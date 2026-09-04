![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.29%25)-yellow?style=for-the-badge)

---

## Problem Explained

The goal is to take a 32-bit signed integer `x` and flip its digits in reverse order.

For example:
* If `x = 123`, reversing the digits gives `321`.
* If `x = -123`, keeping the negative sign gives `-321`.
* If `x = 120`, dropping the front zero after flipping gives `21`.

There is a major constraint: signed 32-bit integers can only store numbers between `-2^31` (-2,147,483,648) and `2^31 - 1` (2,147,483,647). If reversing `x` results in a number outside this range, the function must return `0`. You are not allowed to use 64-bit integers (like `long long` in C++) to temporarily store larger numbers.

---

## Intuition

To reverse a number without converting it into a string, you can pop digits off the back of `x` one by one and push them onto a new number.

1. **Pop the last digit:** Taking `x % 10` gives you the last digit.
2. **Remove the last digit:** Integer division `x / 10` strips that last digit away.
3. **Push onto the result:** Multiply your running total by `10` and add the popped digit.

The key challenge is predicting **integer overflow** before it actually happens. Since multiplying by 10 could instantly overflow a 32-bit integer, you must check your running total *before* doing the multiplication. If your running total is already larger than `INT_MAX / 10` or smaller than `INT_MIN / 10`, multiplying it by 10 will exceed the 32-bit limit, so you must stop and return `0`.

---

## Approach

Here is how the code builds the reversed number step-by-step:

* `int check = 0;`: Creates an integer variable `check` set to `0`. This variable will hold our running reversed result.
* `while( x != 0){`: Starts a loop that keeps running as long as `x` still has digits remaining to process.
* `if( check > INT_MAX/10 || check < INT_MIN/10){`: Checks if the current value of `check` is too large or too small. If `check` exceeds `214748364` or drops below `-214748364`, multiplying by 10 on the next line would overflow a 32-bit signed integer.
* `return 0;`: Immediately exits and returns `0` if an overflow condition was detected.
* `check = check *10 + x % 10 ;`: Extracts the last digit of `x` using `x % 10`, shifts the current digits in `check` left by multiplying by 10, and adds the extracted digit.
* `x /= 10 ;`: Removes the last digit from `x` by performing integer division by 10.
* `return check;`: Returns the final reversed integer `check` after all digits of `x` have been processed.

---

## Dry Run

### Case 1: Standard positive number (`x = 123`)

| Step | `x` | `check` | Action |
| --- | --- | --- | --- |
| Start | 123 | 0 | `check` is within bounds. Append `123 % 10` (3). `check` becomes 3. Divide `x` by 10 (`x` = 12). |
| Step 1 | 12 | 3 | `check` is within bounds. Append `12 % 10` (2). `check` becomes 32. Divide `x` by 10 (`x` = 1). |
| Step 2 | 1 | 32 | `check` is within bounds. Append `1 % 10` (1). `check` becomes 321. Divide `x` by 10 (`x` = 0). |
| End | 0 | 321 | Loop ends because `x == 0`. Return `321`. |

### Case 2: Positive number with trailing zero (`x = 120`)

| Step | `x` | `check` | Action |
| --- | --- | --- | --- |
| Start | 120 | 0 | `check` is within bounds. Append `120 % 10` (0). `check` becomes 0. Divide `x` by 10 (`x` = 12). |
| Step 1 | 12 | 0 | `check` is within bounds. Append `12 % 10` (2). `check` becomes 2. Divide `x` by 10 (`x` = 1). |
| Step 2 | 1 | 2 | `check` is within bounds. Append `1 % 10` (1). `check` becomes 21. Divide `x` by 10 (`x` = 0). |
| End | 0 | 21 | Loop ends because `x == 0`. Return `21`. |

---

## Time & Space Complexity

* **Time:** O(1) — A 32-bit integer has at most 10 digits. The `while` loop runs at most 10 times regardless of the input size. This bounded work means the time complexity is strictly constant O(1).
* **Space:** O(1) — The solution only allocates a single integer variable (`check`), using a constant amount of memory.

**Is this optimal?**
Yes, this code is already fully optimal. You must inspect every digit of `x` at least once to reverse it, which takes at most 10 operations. You cannot use fewer than zero extra helper structures. No further optimizations in time or space are possible.

---

## Edge Cases Handled

* **Negative Numbers:** C++ preserves the sign during modulo and division operations (for example, `-123 % 10` is `-3` and `-123 / 10` is `-12`). The exact same logic works seamlessly for negative values without needing extra `if` statements.
* **Trailing Zeros:** Numbers like `120` process the tailing zero first, setting `check` to `0`. Subsequent non-zero digits convert `check` to `2` and then `21`, correctly stripping leading zeros from the final result.
* **Overflow / Boundary Exceeded:** Large inputs like `1534236469` would reverse to `9646324351`, which exceeds `2^31 - 1`. The guard clause `check > INT_MAX / 10` catches this overflow attempt before it happens and safely returns `0`.
* **Single-Digit Numbers & Zero:** Inputs like `0` or `7` run the loop at most once and immediately return the input itself without triggering any false overflow flags.
