![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.17%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.74%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to convert an integer between 1 and 3999 into a **Roman numeral**.

Roman numerals use seven basic symbols:
* **I** = 1
* **V** = 5
* **X** = 10
* **L** = 50
* **C** = 100
* **D** = 500
* **M** = 1000

Normally, symbols are added together from left to right (for example, 15 is **XV**, which is 10 + 5). However, if a smaller value comes before a larger value, it means subtraction:
* 4 is **IV** (5 - 1)
* 9 is **IX** (10 - 1)
* 40 is **XL** (50 - 10)
* 90 is **XC** (100 - 10)
* 400 is **CD** (500 - 100)
* 900 is **CM** (1000 - 100)

To convert any number, you break it down by place value (thousands, hundreds, tens, ones) and convert each part to Roman numerals.

For example, 3749 breaks down into:
* **3000** = MMM
* **700** = DCC
* **40** = XL
* **9** = IX

Putting them together gives **MMMDCCXLIX**.

---

## Intuition

Because the input is capped at 3999, each decimal place can only be a digit from 0 to 9 (or 0 to 3 for thousands). 

Instead of writing complex rules or loops to subtract values repeatedly, we can pre-build lookup tables for every single digit (0 through 9) at each place value (ones, tens, hundreds, thousands). 

Extracting each digit using simple division and modulo arithmetic lets us grab the matching Roman string fragment instantly.

---

## Approach

Here is how the code works step-by-step:

* `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`  
  Creates a lookup array for the ones place (digits 0 through 9). Index 0 is an empty string `""` so zero adds nothing to the output.

* `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`  
  Creates a lookup array for the tens place (0, 10, 20, ..., 90).

* `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`  
  Creates a lookup array for the hundreds place (0, 100, 200, ..., 900).

* `string ths[]={"","M","MM","MMM"};`  
  Creates a lookup array for the thousands place (0, 1000, 2000, 3000).

* `ths[num/1000]`  
  Divides `num` by 1000 to get the thousands digit (0, 1, 2, or 3) and looks up its Roman representation.

* `hrns[(num%1000)/100]`  
  Uses the modulo operator `% 1000` to strip away the thousands digit, then divides by 100 to isolate the hundreds digit (0 through 9) and look it up.

* `tens[(num%100)/10]`  
  Uses `% 100` to remove the hundreds and thousands, then divides by 10 to extract the tens digit (0 through 9) for lookup.

* `ones[num%10]`  
  Uses `% 10` to isolate the final ones digit (0 through 9) and look it up.

* `return ths[...] + hrns[...] + tens[...] + ones[...]`  
  Concatenates the four Roman string pieces together from highest place value to lowest and returns the final string.

---

## Dry Run

### Case 1: Standard number with subtractive forms (`num = 3749`)

| Step | Expression | Value Evaluated | Lookup Result | Combined Result | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `num / 1000` | 3749 / 1000 = 3 | `ths[3]` = "MMM" | "MMM" | Extracted thousands place |
| 2 | `(num % 1000) / 100` | 749 / 100 = 7 | `hrns[7]` = "DCC" | "MMMDCC" | Extracted hundreds place |
| 3 | `(num % 100) / 10` | 49 / 10 = 4 | `tens[4]` = "XL" | "MMMDCCXL" | Extracted tens place |
| 4 | `num % 10` | 3749 % 10 = 9 | `ones[9]` = "IX" | "MMMDCCXLIX" | Extracted ones place |

Final returned string: **"MMMDCCXLIX"**

---

### Case 2: Number with subtractive forms (`num = 1994`)

| Step | Expression | Value Evaluated | Lookup Result | Combined Result | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `num / 1000` | 1994 / 1000 = 1 | `ths[1]` = "M" | "M" | Extracted thousands place |
| 2 | `(num % 1000) / 100` | 994 / 100 = 9 | `hrns[9]` = "CM" | "MCM" | Extracted hundreds place |
| 3 | `(num % 100) / 10` | 94 / 10 = 9 | `tens[9]` = "XC" | "MCMXC" | Extracted tens place |
| 4 | `num % 10` | 1994 % 10 = 4 | `ones[4]` = "IV" | "MCMXCIV" | Extracted ones place |

Final returned string: **"MCMXCIV"**

---

## Time & Space Complexity

* **Time Complexity:** O(1) — The algorithm performs a fixed number of arithmetic operations (4 divisions/modulos) and array lookups regardless of how large `num` is.
* **Space Complexity:** O(1) — The four lookup arrays hold a constant number of small strings (34 string elements total), taking a fixed amount of memory.

### Is this solution optimal?
**Yes, this is already optimal.** Both time and space complexities are O(1), which is the theoretical best possible performance because the input size is capped at 3999. No further optimizations can improve upon constant time and space.

---

## Edge Cases Handled

* **Minimum Constraint (`num = 1`)**: Thousands, hundreds, and tens return `0` (mapping to `""`), while ones returns `"I"`. The result correctly yields `"I"`.
* **Maximum Constraint (`num = 3999`)**: Thousands digit is 3 (`"MMM"`), which is safely within the bounds of `ths`. The result yields `"MMMCMXCIX"`.
* **Zeroes in place values (e.g., `num = 1004`)**: Middle digits evaluate to index `0`, mapping to `""`. The expression computes `"M" + "" + "" + "IV"`, producing `"MIV"` cleanly.
* **Subtractive cases (digits 4 and 9)**: Subtractive strings like `"IV"`, `"IX"`, `"XL"`, `"XC"`, `"CD"`, and `"CM"` are explicitly defined in the lookup arrays, eliminating special branching logic.
