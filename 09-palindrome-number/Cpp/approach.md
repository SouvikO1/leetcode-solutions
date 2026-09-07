![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.03%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

A palindrome is a value that reads the same backward as forward. For example, the number `121` is a palindrome because reversing its digits still gives `121`. 

The problem asks us to take an integer `x` and decide if it is a palindrome. If it reads the same both ways, return `true`. Otherwise, return `false`.

Negative numbers like `-121` are not palindromes because the minus sign stays at the front. Reversing `-121` gives `121-`, which is not the same number. Numbers ending in zero (like `10`) are also not palindromes because reversing them gives `01` (which is `1`).

---

## Intuition

The simplest idea is to reverse the number mathematically and check if the reversed version matches the original version.

To reverse an integer without converting it to text:
1. Extract the last digit using the remainder operation (`x % 10`).
2. Add that digit to our growing reversed number.
3. Remove the last digit from `x` by dividing by 10 (`x / 10`).
4. Repeat until all digits are processed.

Negative numbers can never be palindromes, so we can filter them out immediately.

---

## Approach

Here is how the code works step-by-step:

* `if( x < 0){ return false; }`: Checks if `x` is negative. If it is negative, it cannot be a palindrome, so return `false` right away.
* `long n = x;`: Stores a copy of the original number `x` inside variable `n`. We need this copy because our loop will strip digits from `x` until `x` becomes `0`. We use `long` to safely hold large numbers.
* `long rev = 0 ;`: Creates a variable `rev` set to `0`. This will hold our reversed number as we build it digit by digit.
* `while ( x != 0){`: Starts a loop that keeps running until `x` has no digits left.
* `rev = rev * 10 + x % 10;`: Takes the last digit of `x` using `x % 10`. It then shifts the existing digits in `rev` one position to the left (by multiplying `rev` by 10) and appends the new digit.
* `x /= 10;`: Removes the last digit from `x` using integer division by 10.
* `if( rev == n ){ return true; } else{ return false; }`: Compares the fully reversed number `rev` against the original saved number `n`. If they are equal, returns `true`; otherwise, returns `false`.

---

## Dry Run

### Case 1: Standard Palindrome (`x = 121`)

| Step | Action | `x` | `n` | `rev` |
| :--- | :--- | :--- | :--- | :--- |
| Initial | Check `x < 0` (false). Save copy to `n`. Set `rev = 0`. | 121 | 121 | 0 |
| Loop 1 | Peel `1` from `x`. Add to `rev`. Shrink `x`. | 12 | 121 | 1 |
| Loop 2 | Peel `2` from `x`. Add to `rev`. Shrink `x`. | 1 | 121 | 12 |
| Loop 3 | Peel `1` from `x`. Add to `rev`. Shrink `x`. | 0 | 121 | 121 |
| End | Loop ends (`x == 0`). Compare `rev == n` (`121 == 121`). Return `true`. | 0 | 121 | 121 |

### Case 2: Non-Palindrome (`x = 10`)

| Step | Action | `x` | `n` | `rev` |
| :--- | :--- | :--- | :--- | :--- |
| Initial | Check `x < 0` (false). Save copy to `n`. Set `rev = 0`. | 10 | 10 | 0 |
| Loop 1 | Peel `0` from `x`. Add to `rev`. Shrink `x`. | 1 | 10 | 0 |
| Loop 2 | Peel `1` from `x`. Add to `rev`. Shrink `x`. | 0 | 10 | 1 |
| End | Loop ends (`x == 0`). Compare `rev == n` (`1 == 10`). Return `false`. | 0 | 10 | 1 |

---

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — In each iteration of the loop, we divide `x` by 10. The number of steps equals the number of digits in `x`.
* **Space Complexity:** **O(1)** — We only store a few single variables (`n`, `rev`), so memory usage remains constant regardless of the input size.

### Can this be improved?

Yes, slightly in performance and memory usage!

Currently, the code reverses the **entire** number and relies on a 64-bit integer (`long`) to prevent arithmetic overflow if the reversed number exceeds standard integer limits.

We can improve this by reversing **only half** of the number:
1. If we reverse only half the digits, the reversed value will never exceed the size of a standard integer, so we do not need `long`.
2. Reversing stops as soon as the reversed number `rev` becomes greater than or equal to the remaining half of `x`.
3. If the length of the number is even, `x` and `rev` will be equal for a palindrome (e.g., `1221` becomes `x = 12` and `rev = 12`).
4. If the length is odd, `x` will equal `rev / 10` because the middle digit does not matter (e.g., `12321` becomes `x = 12` and `rev = 123`).

Here is the key optimized change:

```cpp
bool isPalindrome(int x) {
    // Negative numbers or numbers ending in 0 (except 0 itself) are not palindromes
    if (x < 0 || (x % 10 == 0 && x != 0)) {
        return false;
    }

    int rev = 0;
    while (x > rev) {
        rev = rev * 10 + x % 10;
        x /= 10;
    }

    // Even length: x == rev
    // Odd length: x == rev / 10 (discard middle digit)
    return x == rev || x == rev / 10;
}
```

* **Improved Time Complexity:** **O(log10(x))** — Still logarithmic, but executes half as many loop iterations.
* **Improved Space Complexity:** **O(1)** — Still constant, but uses 32-bit `int` instead of 64-bit `long`.
* **Theoretical Best:** **O(log10(x))** time and **O(1)** space. The half-reversal method reaches the theoretical optimal limit.

---

## Edge Cases Handled

* **Negative Numbers (e.g., `-121`):** Caught instantly by `if (x < 0)` and returns `false`.
* **Single Digit Numbers (e.g., `7`):** The loop runs once, setting `rev` to `7`, which equals `n`, returning `true`.
* **Zero (`0`):** Handled correctly as a single digit, returning `true`.
* **Numbers ending in Zero (e.g., `10`):** The code computes `rev = 1`, compares `1 == 10`, and returns `false`.
* **Large Numbers:** Handled without integer overflow because `rev` and `n` use the 64-bit `long` type in the original code.
