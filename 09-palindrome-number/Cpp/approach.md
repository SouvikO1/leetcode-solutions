![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.4%20MB%20(beats%2092.01%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The problem asks us to determine if a given integer reads the exact same forwards and backwards. A number with this property is called a **palindrome**. 

If the number is a palindrome, we return `true`. Otherwise, we return `false`.

- **Example 1:** `121` reads as `121` from left to right and from right to left. Result: `true`.
- **Example 2:** `-121` reads as `-121` from left to right, but `121-` from right to left. Result: `false`.
- **Example 3:** `10` reads as `01` when reversed (which is just `1`). Result: `false`.

## Intuition

To check if a number reads the same backwards, we can reverse the digits of the number using basic arithmetic and compare the reversed result to the original input.

We extract digits from the end of the number one by one using the modulo operator (`% 10`), append each digit to our reversed number, and drop the last digit from the original number using integer division (`/ 10`).

Negative numbers can never be palindromes because the negative sign always sits at the front, but reversing it puts the sign at the end.

## Approach

Here is how the code solves the problem step-by-step:

- `if( x < 0)`: Checks if `x` is negative. If it is negative, it cannot be a palindrome, so we immediately return `false`.
- `long n = x;`: Stores a backup copy of the original input `x` in a 64-bit integer variable `n`. We need this because `x` will be modified down to `0` inside our loop, and we need the original value for comparison at the end.
- `long rev = 0 ;`: Creates a 64-bit integer variable `rev` set to `0`. It will hold the reversed number as we build it. We use a 64-bit integer (`long`) to prevent arithmetic overflow if the reversed number exceeds standard 32-bit integer limits.
- `while ( x != 0)`: Loops until `x` has no digits left.
- `rev = rev * 10 + x % 10;`: Takes the last digit of `x` using `x % 10`, shifts the current reversed value `rev` one place to the left by multiplying it by `10`, and adds the extracted digit.
- `x /= 10;`: Removes the last digit from `x` by dividing it by `10`.
- `if( rev == n )`: Compares the fully built reversed number `rev` to the saved original number `n`.
- `return true;` / `else { return false; }`: Returns `true` if `rev` matches `n`, otherwise returns `false`.

## Dry Run

### Case 1: Typical positive palindrome (`x = 121`)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | `121` | `121` | `0` | Check `x < 0` (false). Save `n = 121`. |
| Loop 1 | `12` | `121` | `1` | Extract `1` (`121 % 10`). `rev = 0 * 10 + 1`. Drop digit (`121 / 10`). |
| Loop 2 | `1` | `121` | `12` | Extract `2` (`12 % 10`). `rev = 1 * 10 + 2`. Drop digit (`12 / 10`). |
| Loop 3 | `0` | `121` | `121` | Extract `1` (`1 % 10`). `rev = 12 * 10 + 1`. Drop digit (`1 / 10`). |
| End | `0` | `121` | `121` | Loop terminates (`x == 0`). `rev == n` (121 == 121) is true. |

### Case 2: Number ending in zero (`x = 10`)

| Step | `x` | `n` | `rev` | Action |
| :--- | :--- | :--- | :--- | :--- |
| Start | `10` | `10` | `0` | Check `x < 0` (false). Save `n = 10`. |
| Loop 1 | `1` | `10` | `0` | Extract `0` (`10 % 10`). `rev = 0 * 10 + 0`. Drop digit (`10 / 10`). |
| Loop 2 | `0` | `10` | `1` | Extract `1` (`1 % 10`). `rev = 0 * 10 + 1`. Drop digit (`1 / 10`). |
| End | `0` | `10` | `1` | Loop terminates (`x == 0`). `rev == n` (1 == 10) is false. |

## Time & Space Complexity

- **Time Complexity:** **O(log10(x))** — The number of loop iterations depends on the number of decimal digits in `x`. Dividing a number by 10 in each step means the loop runs log base 10 of `x` times.
- **Space Complexity:** **O(1)** — Memory usage is constant because we only store a few scalar variables (`n` and `rev`).

### Can this be improved?

Yes! We can optimize the logic by **reversing only half of the number**. 

Reversing the entire number requires using a 64-bit integer (`long`) to avoid integer overflow issues when reversing large numbers. If we only reverse the back half of the number until it reaches or exceeds the front half, we completely avoid integer overflow and cut the loop steps in half.

Here is the key logic:
1. Any number ending in `0` (except `0` itself) cannot be a palindrome, so we fail it early.
2. We stop our loop as soon as `x <= rev`. At this point, we have processed half the digits.
3. For numbers with an even count of digits (like `1221`), `x` will equal `rev` (`12 == 12`).
4. For numbers with an odd count of digits (like `12321`), `rev` will end up as `123` and `x` as `12`. We can discard the middle digit by checking `x == rev / 10`.

```cpp
// Optimized snippet:
if (x < 0 || (x % 10 == 0 && x != 0)) return false;

int rev = 0;
while (x > rev) {
    rev = rev * 10 + x % 10;
    x /= 10;
}

return x == rev || x == rev / 10;
```

- **`x > rev`**: Keeps looping only until `rev` has as many (or more) digits as `x`.
- **`x == rev / 10`**: Ignores the middle digit for odd-length numbers.

- **Improved Time Complexity:** **O(log10(x))** — Still logarithmic, but processes only half the digits (half the iterations).
- **Improved Space Complexity:** **O(1)** — Pure 32-bit integer operations without using `long`.
- **Is it optimal?** Yes. You must inspect at least half the digits to verify a palindrome, making this the theoretical best approach.

## Edge Cases Handled

- **Negative Numbers (e.g., `-121`):** Handled by `if (x < 0)`, immediately returning `false`.
- **Single-Digit Numbers (e.g., `7`):** Runs through the loop once, `rev` becomes `7`, matching `n`, returning `true`.
- **Numbers ending in zero (e.g., `10`, `100`):** Correctly reverses to `1` (stripping leading zeros in math), failing the `rev == n` check and returning `false`.
- **32-Bit Integer Overflow:** Handled safely by defining `rev` and `n` as 64-bit integers (`long`), preventing crashes or garbage comparisons when reversing numbers near `2^31 - 1`.
