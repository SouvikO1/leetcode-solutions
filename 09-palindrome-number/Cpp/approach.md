![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.06%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

A palindrome is a word or number that reads the exact same backward as it does forward. 

This problem asks us to take an integer `x` and decide if it is a palindrome. If it is, we return `true`. If it is not, we return `false`.

For example:
* `121` is a palindrome because reversing it still gives `121`.
* `-121` is **not** a palindrome because reading it backward gives `121-` (the minus sign moves to the end).
* `10` is **not** a palindrome because reading it backward gives `01`.

## Intuition

The simplest way to check if a number is a palindrome is to build its reverse and compare it to the original number.

To reverse a number mathematically without converting it to a string:
1. Grab the last digit using modulo (`x % 10`).
2. Append that digit to our growing reversed number.
3. Remove the last digit from the original number using integer division (`x / 10`).
4. Repeat this until the original number becomes zero.

If the fully reversed number equals the original starting number, it is a palindrome.

## Approach

Here is how the code works, step-by-step:

* `if( x < 0)`: Checks if the number is negative. If it is, the code immediately returns `false` because a negative sign at the front cannot exist at the back.
* `long n = x;`: Saves a backup copy of `x` into `n`. We need this because our loop will modify and shrink `x` down to `0`. We use `long` to avoid potential overflow issues.
* `long rev = 0;`: Creates a variable named `rev` to hold our reversed number, starting at `0`.
* `while ( x != 0)`: Starts a loop that runs as long as `x` still has digits left to process.
* `rev = rev * 10 + x % 10;`: Takes the last digit of `x` using `x % 10`. Shifts all existing digits in `rev` one place to the left by multiplying by `10`, then adds the new digit.
* `x /= 10;`: Removes the last digit from `x` by dividing it by `10`.
* `if( rev == n )`: Compares our reconstructed reversed number (`rev`) with the saved original number (`n`).
* `return true;` / `return false;`: Returns `true` if they are identical, otherwise returns `false`.

## Dry Run

### Case 1: `x = 121` (Typical palindrome)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | `121` | `121` | `0` | `x > 0`, enter loop |
| Loop 1 | `12` | `121` | `1` | Extract `1`, add to `rev`, drop `1` from `x` |
| Loop 2 | `1` | `121` | `12` | Extract `2`, add to `rev`, drop `2` from `x` |
| Loop 3 | `0` | `121` | `121` | Extract `1`, add to `rev`, drop `1` from `x` |
| End | `0` | `121` | `121` | Loop ends (`x == 0`). `rev == n` is `true`. Return `true`. |

### Case 2: `x = 10` (Non-palindrome ending in zero)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | `10` | `10` | `0` | `x > 0`, enter loop |
| Loop 1 | `1` | `10` | `0` | Extract `0`, `rev` stays `0`, drop `0` from `x` |
| Loop 2 | `0` | `10` | `1` | Extract `1`, `rev` becomes `1`, drop `1` from `x` |
| End | `0` | `10` | `1` | Loop ends (`x == 0`). `rev (1) == n (10)` is `false`. Return `false`. |

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — The number of iterations depends on the number of digits in `x`. Dividing a number by 10 at each step takes logarithmic time relative to its value.
* **Space Complexity:** **O(1)** — We only use a few basic variables (`n`, `rev`), taking constant memory.

### Optimization Potential

Can we make this even better?

Yes! The current code reverses the entire number and relies on a 64-bit `long` to prevent integer overflow. We can optimize this by **reversing only half of the number**.

#### How Half-Reversal Works:
1. If a number ends in `0` (like `10` or `120`), it cannot be a palindrome unless the number itself is `0`. We can reject these immediately.
2. We move digits from `x` into `rev` one by one.
3. When `x <= rev`, we have reached or passed the middle of the number!
4. For even-length numbers (like `1221`), `x` will equal `rev` (`12 == 12`).
5. For odd-length numbers (like `12321`), `x` will equal `rev / 10` (`12 == 123 / 10` which is `12`), because the middle digit does not matter.

This eliminates the need for `long` entirely and runs in half the iterations.

```cpp
// Optimized snippet
if (x < 0 || (x % 10 == 0 && x != 0)) return false;

int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

return x == rev || x == rev / 10;
```

* Line 2 checks for negative numbers and non-zero numbers ending in zero.
* The loop stops right at the middle point when `x` is no longer greater than `rev`.
* Line 9 checks both even-length (`x == rev`) and odd-length (`x == rev / 10`) matches.

* **Improved Time Complexity:** **O(log10(x))** — Still logarithmic, but processes half as many digits.
* **Improved Space Complexity:** **O(1)** — Still constant space, but uses standard 32-bit `int` instead of `long`.
* **Theoretical Best:** **O(log10(x))** time and **O(1)** space is the theoretical optimal limit, because you must check at least half the digits to verify a palindrome. The half-reversal approach reaches this theoretical limit.

## Edge Cases Handled

* **Negative numbers (e.g., `-121`):** Handled by `if (x < 0)` at the top, immediately returning `false`.
* **Single-digit numbers (e.g., `7`):** Handled naturally. The loop runs once, `rev` becomes `7`, which equals `n` (`7`).
* **Zero (`0`):** Handled correctly. The loop does not run, leaving `rev` as `0`, which equals `n` (`0`). Returns `true`.
* **Numbers ending in zero (e.g., `10`):** Handled correctly. Reverses to `1`, which does not match `10`. Returns `false`.
* **Large numbers near integer limits (up to `2^31 - 1`):** Using `long` for `n` and `rev` prevents buffer overflow during reversal.
