![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.05%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.48%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

This problem asks us to take a normal integer (like 3749) and translate it into a **Roman numeral string** (like "MMMDCCXLIX"). 

Roman numerals use letters to represent values:
- I = 1
- V = 5
- X = 10
- L = 50
- C = 100
- D = 500
- M = 1000

Instead of just adding symbols together endlessly, Roman numerals have specific rules. For example, you cannot write four I symbols in a row to mean 4. Instead, you write IV, which means 5 minus 1. This is called a **subtractive form**. The only subtractive pairs allowed are:
- 4 = IV (5 - 1)
- 9 = IX (10 - 1)
- 40 = XL (50 - 10)
- 90 = XC (100 - 10)
- 450? No: 400 = CD (500 - 100)
- 900 = CM (1000 - 100)

The input numbers will always be between 1 and 3999 inclusive. We need to output the exact correct Roman numeral combination.

## Intuition

The "aha" moment for this solution comes from realizing that **Roman numerals are grouped strictly by decimal place values**. 

Think about any number, like 3749. It breaks down into:
- Thousands: 3000 (MMM)
- Hundreds: 700 (DCC)
- Tens: 40 (XL)
- Ones: 9 (IX)

Because the input is capped at 3999, the thousands place can only ever go from 0 to 3, the hundreds from 0 to 9, the tens from 0 to 9, and the ones from 0 to 9. 

Instead of writing a complicated loop that checks which subtraction is best at every single step, we can just **pre-write every possible translation for each place value** in small look-up lists (arrays). 
- Ones digits always map to the exact same 10 words ("I", "II", "III", "IV", etc.).
- Tens digits always map to the exact same 10 words ("X", "XX", "XXX", "XL", etc.).
- Hundreds digits map to their own 10 words ("C", "CC", "CCC", "CD", etc.).
- Thousands digits map to 4 words ("", "M", "MM", "MMM").

To convert any number, we just use simple math (division and remainder) to slice out each digit, grab its matching Roman translation from our lists, and glue them all together.

## Approach

- `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`: Creates an array of all possible Roman numeral representations for the ones place (from 0 to 9). Index 0 is empty because a digit of 0 contributes nothing.
- `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`: Creates an array of all possible Roman numeral representations for the tens place (from 0 to 9).
- `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`: Creates an array of all possible Roman numeral representations for the hundreds place (from 0 to 9).
- `string ths[]={"","M","MM","MMM"};`: Creates an array of all possible Roman numeral representations for the thousands place (from 0 to 3).
- `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];`: Extracts each place value using division (`/`) and remainder (`%`) math, looks up the matching strings in the arrays, and concatenates them together into one final string to return.

## Dry Run

### Case 1: Typical case (num = 3749)

| num (input) | num / 1000 | (num % 1000) / 100 | (num % 100) / 10 | num % 10 | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 3749 | 3 | 7 | 4 | 9 | Looks up index 3 in `ths` ("MMM"), index 7 in `hrns` ("DCC"), index 4 in `tens` ("XL"), and index 9 in `ones` ("IX"). Combines them into "MMMDCCXLIX". |

### Case 2: Edge case with subtractive forms (num = 58)

| num (input) | num / 1000 | (num % 1000) / 100 | (num % 100) / 10 | num % 10 | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 58 | 0 | 0 | 5 | 8 | Looks up index 0 in `ths` (""), index 0 in `hrns` (""), index 5 in `tens` ("L"), and index 8 in `ones` ("VIII"). Combines them into "LVIII". |

## Time & Space Complexity

- **Time:** O(1) — The code performs a fixed number of basic arithmetic operations and array lookups, regardless of how large the input number is (since the maximum input is constrained to 3999).
- **Space:** O(1) — The arrays use a tiny, fixed amount of memory that never grows.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already the most optimal possible complexity. Both time complexity and space complexity are O(1) (constant time and constant space). You cannot go faster than a fixed set of math lookups, and you cannot use less memory than a few tiny hardcoded string arrays. No further improvement is possible.

## Edge Cases Handled

- **Minimum value constraint (num = 1):** Handled cleanly because `1/1000` is 0, `(1%1000)/100` is 0, `(1%100)/10` is 0, and `1%10` is 1, yielding "I".
- **Maximum value constraint (num = 3999):** Handled because the thousands array accommodates up to 3 ("MMM"), and the other arrays accommodate up to 9 ("CM", "XC", "IX"), yielding "MMMCMXCIX".
- **Zeros in place values (e.g. num = 1000):** Handled because an index of 0 maps to an empty string `""` in our arrays, meaning it contributes no characters to the final output.
