![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.62%20MB%20(beats%2036.00%25)-orange?style=for-the-badge)

---

## Problem Explained

A **palindrome** is a number that reads the same backward as forward. 

Given an integer `x`, your job is to figure out if it is a palindrome. Return `true` if it reads the same both ways, and `false` if it does not.

*   `x = 121` is a palindrome because reversing `121` gives `121`.
*   `x = -121` is **not** a palindrome because reading it backward gives `121-` (the minus sign moves to the end).
*   `x = 10` is **not** a palindrome because reading it backward gives `01` (which is just `1`).

---

## Intuition

How do you reverse a number mathematically without converting it into text?

1.  **Get the last digit:** Taking a number modulo 10 (`x % 10`) gives you its rightmost digit. For example, `121 % 10` is `1`.
2.  **Shift digits left:** Multiplying a running total by 10 moves all its digits one place to the left, making room for a new last digit.
3.  **Remove the last digit:** Dividing an integer by 10 (`x / 10`) chops off its rightmost digit.

By doing this repeatedly in a loop, you can build the complete reversed number. 

If the original number was negative, it can never be a palindrome because of the minus sign at the front. So, you can instantly reject all negative numbers.

---

## Approach

Here is how the code works step-by-step:

*   `if( x < 0){ return false; }`: Checks if `x` is negative. Since a minus sign only appears at the start, negative numbers can never be palindromes.
*   `long n = x;`: Keeps a copy of the original value of `x` inside `n`. We use a `long` data type so we have extra memory space to prevent potential number overflow.
*   `long rev = 0 ;`: Initializes `rev` to `0`. This variable will hold our reversed number as we build it.
*   `while ( x != 0)`: Starts a loop that keeps running until `x` becomes `0` (meaning all digits have been processed).
*   `rev = rev * 10 + x % 10;`: Takes the last digit of `x` using `x % 10`, shifts the current `rev` number one spot to the left by multiplying by 10, and adds the new digit to `rev`.
*   `x /= 10;`: Removes the last digit from `x` using integer division by 10.
*   `if( rev == n ){ return true; } else { return false; }`: Compares the fully reversed number `rev` with the saved original value `n`. If they are identical, it returns `true`. Otherwise, it returns `false`.

---

## Dry Run

### Case 1: Palindrome number (`x = 121`)

*   Before loop: `x = 121`, `n = 121`, `rev = 0`

| Step | `x != 0` check | `x % 10` (last digit) | `rev` update (`rev * 10 + last digit`) | `x` update (`x / 10`) | Action |
|---|---|---|---|---|---|
| 1 | `121 != 0` (true) | `1` | `0 * 10 + 1 = 1` | `121 / 10 = 12` | Extract `1`, add to `rev` |
| 2 | `12 != 0` (true) | `2` | `1 * 10 + 2 = 12` | `12 / 10 = 1` | Extract `2`, add to `rev` |
| 3 | `1 != 0` (true) | `1` | `12 * 10 + 1 = 121` | `1 / 10 = 0` | Extract `1`, add to `rev` |
| 4 | `0 != 0` (false) | - | `121` | `0` | Loop terminates |

*   Final Check: `rev == n` is `121 == 121` (true). Return `true`.

---

### Case 2: Non-palindrome number ending in zero (`x = 10`)

*   Before loop: `x = 10`, `n = 10`, `rev = 0`

| Step | `x != 0` check | `x % 10` (last digit) | `rev` update (`rev * 10 + last digit`) | `x` update (`x / 10`) | Action |
|---|---|---|---|---|---|
| 1 | `10 != 0` (true) | `0` | `0 * 10 + 0 = 0` | `10 / 10 = 1` | Extract `0`, add to `rev` |
| 2 | `1 != 0` (true) | `1` | `0 * 10 + 1 = 1` | `1 / 10 = 0` | Extract `1`, add to `rev` |
| 3 | `0 != 0` (false) | - | `1` | `0` | Loop terminates |

*   Final Check: `rev == n` is `1 == 10` (false). Return `false`.

---

## Time & Space Complexity

*   **Time Complexity:** **O(log10(x))** — The number of loop iterations depends on the number of digits in `x`. Dividing `x` by 10 each step processes one digit at a time.
*   **Space Complexity:** **O(1)** — Only a few fixed memory variables (`n`, `rev`) are created, regardless of how large `x` is.

### Can this be improved?

Yes! While the overall theoretical time and space complexity remain the same, we can make this code faster and avoid using a 64-bit `long` integer to prevent overflow.

Instead of reversing the **entire** number, we only need to reverse **half** of it.

1.  If the reversed right half equals the remaining left half, the number is a palindrome.
2.  We stop our loop when `x <= rev`. At this point, we have reached or passed the middle digit.
3.  For even digit lengths (like `1221`), `x` becomes `12` and `rev` becomes `12`. They match (`x == rev`).
4.  For odd digit lengths (like `12321`), `x` becomes `12` and `rev` becomes `123`. The middle digit `3` doesn't affect palindrome status, so we discard it by dividing `rev` by 10 (`x == rev / 10`).
5.  Any positive number ending in `0` (except `0` itself) cannot be a palindrome, so we can filter those out immediately.

Here is the key optimization code:

```cpp
if (x < 0 || (x % 10 == 0 && x != 0)) {
    return false;
}

int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

// Even length: x == rev
// Odd length: x == rev / 10 (discards middle digit)
return x == rev || x == rev / 10;
```

*   `x % 10 == 0 && x != 0`: Rejects numbers like `10`, `100`, `1000` right away.
*   `while (x > rev)`: Only loops through half of the digits.
*   `x == rev / 10`: Ignores the middle digit for odd-length numbers.

*   **Improved Time Complexity:** **O(log10(x))** — Half as many loop steps as full reversal.
*   **Improved Space Complexity:** **O(1)** — Uses regular 32-bit `int` instead of 64-bit `long`.
*   **Theoretical Best Complexity:** **O(log10(x))** time and **O(1)** space. The improved half-reversal solution achieves the absolute theoretical limit for performance.

---

## Edge Cases Handled

*   **Negative numbers (e.g., `-121`):** Immediately caught by `x < 0` and returns `false`.
*   **Single-digit numbers (e.g., `7` or `0`):** Handled cleanly. Loop runs once, `rev` becomes `7`, and `rev == n` returns `true`.
*   **Numbers ending in zero (e.g., `10`, `100`):** Loop builds `rev = 1`, which fails `1 == 10` and correctly returns `false`.
*   **Large numbers (near 2^31 - 1 limit):** Reversing a large 32-bit integer fully could cause integer overflow. Using `long` for `rev` in the current code prevents runtime overflow errors.
