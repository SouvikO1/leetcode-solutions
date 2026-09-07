![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.05%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.48%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The problem asks us to take a normal integer (like 3749) and translate it into a **Roman numeral string** (like "MMMDCCXLIX"). 

Roman numerals use seven core symbols:
- I = 1
- V = 5
- X = 10
- L = 50
- C = 100
- D = 500
- M = 1000

Instead of just adding symbols together blindly, Roman numerals follow strict rules. Powers of 10 (I, X, C, M) can only be repeated up to three times consecutively. When you need to represent numbers like 4 or 9, you use a **subtractive form** (placing a smaller value before a larger one). For example, 4 is written as IV (1 before 5), and 9 is written as IX (1 before 10). 

We are given a number between 1 and 3999, and our job is to output the correct Roman numeral string following these exact rules.

## Intuition

The "aha" moment for this problem comes from realizing that **any number up to 3999 can be broken down strictly by its decimal places**: thousands, hundreds, tens, and ones. 

Because the input has a strict upper limit of 3999, there are only a fixed number of possibilities for each decimal place:
- The thousands place can only be 0, 1000, 2000, or 3000.
- The hundreds place can only be anything from 0 to 900.
- The tens place can only be anything from 0 to 90.
- The ones place can only be anything from 0 to 9.

Instead of writing complex loops or conditional statements to figure out how to build the string piece by piece, we can pre-build lookup lists for every single possible digit value from 0 to 9 in each decimal position. Then, we can use simple math (division and remainder) to extract each digit of our input number and instantly grab its Roman numeral translation from our lists.

## Approach

Here is how the code executes step-by-step:

- `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`: Creates a list of all possible Roman numeral representations for the ones place (digits 0 through 9).
- `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`: Creates a list of all possible Roman numeral representations for the tens place (multiples of 10 from 0 to 90).
- `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`: Creates a list of all possible Roman numeral representations for the hundreds place (multiples of 100 from 0 to 900).
- `string ths[]={"","M","MM","MMM"};`: Creates a list of all possible Roman numeral representations for the thousands place (multiples of 1000 from 0 to 3000).
- `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];`: Extracts each place value using division and remainder operations, looks up the corresponding Roman string in each array, and adds them together from largest to smallest place value to form the final result.

## Dry Run

Let us trace the code using two examples: one typical case and one boundary/edge case.

### Case 1: Typical case (num = 3749)

| num (input) | num / 1000 | (num % 1000) / 100 | (num % 100) / 10 | num % 10 | Lookup and Result |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 3749 | 3 | 7 | 4 | 9 | ths[3] ("MMM") + hrns[7] ("DCC") + tens[4] ("XL") + ones[9] ("IX") -> "MMMDCCXLIX" |

### Case 2: Edge case with small value and subtractive forms (num = 58)

| num (input) | num / 1000 | (num % 1000) / 100 | (num % 100) / 10 | num % 10 | Lookup and Result |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 58 | 0 | 0 | 5 | 8 | ths[0] ("") + hrns[0] ("") + tens[5] ("L") + ones[8] ("VIII") -> "LVIII" |

## Time & Space Complexity

- **Time:** O(1) — The code performs a fixed number of basic math operations (division, modulo) and array lookups regardless of the input value. Because the input is capped at 3999, it never scales with a growing data size.
- **Space:** O(1) — The four string arrays have a fixed, constant size (10, 10, 10, and 4 elements) and require a constant amount of memory.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already the most optimal possible solution. 
- **Time complexity:** O(1) is the absolute best theoretical time complexity because you cannot process a number faster than a constant set of math operations.
- **Space complexity:** O(1) is optimal because the lookup storage requirements never grow; they remain completely static. No further improvements are possible.

## Edge Cases Handled

- **Minimum value constraint (num = 1):** Correctly handles single-digit inputs by returning "I" using the ones array while other arrays evaluate to empty strings.
- **Maximum value constraint (num = 3999):** Correctly handles the highest possible constrained input, mapping it to "MMMCMXCIX".
- **Zeros in place values:** Handles numbers with zeros (like 1994, where the tens place yields 90 and ones yield 4, but no tens require special handling) because the index 0 in every array points to an empty string (""), which safely contributes nothing to the final concatenation.
- **Subtractive forms (4 and 9 patterns):** Correctly captures values requiring "IV", "IX", "XL", "XC", "CD", and "CM" because they are hardcoded directly into the lookup tables at their respective indices.
