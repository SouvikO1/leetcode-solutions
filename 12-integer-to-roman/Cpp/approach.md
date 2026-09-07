![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.05%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.48%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

This problem asks us to take a regular number (like 3749) and turn it into its Roman numeral equivalent (like "MMMDCCXLIX"). 

Roman numerals use combinations of seven letters:
- I = 1
- V = 5
- X = 10
- L = 50
- C = 100
- D = 500
- M = 1000

Instead of just adding symbols together blindly, Roman numerals follow a subtractive rule for numbers like 4, 9, 40, 90, 400, and 900. For example, instead of writing four "I"s for the number 4, we write "IV" (which means 5 minus 1). The input number will always be between 1 and 3999.

## Intuition

The "aha" moment for this problem is realizing that any number between 1 and 3999 can be broken down into its individual place values: thousands, hundreds, tens, and ones. 

Because the input has a strict upper limit (3999), there are only a fixed number of possibilities for each place value. For example, the hundreds place can only ever be values like 100 ("C"), 200 ("CC"), 400 ("CD"), up to 900 ("CM"), or nothing at all. 

Instead of writing complex loops or conditional statements to figure this out dynamically, we can pre-write every possible word piece for every place value in lookup lists (arrays). Then, converting a number is as simple as slicing out each digit (thousands, hundreds, tens, ones) and grabbing the exact matching translation from our pre-made lists.

## Approach

Here is how the code works, step-by-step:

- `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`: Creates an array holding every Roman numeral combination for the ones place (from 0 to 9). Index 0 is blank because a number might not have a ones digit (like 50 has nothing in the ones place).
- `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`: Creates an array holding every Roman numeral combination for the tens place (from 0 to 90).
- `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`: Creates an array holding every Roman numeral combination for the hundreds place (from 0 to 900).
- `string ths[]={"","M","MM","MMM"};`: Creates an array holding the thousands place combinations. It only goes up to 3000 ("MMM") because the constraints say the maximum input is 3999.
- `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];`: Takes the input number, uses math to isolate each place value digit, uses those digits as indexes to look up the correct Roman fragments from our arrays, and adds them all together into one final string to return.

## Dry Run

Let's trace two examples to see how the code processes them.

### Case 1: Typical case (num = 3749)

| Variable / Expression | Value | Action |
| :--- | :--- | :--- |
| `num` | 3749 | Starting input value. |
| `num / 1000` | 3 | Looks up index 3 in `ths`, getting `"MMM"`. |
| `(num % 1000) / 100` | 7 | Looks up index 7 in `hrns`, getting `"DCC"`. |
| `(num % 100) / 10` | 4 | Looks up index 4 in `tens`, getting `"XL"`. |
| `num % 10` | 9 | Looks up index 9 in `ones`, getting `"IX"`. |
| Final string combination | `"MMMDCCXLIX"` | Concatenates all parts together and returns. |

### Case 2: Edge case with subtractive forms (num = 58)

| Variable / Expression | Value | Action |
| :--- | :--- | :--- |
| `num` | 58 | Starting input value. |
| `num / 1000` | 0 | Looks up index 0 in `ths`, getting `""` (empty). |
| `(num % 1000) / 100` | 0 | Looks up index 0 in `hrns`, getting `""` (empty). |
| `(num % 100) / 10` | 5 | Looks up index 5 in `tens`, getting `"L"`. |
| `num % 10` | 8 | Looks up index 8 in `ones`, getting `"VIII"`. |
| Final string combination | `"LVIII"` | Concatenates all parts together and returns. |

## Time & Space Complexity

- **Time:** O(1) — The code performs a fixed number of basic arithmetic operations and string lookups regardless of the input size (since the input is strictly capped at 3999).
- **Space:** O(1) — The arrays (`ones`, `tens`, `hrns`, `ths`) take a tiny, fixed amount of memory that never grows.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already optimal. Both time complexity and space complexity are O(1) (constant time and constant space). You cannot go faster than a single pass of basic math lookups, and you cannot use less memory than a few tiny, fixed-size string arrays.

## Edge Cases Handled

- **Numbers with missing place values (like 50):** Handled cleanly because the index arrays start with an empty string (`""`), so missing digits contribute nothing to the final string instead of causing errors.
- **Subtractive forms (like 4 or 9):** Handled because the lookup arrays explicitly include the correct Roman representations for values like "IV" or "XC" at indices 4 and 9.
- **Maximum boundary constraint (3999):** Handled because the `ths` array provides combinations up to index 3 ("MMM"), which matches the upper limit of the problem constraints.
