![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.04%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The problem asks us to determine if a given integer reads the same backward as forward. If it does, we return `true`. If it does not, we return `false`.

For example:
* `121` read backward is `121`. This matches, so the answer is `true`.
* `10` read backward is `01` (or `1`). This does not match `10`, so the answer is `false`.
* `-121` read backward is `121-` because of the negative sign. This does not match, so the answer is `false`.

## Intuition

To check if a number is a palindrome without turning it into a string, we can build its reverse mathematically. 

We can extract the last digit of a number by taking its remainder when divided by 10 (`x % 10`). We can then add this digit to a running total for the reversed number. To make room for the next digit, we multiply our running total by 10. Finally, we chop off the last digit of our original number by dividing it by 10 (`x /= 10`).

Once we reverse the entire number, we compare it to our original number. If they are identical, the original number was a palindrome.

## Approach

Here is the step-by-step breakdown of how the code works:

* `if( x < 0){ return false; }`: Checks if the number is negative. Negative numbers always have a minus sign at the front, which ends up at the back when reversed. Therefore, negative numbers can never be palindromes.
* `long n = x;`: Creates a backup copy of `x` stored in `n`. We use a 64-bit integer type (`long`) to avoid memory overflow issues later, because reversing a large 32-bit integer might create a number too big for a normal `int`.
* `long rev = 0 ;`: Sets up a variable `rev` (also as a 64-bit integer) initialized to `0`. This will store the reversed version of the number as we build it.
* `while ( x != 0){`: Starts a loop that runs until every digit has been stripped from `x`.
* `rev = rev * 10 + x % 10;`: Gets the last digit of `x` using `x % 10`, shifts the current digits in `rev` one position to the left by multiplying by `10`, and appends the extracted digit.
* `x /= 10;`: Removes the last digit from `x` using integer division by 10.
* `if( rev == n ){ return true; } else{ return false; }`: Compares the fully reversed number `rev` with the saved original number `n`. If they are equal, it returns `true`; otherwise, it returns `false`.

## Dry Run

### Case 1: Typical palindrome (`x = 121`)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | `121` | `121` | `0` | Check `x < 0` (false). Copy `x` to `n`. Initialize `rev = 0`. |
| Loop 1 | `12` | `121` | `1` | `rev = 0 * 10 + 121 % 10 = 1`. Drop last digit: `x = 121 / 10 = 12`. |
| Loop 2 | `1` | `121` | `12` | `rev = 1 * 10 + 12 % 10 = 12`. Drop last digit: `x = 12 / 10 = 1`. |
| Loop 3 | `0` | `121` | `121` | `rev = 12 * 10 + 1 % 10 = 121`. Drop last digit: `x = 1 / 10 = 0`. |
| End | `0` | `121` | `121` | Loop ends because `x == 0`. Compare `rev == n` (`121 == 121`), which is true. Return `true`. |

### Case 2: Number ending in zero (`x = 10`)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | `10` | `10` | `0` | Check `x < 0` (false). Copy `x` to `n`. Initialize `rev = 0`. |
| Loop 1 | `1` | `10` | `0` | `rev = 0 * 10 + 10 % 10 = 0`. Drop last digit: `x = 10 / 10 = 1`. |
| Loop 2 | `0` | `10` | `1` | `rev = 0 * 10 + 1 % 10 = 1`. Drop last digit: `x = 1 / 10 = 0`. |
| End | `0` | `10` | `1` | Loop ends because `x == 0`. Compare `rev == n` (`1 == 10`), which is false. Return `false`. |

## Time & Space Complexity

* **Time Complexity:** **O(log10(x))** — In each iteration of the loop, we divide `x` by 10. The number of steps is equal to the total number of digits in `x`, which is roughly log base 10 of `x`.
* **Space Complexity:** **O(1)** — We only use two extra 64-bit variables (`n` and `rev`). The memory used does not grow with the input size.

### Can this be improved?

The overall time and space complexity are already optimal, but the implementation itself can be improved. 

Right now, the code reverses the **entire** number, which requires using a 64-bit integer (`long`) to prevent memory overflow. We can optimize this by reversing **only half** of the number. 

If we reverse digits until the reversed number becomes greater than or equal to the remaining original number, we know we have reached the midpoint. This avoids overflow completely, removes the need for `long`, cuts the number of loop steps in half, and allows us to rule out numbers ending in 0 immediately (except 0 itself).

Here is the key optimization:

```cpp
// Negative numbers, or non-zero numbers ending in 0, cannot be palindromes
if (x < 0 || (x % 10 == 0 && x != 0)) {
    return false;
}

int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

// For even length numbers: x == rev (e.g. 1221 becomes x = 12, rev = 12)
// For odd length numbers: x == rev / 10 (e.g. 12321 becomes x = 12, rev = 123, discard middle digit)
return x == rev || x == rev / 10;
```

* **Improved Complexity:** Still **O(log10(x))** time and **O(1)** space, but with half the loop iterations and less memory usage per variable.
* **Theoretical Limit:** **O(log10(x))** time is the absolute theoretical best because we must look at every digit at least once to confirm if it is a palindrome. The improved half-reversal version reaches this best limit.

## Edge Cases Handled

* **Negative numbers (e.g., `-121`):** Handled immediately at the start with `if (x < 0) return false;`.
* **Single-digit numbers (e.g., `7`):** The loop runs once, `rev` becomes `7`, which equals `n = 7`, correctly returning `true`.
* **Numbers ending in zero (e.g., `10`):** Handled correctly as `rev` becomes `1`, which does not match `10`, returning `false`.
* **Large numbers near integer limits (e.g., `2147483647`):** Using `long` for `n` and `rev` prevents overflow errors during multiplication when reversing large 32-bit integers.
