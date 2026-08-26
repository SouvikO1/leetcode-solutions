![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.39%20MB%20(beats%2098.16%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to take a 32-bit signed integer `x` and flip its digits backward. 

For example:
* If `x = 123`, reversing the digits gives `321`.
* If `x = -123`, the negative sign stays at the front, giving `-321`.
* If `x = 120`, reversing the digits gives `021`, which simplifies to `21`.

There is one major catch: computer memory for a standard 32-bit integer can only store values between **-2^31** (`-2,147,483,648`) and **2^31 - 1** (`2,147,483,647`). If reversing the digits produces a number outside of this range, the function must return `0`. 

You must solve this without using larger integer types like 64-bit `long long`.

## Intuition

To reverse a number, you need to pull digits off the end of `x` one by one and append them to the front of a new number (`check`).

1. **Extracting the last digit:** Taking `x % 10` gives you the last digit of `x`.
2. **Removing the last digit:** Dividing `x` by `10` (`x / 10`) drops the last digit from `x`.
3. **Building the reversed number:** Multiplying your running total (`check`) by `10` shifts its digits to the left, opening up the ones place to add the new digit: `check = check * 10 + digit`.

The "aha" moment is handling **integer overflow** without using 64-bit numbers. Before you multiply `check` by `10`, you must check if doing so will breach the 32-bit limit. 

If `check` is already greater than `INT_MAX / 10` (which is `214,748,364`), multiplying it by `10` will exceed `INT_MAX`. Similarly, if `check` is less than `INT_MIN / 10`, multiplying by `10` will drop below `INT_MIN`. If either condition is true, stop immediately and return `0`.

## Approach

* `int check = 0;` — Create a variable named `check` initialized to `0`. This will store our reversed number as we build it digit by digit.
* `while( x != 0)` — Start a loop that runs as long as `x` still has digits left to process.
* `if( check > INT_MAX/10 || check < INT_MIN/10)` — Check if multiplying `check` by `10` in the next step will cause a 32-bit overflow. If `check` is beyond these limits, returning `0` prevents a crash or invalid memory state.
* `return 0;` — Instantly exit and return `0` if an overflow was detected.
* `check = check *10 + x % 10 ;` — Extract the last digit of `x` using `x % 10`, shift the existing digits in `check` one place to the left by multiplying by `10`, and add the extracted digit to `check`.
* `x /= 10 ;` — Remove the last digit from `x` by dividing it by `10`.
* `return check;` — Once `x` becomes `0`, all digits have been reversed. Return the completed number `check`.

## Dry Run

### Case 1: Standard positive integer (`x = 123`)

| Step | `x` | `check` | Action |
| :--- | :--- | :--- | :--- |
| Start | `123` | `0` | Initialize `check = 0`. Enter loop (`x != 0`). |
| 1 | `123` | `0` | `check` is within bounds. `check = 0 * 10 + (123 % 10)` -> `3`. `x = 123 / 10` -> `12`. |
| 2 | `12` | `3` | `check` is within bounds. `check = 3 * 10 + (12 % 10)` -> `32`. `x = 12 / 10` -> `1`. |
| 3 | `1` | `32` | `check` is within bounds. `check = 32 * 10 + (1 % 10)` -> `321`. `x = 1 / 10` -> `0`. |
| End | `0` | `321` | Loop terminates (`x == 0`). Return `check` (`321`). |

### Case 2: Negative integer (`x = -123`)

| Step | `x` | `check` | Action |
| :--- | :--- | :--- | :--- |
| Start | `-123` | `0` | Initialize `check = 0`. Enter loop (`x != 0`). |
| 1 | `-123` | `0` | `check` is within bounds. `check = 0 * 10 + (-123 % 10)` -> `-3`. `x = -123 / 10` -> `-12`. |
| 2 | `-12` | `-3` | `check` is within bounds. `check = -3 * 10 + (-12 % 10)` -> `-32`. `x = -12 / 10` -> `-1`. |
| 3 | `-1` | `-32` | `check` is within bounds. `check = -32 * 10 + (-1 % 10)` -> `-321`. `x = -1 / 10` -> `0`. |
| End | `0` | `-321` | Loop terminates (`x == 0`). Return `check` (`-321`). |

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — The number of iterations depends on the number of digits in `x`. Since a signed 32-bit integer has at most 10 digits, the loop runs at most 10 times. This makes the runtime effectively **O(1)** (constant time).
* **Space Complexity:** **O(1)** — Memory usage is constant because the algorithm only uses a single variable `check` to track state.

### Is this solution optimal?

**Yes, this solution is already fully optimal.** 

To reverse an integer, every digit must be inspected at least once, requiring time proportional to the number of digits. Since a 32-bit integer has a fixed maximum length of 10 digits, both the time and space complexity are strictly **O(1)**. No algorithm can process fewer than all the digits of the input, so further improvements in theoretical bounds are impossible.

## Edge Cases Handled

* **Negative Numbers:** In C++, modulo (`%`) and integer division (`/`) preserve signs (e.g., `-123 % 10` equals `-3`). The code naturally builds negative numbers without needing special `if` statements for sign handling.
* **Trailing Zeros:** Numbers like `120` end up as `21`. The first iteration converts `0` into `check = 0`. In the next step, `check = 0 * 10 + 2 = 2`, automatically stripping leading zeros from the final result.
* **32-Bit Overflow:** Input values whose reversed form exceeds `2^31 - 1` or drops below `-2^31` trigger the pre-multiplication limit checks (`INT_MAX/10` and `INT_MIN/10`) and return `0` safely without crashing or causing undefined behavior.
* **Single Digit & Zero:** Inputs like `0` or `7` execute correctly: `0` skips the loop and returns `0`, while single digits run once and return unchanged.
