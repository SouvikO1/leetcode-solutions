![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.01%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The problem asks us to check if a given integer reads the same forwards and backwards. This is called a **palindrome**.

If the number reads identically from left to right and right to left, we return `true`. Otherwise, we return `false`.

* Example 1: `121` reversed is `121`. This is a palindrome, so the answer is `true`.
* Example 2: `-121` reversed is `121-`. The minus sign moves to the end, so it is not a palindrome. The answer is `false`.
* Example 3: `10` reversed is `01` (which is `1`). This is not equal to `10`, so the answer is `false`.

## Intuition

The core trick is to reverse the digits of the number using simple math, then compare the reversed number with the original.

To reverse a number without turning it into a text string:
1. Extract the last digit using the **remainder operation** (`% 10`, which gives the remainder after dividing by 10).
2. Append that digit to a growing reversed total.
3. Remove the last digit from the original number using **integer division** (`/ 10`, which drops any decimal remainder).

Also, any negative number can never be a palindrome because of the leading minus sign. We can reject negative numbers right away.

## Approach

Here is step-by-step how the code works:

* `if( x < 0)`: Checks if the input number is negative. If it is negative, it immediately returns `false` because a minus sign at the front cannot exist at the end.
* `long n = x;`: Saves a copy of the original number `x` into `n`. We use a 64-bit integer type (`long`) to safely store large values and avoid memory overflow if the reversed number gets too big for a standard integer.
* `long rev = 0 ;`: Creates a variable named `rev` to hold the reversed number, initialized to `0`.
* `while ( x != 0)`: Starts a loop that continues until every digit of `x` has been processed and `x` reaches `0`.
* `rev = rev * 10 + x % 10;`: Gets the last digit of `x` using `x % 10`. It shifts the current `rev` number one place to the left (by multiplying by 10) and adds the new digit to the right.
* `x /= 10;`: Cuts off the last digit from `x` using integer division by 10.
* `if( rev == n )`: Compares the fully reversed number `rev` with our stored original number `n`. It returns `true` if they match, and `false` if they do not.

## Dry Run

### Case 1: Standard palindrome (`x = 121`)

| Step | `x` | `n` | `rev` | Action |
| --- | --- | --- | --- | --- |
| Start | `121` | `121` | `0` | `x > 0` is true. Enter loop. |
| Loop 1 | `12` | `121` | `1` | Extract `1` (`121 % 10`). `rev = 0 * 10 + 1`. Chop `x` to `12`. |
| Loop 2 | `1` | `121` | `12` | Extract `2` (`12 % 10`). `rev = 1 * 10 + 2`. Chop `x` to `1`. |
| Loop 3 | `0` | `121` | `121` | Extract `1` (`1 % 10`). `rev = 12 * 10 + 1`. Chop `x` to `0`. |
| End | `0` | `121` | `121` | Loop ends because `x == 0`. `rev == n` (`121 == 121`) is true. Return `true`. |

### Case 2: Number ending in zero (`x = 10`)

| Step | `x` | `n` | `rev` | Action |
| --- | --- | --- | --- | --- |
| Start | `10` | `10` | `0` | `x > 0` is true. Enter loop. |
| Loop 1 | `1` | `10` | `0` | Extract `0` (`10 % 10`). `rev = 0 * 10 + 0`. Chop `x` to `1`. |
| Loop 2 | `0` | `10` | `1` | Extract `1` (`1 % 10`). `rev = 0 * 10 + 1`. Chop `x` to `1`. |
| End | `0` | `10` | `1` | Loop ends because `x == 0`. `rev == n` (`1 == 10`) is false. Return `false`. |

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — The number of loop iterations equals the number of digits in `x`. Since we divide `x` by 10 in every step, the total steps grow logarithmically base 10 with the size of `x`.
* **Space Complexity:** **O(1)** — We only use a fixed amount of extra space for two `long` variables (`n` and `rev`).

### Optimization Check

Can this code be improved? **Yes, slightly.**

While the time and space complexity are already very good, we can optimize performance in two ways:
1. **Reverse only half the number:** Instead of reversing the whole number, we can stop when `rev` becomes greater than or equal to `x`. If a number is a palindrome, its tail half reversed must match its head half.
2. **Avoid 64-bit integer (`long`):** Reversing only half the number guarantees the reversed value will never overflow a standard 32-bit integer.

Here is how the optimized logic works:
* If a number ends in `0` (and is not `0` itself), it cannot be a palindrome (for example, `10` or `100`). We can filter this out at the start.
* We move digits from `x` to `rev` as long as `x > rev`.
* When the loop stops, if the length of the number was even (like `1221`), `x` will equal `rev` (`12 == 12`). If the length was odd (like `12321`), `x` will equal `rev / 10` (`12 == 123 / 10`), ignoring the middle digit.

```cpp
if (x < 0 || (x % 10 == 0 && x != 0)) return false;

int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

return x == rev || x == rev / 10;
```

* **Improved Time Complexity:** **O(log10(x))** — Runs in half the steps of the original loop.
* **Improved Space Complexity:** **O(1)** — Still uses constant extra space, but avoids using `long`.
* **Theoretical Best:** **O(log10(x)) time and O(1) space.** We must inspect at least half the digits to check for symmetry, so this half-reversing approach reaches the theoretical limit.

## Edge Cases Handled

* **Negative numbers (e.g., `-121`):** Handled immediately by `if (x < 0)`. Always returns `false`.
* **Single-digit numbers (e.g., `7`):** The loop runs once, setting `rev = 7`. Since `7 == 7`, it correctly returns `true`.
* **Zero (`0`):** `x < 0` is false, loop does not run, `rev` stays `0`, `0 == 0` returns `true`.
* **Numbers ending in zero (e.g., `100`):** Reverses to `1`. `1 == 100` is false, correctly returning `false`.
* **Large numbers near maximum limits:** Saved as `long` to prevent numeric overflow during full reversal.
