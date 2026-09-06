![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.16%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.61%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

We need to convert a standard integer (like 3749) into its Roman numeral equivalent (like "MMMDCCXLIX"). 

Roman numerals use seven core symbols:
- I = 1
- V = 5
- X = 10
- L = 50
- C = 100
- D = 500
- M = 1000

They are built from highest place value to lowest. For example, thousands first, then hundreds, then tens, and finally ones. 

Usually, you just stack symbols together (like 3000 = MMM). But there is a special rule for numbers that involve 4 or 9. Instead of writing four identical symbols in a row (like IIII for 4), you use a subtractive notation. You place a smaller value symbol *before* a larger value symbol. For example, 4 is written as IV (1 less than 5), and 9 is written as IX (1 less than 10). The allowed subtractive forms are IV (4), IX (9), XL (40), XC (90), CD (400), and CM (900).

The problem gives numbers between 1 and 3999.

## Intuition

The "aha" moment for this problem comes from realizing that Roman numerals are completely **positional** at their core, just like our decimal system. 

A number like 3749 is just 3000 + 700 + 40 + 9. 

Instead of writing a complicated loop to subtract values one by one, we can look at each decimal place independently:
- The thousands digit is 3 ("MMM")
- The hundreds digit is 7 ("DCC")
- The tens digit is 4 ("XL")
- The ones digit is 9 ("IX")

Since the input is capped at 3999, there are only 10 possible values for each place (0 through 9). We can prewrite all possible Roman numeral translations for each place value in small arrays. Then, translating any number is just a matter of extracting its digits using division and modulo arithmetic, and gluing the prewritten pieces together.

## Approach

Here is how the code implements this step-by-step:

- `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`
  We create an array containing all possible Roman numeral representations for the ones place (from 0 to 9). Index 0 is empty (for when a digit is 0), index 1 is "I", index 4 is "IV", and so on.
- `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`
  We create an array for the tens place, covering values from 0 (empty string) up to 90 ("XC").
- `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`
  We create an array for the hundreds place, covering values from 0 up to 900 ("CM").
- `string ths[]={"","M","MM","MMM"};`
  We create an array for the thousands place. Since the maximum input is 3999, the thousands digit can only range from 0 to 3.
- `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];`
  We extract each digit of the input `num` using division and modulo (remainder) operations:
  - `num/1000` gets the thousands digit.
  - `(num%1000)/100` strips away the thousands, then extracts the hundreds digit.
  - `(num%100)/10` strips away higher places, then extracts the tens digit.
  - `num%10` extracts the ones digit.
  We use these digits as indices to look up the correct strings in our four arrays, concatenate them together using the plus operator, and return the final combined string.

## Dry Run

### Case 1: Typical case (num = 3749)

| Step | num / 1000 | (num % 1000) / 100 | (num % 100) / 10 | num % 10 | Array Lookups | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 3 | 7 | 4 | 9 | `ths[3]` = "MMM"<br>`hrns[7]` = "DCC"<br>`tens[4]` = "XL"<br>`ones[9]` = "IX" | Extract each digit and look up its Roman equivalent |
| 2 | - | - | - | - | "MMM" + "DCC" + "XL" + "IX" | Concatenate all parts and return "MMMDCCXLIX" |

### Case 2: Edge case with zeros and subtractive forms (num = 58)

| Step | num / 1000 | (num % 1000) / 100 | (num % 100) / 10 | num % 10 | Array Lookups | Action |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | 0 | 0 | 5 | 8 | `ths[0]` = ""<br>`hrns[0]` = ""<br>`tens[5]` = "L"<br>`ones[8]` = "VIII" | Thousands and hundreds evaluate to 0, tens is 5, ones is 8 |
| 2 | - | - | - | - | "" + "" + "L" + "VIII" | Combine the results, ignoring empty strings, to return "LVIII" |

## Time & Space Complexity

- **Time:** O(1) — The code performs a fixed number of basic arithmetic operations (divisions, modulos) and array lookups. It does not loop through the input size. Therefore, execution time is constant regardless of how large the number is.
- **Space:** O(1) — The four arrays take a tiny, fixed amount of memory (totaling 23 small strings) that never grows based on the input.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already at optimal O(1) time and O(1) space complexity. Because the input integer is strictly bounded between 1 and 3999, the output string can never exceed 15 characters in length. No algorithm can process a bounded input faster than constant time. No further improvement is possible.

## Edge Cases Handled

- **Numbers with zeros in a middle place (e.g., 109):** The modulo and division arithmetic extracts 0 for the tens place (`(109 % 100) / 10 = 0`), which correctly maps to an empty string `""` in the `tens` array, skipping that place cleanly.
- **Subtractive forms (4, 9, 40, etc.):** Because every single combination from 1 to 9 for every place value is explicitly hardcoded into the arrays (like `IV` for 4 or `XC` for 90), subtractive forms are handled instantly without complex conditional logic.
- **Maximum constraint boundary (3999):** The `ths` array safely handles up to index 3 ("MMM"), which is the largest thousands value possible under the 3999 constraint.
