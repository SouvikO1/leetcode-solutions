![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-8.6%20MB%20(beats%2054.21%25)-yellow?style=for-the-badge)

---

## Problem Explained

Imagine you have a regular whole number, like 123. The problem asks you to flip its digits backward so it becomes 321. If the number is negative, like -123, the minus sign stays out front and the digits flip to become -321. 

There is a major catch. Computers store standard 32-bit integers within a strict numeric range from -2,147,483,648 up to 2,147,483,647. If flipping the digits makes the number grow too large or too small to fit inside this range, you must return 0 instead. Also, the rules explicitly forbid using 64-bit numbers as a shortcut. You have to solve it using regular math on standard 32-bit values.

## Intuition

The core trick to reversing a number is peeling off the digits one by one from right to left, and building the new reversed number from left to right. 

Think about how you pull the last digit off any number in base-10 math: you use the remainder operator (percent sign). For example, 123 percent 10 gives you 3. Then, to get rid of that 3 so you can look at the next digit, you divide the number by 10. That turns 123 into 12. 

To build your reversed answer, you take whatever you have built so far, multiply it by 10 to shift all its digits one spot to the left, and then add your newly grabbed digit onto the end. 

The "aha" safety moment of this specific solution is checking for overflow *before* you actually do the multiplication and addition. Because you cannot use 64-bit numbers, you cannot wait for the number to overflow and then check if it broke. Instead, you look ahead: if your current built-up number is already larger than the maximum integer divided by 10, multiplying it by 10 on the next step will definitely push it past the limit. Checking this early prevents your program from crashing or wrapping around into garbage numbers.

## Approach

* `int check = 0;`: Initialize a tracking variable named `check` to store our reversed number as we build it, starting at 0.
* `while( x != 0){`: Start a loop that keeps running as long as there are still digits left in `x` to process.
* `if( check > INT_MAX/10 || check < INT_MIN/10){ return 0; }`: Check if multiplying `check` by 10 on the upcoming line would cause it to exceed the maximum allowed 32-bit integer or drop below the minimum allowed 32-bit integer. If it would, safety rules trigger and we immediately return 0.
* `check = check *10 + x % 10 ;`: Shift our existing `check` value one decimal place to the left by multiplying by 10, then add the rightmost digit of `x` (obtained using `x % 10`) onto the end.
* `x /= 10 ;`: Strip away the rightmost digit we just processed from `x` by dividing `x` by 10.
* `return check;`: Once the loop finishes because `x` has been completely stripped down to 0, return the fully constructed reversed number stored in `check`.

## Dry Run

### Case 1: Typical case (Positive number)
Input: `x = 123`

| Step | `x` (before loop/step) | `check` (before update) | `x % 10` | Action | `x` (after update) | `check` (after update) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 123 | 0 | 3 | Multiply check by 10 and add 3, then divide x by 10 | 12 | 3 |
| 2 | 12 | 3 | 2 | Multiply check by 10 and add 2, then divide x by 10 | 1 | 32 |
| 3 | 1 | 32 | 1 | Multiply check by 10 and add 1, then divide x by 10 | 0 | 321 |

Loop terminates because `x` is now 0. Returns `321`.

### Case 2: Negative number with a trailing zero
Input: `x = -120`

| Step | `x` (before loop/step) | `check` (before update) | `x % 10` | Action | `x` (after update) | `check` (after update) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | -120 | 0 | 0 | Multiply check by 10 and add 0, then divide x by 10 | -12 | 0 |
| 2 | -12 | 0 | -2 | Multiply check by 10 and add -2, then divide x by 10 | -1 | -2 |
| 3 | -1 | -2 | -1 | Multiply check by 10 and add -1, then divide x by 10 | 0 | -21 |

Loop terminates because `x` is now 0. Returns `-21`.

## Time & Space Complexity

* **Time:** `O(log(N))` — where `N` is the value of `x`. The number of steps depends directly on how many digits `N` has. Because we divide `x` by 10 in every single loop iteration, the number of steps grows logarithmically based on the size of the number.
* **Space:** `O(1)` — constant space. We only use a couple of primitive integer variables (`check`), meaning our memory usage never grows no matter how large the input number gets.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this solution is already completely optimal. 
* **Time complexity:** You cannot do it faster than checking each digit once, so `O(log(N))` is the absolute theoretical limit for time.
* **Space complexity:** Using `O(1)` memory means we use a fixed amount of extra space regardless of input size, which cannot be beaten. 

No further improvements are possible.

## Edge Cases Handled

* **Negative numbers:** C++ handles negative number remainders naturally (e.g., `-123 % 10` is `-3`), and the loop logic correctly preserves negative signs all the way through to the final output.
* **Trailing zeros:** Numbers like `120` turn into `21` because the leading zero becomes a useless leading zero when reversed, which math-wise drops off automatically.
* **Overflow protection:** Any input that would overflow the 32-bit boundary when reversed is safely caught *before* it breaks the program, returning 0 as required.
