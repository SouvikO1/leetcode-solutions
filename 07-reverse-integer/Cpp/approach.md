![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.21%25)-yellow?style=for-the-badge)

---

## Problem Explained

Imagine you are holding a standard 32-bit signed integer. This is a whole number that can be positive or negative, living anywhere between roughly negative 2.1 billion and positive 2.1 billion (specifically from -2147483648 to 2147483647). 

The problem asks you to take this number and **reverse its digits**. For example, if you are given 123, you need to return 321. If you are given -123, you need to return -321. 

There is one important catch: **overflow**. If reversing the digits makes the number grow too large to fit inside that standard 32-bit integer range, the number will "break" or wrap around in computer memory. The problem says that if this happens, you must not let it break. Instead, you must safely catch it and **return 0**. 

Also, a crucial rule: the environment does not let you use 64-bit integers. Usually, programmers might store a reversed number in a larger 64-bit box to check for overflow safely. Here, you are forced to do all your checks using only standard 32-bit limits.

## Intuition

The "aha" moment for this problem relies on a classic math trick: **peeling off digits from the back of a number one by one using modulo and division**, and building a new number from scratch. 

Think about how you would reverse the number 123 in your head:
1. Grab the last digit (3) by looking at the remainder when dividing by 10 (123 % 10 = 3).
2. Move what is left of the original number down by dividing by 10 (123 / 10 = 12).
3. Start building your reversed answer. Multiply your current answer by 10 to make room, then tack on that new digit. (0 * 10 + 3 = 3).
4. Repeat this for the next digit (2), so your answer becomes 3 * 10 + 2 = 32.
5. Repeat for the last digit (1), so your answer becomes 32 * 10 + 1 = 321.

Because we cannot use 64-bit integers to safely hold an oversized number before checking if it exceeds the limit, **we must check for overflow *before* we actually perform the multiplication.** 

If our running total (`check`) is already greater than 214748364 (which is INT_MAX / 10), multiplying it by 10 on the next step will guaranteed push it past the maximum 32-bit limit of 2147483647. The same logic applies on the negative side for INT_MIN / 10. By checking this ahead of time, we catch potential overflows safely.

## Approach

* `int check = 0;` : Create a variable named `check` initialized to 0. This will store our reversed number as we build it digit by digit.
* `while( x != 0){` : Start a loop that keeps running as long as the input number `x` still has digits left to process (meaning `x` is not yet zero).
* `if( check > INT_MAX/10 || check < INT_MIN/10){ return 0; }` : Before multiplying `check` by 10, check if it is already too large or too small to safely do so. If `check` exceeds the safe threshold, the next multiplication would overflow, so immediately return 0.
* `check = check *10 + x % 10 ;` : Multiply our current `check` by 10 to shift all existing digits one place to the left, and add the last digit of `x` (obtained using `x % 10`) onto the end.
* `x /= 10 ;` : Divide `x` by 10 to strip away the last digit we just processed, shrinking `x` for the next loop iteration.
* `return check;` : Once the loop finishes because `x` has been reduced to 0, return the fully reversed number stored in `check`.

## Dry Run

### Case 1: Typical case (Positive number)
Input: `x = 123`

| Loop Iteration | `x` (before step) | `check` (before step) | Action | `x` (after step) | `check` (after step) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 123 | 0 | Safe check passes. `check` becomes `0 * 10 + 3 = 3`. `x` becomes `123 / 10 = 12`. | 12 | 3 |
| 2 | 12 | 3 | Safe check passes. `check` becomes `3 * 10 + 2 = 32`. `x` becomes `12 / 10 = 1`. | 1 | 32 |
| 3 | 1 | 32 | Safe check passes. `check` becomes `32 * 10 + 1 = 321`. `x` becomes `1 / 10 = 0`. | 0 | 321 |

*Loop ends because `x` is 0. Returns `321`.*

---

### Case 2: Number ending in zero
Input: `x = 120`

| Loop Iteration | `x` (before step) | `check` (before step) | Action | `x` (after step) | `check` (after step) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 120 | 0 | Safe check passes. `check` becomes `0 * 10 + 0 = 0`. `x` becomes `120 / 10 = 12`. | 12 | 0 |
| 2 | 12 | 0 | Safe check passes. `check` becomes `0 * 10 + 2 = 2`. `x` becomes `12 / 10 = 1`. | 1 | 2 |
| 3 | 1 | 2 | Safe check passes. `check` becomes `2 * 10 + 1 = 21`. `x` becomes `1 / 10 = 0`. | 0 | 21 |

*Loop ends because `x` is 0. Returns `21`.*

## Time & Space Complexity

- **Time:** O(log10(n)) — The number of times the while loop runs is equal to the number of digits in the integer `x`. Since a 32-bit integer has a maximum of 10 digits, the loop runs at most 10 times. In big-O terms, this is constant time, or O(1), because the maximum number of operations never scales past 10 regardless of how large the input value is.
- **Space:** O(1) — We only use a couple of primitive integer variables (`check`), meaning the amount of extra memory used is constant and does not grow with the input size.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this code is **already optimal**. 
- The time complexity is O(1) (bounded by the fixed maximum of 10 digits in a 32-bit integer). 
- The space complexity is O(1). 

No further improvements can be made because you fundamentally must inspect every digit of the number at least once to reverse it, and checking for overflow on the fly prevents you from needing any extra memory or data structures.

## Edge Cases Handled

- **Negative Numbers:** The modulo operator in C++ retains the negative sign (e.g., `-123 % 10` is `-3`), and division pushes it toward zero. The code naturally handles negative numbers like `-123` and turns them into `-321`.
- **Numbers Ending in Zero:** Inputs like `120` correctly have their trailing zero shifted to the front during math operations, turning into `21` rather than `021`.
- **Overflow and Underflow:** If a number is close to the 32-bit boundary (like `1534236469`) and reversing it would push it past `INT_MAX`, the `INT_MAX/10` boundary check catches it on the final digit and safely returns `0`.
- **Single-Digit Numbers:** Numbers between -9 and 9 enter the loop once, extract their single digit, and immediately exit the loop to return themselves unchanged.
- **Zero Input:** If `x = 0`, the while loop is skipped entirely, and the initial `check` value of `0` is returned correctly.
