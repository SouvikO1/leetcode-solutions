![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.04%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

A palindrome is a word, phrase, or number that reads the same backward as forward. 

This problem asks us to check if a given integer `x` is a palindrome. If it reads the same from left to right as it does from right to left, we return `true`. Otherwise, we return `false`.

**Examples:**
* `x = 121`: Reads as `121` forward and `121` backward. This is a palindrome (`true`).
* `x = -121`: Reads as `-121` forward, but backward it becomes `121-`. The minus sign ends up at the end, so it is not a palindrome (`false`).
* `x = 10`: Reads as `10` forward, but backward it becomes `01` (which is `1`). It is not a palindrome (`false`).

---

## Intuition

The key idea is to build the reversed version of the number digit by digit using basic math, then compare it to the original number.

1. **Negative numbers:** Any negative number starts with a minus sign (`-`). When reversed, that sign moves to the end, which is not valid. So negative numbers can never be palindromes.
2. **Reversing the digits:** We can extract the last digit of a number using the remainder operator (`x % 10`). We can remove that last digit from `x` by dividing `x` by 10 (`x /= 10`). 
3. **Building the reversed number:** We start a variable `rev` at `0`. In each step, we shift `rev` one place to the left by multiplying it by `10`, then add the extracted digit.
4. **Comparison:** Because modifying `x` reduces it down to `0`, we save a copy of `x` at the start. Once the loop finishes, we check if our reversed number matches that original copy.

---

## Approach

* `if( x < 0)`: Checks if `x` is negative.
* `return false;`: Immediately returns `false` if `x` is negative, because the minus sign prevents it from being a palindrome.
* `long n = x;`: Creates a copy of `x` named `n`. We use `long` (a larger integer type) to store it safely without overflowing memory, because `x` will be changed during the reversing process.
* `long rev = 0 ;`: Creates a variable `rev` set to `0`. This will store our reversed number as we build it.
* `while ( x != 0)`: Loops continuously until all digits of `x` have been processed and `x` becomes `0`.
* `rev = rev * 10 + x % 10;`: Gets the last digit of `x` using `x % 10`, shifts the current digits in `rev` left by multiplying by 10, and adds the new digit to `rev`.
* `x /= 10;`: Drops the last digit from `x` using integer division by 10.
* `if( rev == n )`: Compares the fully built reversed number `rev` to our saved original copy `n`.
* `return true;`: Returns `true` if `rev` and `n` are identical.
* `else{ return false; }`: Returns `false` if `rev` and `n` do not match.

---

## Dry Run

### Case 1: `x = 121` (Typical positive palindrome)

| Step | `x` | `n` | `rev` | Action |
| --- | --- | --- | --- | --- |
| Init | 121 | 121 | 0 | `x >= 0` check passes. Copy `n = 121`, initialize `rev = 0`. |
| Loop 1 | 12 | 121 | 1 | Extract last digit `1` (`121 % 10`). Update `rev` (`0 * 10 + 1 = 1`). Reduce `x` (`121 / 10 = 12`). |
| Loop 2 | 1 | 121 | 12 | Extract last digit `2` (`12 % 10`). Update `rev` (`1 * 10 + 2 = 12`). Reduce `x` (`12 / 10 = 1`). |
| Loop 3 | 0 | 121 | 121 | Extract last digit `1` (`1 % 10`). Update `rev` (`12 * 10 + 1 = 121`). Reduce `x` (`1 / 10 = 0`). |
| End | 0 | 121 | 121 | `x == 0` ends the loop. Compare `rev` (121) with `n` (121). They match, so return `true`. |

### Case 2: `x = -121` (Negative edge case)

| Step | `x` | `n` | `rev` | Action |
| --- | --- | --- | --- | --- |
| Init | -121 | - | - | Check `if (x < 0)`. Since `-121 < 0` is true, immediately return `false`. |

---

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — The number of loop iterations depends on the number of digits in `x`. Dividing an integer by 10 at each step takes log base 10 of `x` operations.
* **Space Complexity:** **O(1)** — We only store a few primitive variables (`n` and `rev`), which consume a small, constant amount of extra memory regardless of the input size.

### Optimization

While the current code beats 100% on runtime, we can improve the memory efficiency and logic by **reversing only half of the number**. 

**Why this works:** 
1. If a number is a palindrome, the second half of the digits reversed will equal the first half (for example, in `1221`, the back half `21` reversed is `12`, matching the front half `12`).
2. Stopping halfway avoids the need for a `long` variable, as half a 32-bit integer will never overflow standard integer limits.
3. Any non-zero number ending in `0` (like `10` or `100`) cannot be a palindrome, because no positive integer starts with a `0`.

```cpp
if (x < 0 || (x % 10 == 0 && x != 0)) return false;

int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

return x == rev || x == rev / 10;
```

* `if (x < 0 || (x % 10 == 0 && x != 0)) return false;`: Early exit for negative numbers and numbers ending in 0.
* `while (x > rev)`: Loops until `rev` has at least as many digits as `x` left over. This automatically stops at the middle point.
* `return x == rev || x == rev / 10;`: For even-length numbers (like `1221`), `x` equals `rev` (`12 == 12`). For odd-length numbers (like `12321`), `x` equals `rev / 10` (`12 == 123 / 10`), ignoring the middle digit.

* **Improved Complexity:** **Time:** O(log10(x)) (runs in half the iterations), **Space:** O(1) (uses only standard `int` variables).
* **Theoretical Best:** O(log10(x)) time and O(1) space. Both solutions reach this optimal Big-O bound, but reversing half the digits uses fewer total hardware operations.

---

## Edge Cases Handled

* **Negative numbers (`x < 0`):** Handled instantly by the initial check, returning `false` before doing any math.
* **Single-digit numbers (`x = 0` to `9`):** Single digits are always palindromes. The loop runs once, sets `rev` equal to `x`, and correctly returns `true`.
* **Numbers ending in zero (`x = 10`, `100`, etc.):** The original code correctly reverses `10` into `1`, compares `1 == 10`, and returns `false`.
* **Integer overflow:** The code uses `long` for variables `n` and `rev`. Reversing large 32-bit integers near 2,147,483,647 could otherwise exceed the maximum range of a standard `int` and cause an overflow error.
