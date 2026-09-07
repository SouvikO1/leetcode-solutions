![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.05%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.48%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to convert a standard decimal integer (from 1 to 3999) into a string representing its **Roman numeral** form. 

Roman numerals use seven base characters:
* `I` = 1
* `V` = 5
* `X` = 10
* `L` = 50
* `C` = 100
* `D` = 500
* `M` = 1000

Roman numbers are formed by writing each place value (thousands, hundreds, tens, ones) from left to right. Standard addition applies (e.g., `VI` is 5 + 1 = 6), except when a smaller value appears before a larger value to represent subtraction (e.g., `IV` is 4, `IX` is 9, `XL` is 40, `XC` is 90, `CD` is 400, and `CM` is 900).

For example, to convert **1994**:
* Thousands: 1000 = `M`
* Hundreds: 900 = `CM`
* Tens: 90 = `XC`
* Ones: 4 = `IV`
* Result: `"MCMXCIV"`

---

## Intuition

Because the input integer `num` is guaranteed to be between 1 and 3999, each digit position (thousands, hundreds, tens, ones) can only take a tiny set of values (0 through 9, or 0 through 3 for thousands). 

Instead of doing loops or subtraction steps to figure out the Roman numerals piece-by-piece, we can pre-build a **lookup table** (a fixed array of pre-calculated values) for every possible digit at every position. 

Once we break down the number into its place values using simple math—**integer division** (which discards the fractional part) and **modulo** (which calculates the remainder after division)—we can immediately fetch the matching Roman string for each position and stick them together.

---

## Approach

* `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};` — Creates a lookup array for the ones place (0 to 9). Index 0 is an empty string `""` so zero digits add nothing to the output.
* `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};` — Creates a lookup array for the tens place (0, 10, 20, ..., 90).
* `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};` — Creates a lookup array for the hundreds place (0, 100, 200, ..., 900).
* `string ths[] = {"","M","MM","MMM"};` — Creates a lookup array for the thousands place (0, 1000, 2000, 3000).
* `ths[num/1000]` — Divides `num` by 1000 to extract the thousands digit (0 to 3) and looks up its Roman symbol.
* `hrns[(num%1000)/100]` — Takes `num % 1000` (the remainder after removing thousands), divides by 100 to get the hundreds digit (0 to 9), and looks up its Roman symbol.
* `tens[(num%100)/10]` — Takes `num % 100` (the remainder after removing hundreds and thousands), divides by 10 to get the tens digit (0 to 9), and looks up its Roman symbol.
* `ones[num%10]` — Takes `num % 10` to get the last digit (0 to 9) and looks up its Roman symbol.
* `return ths[...] + hrns[...] + tens[...] + ones[...]` — Concatenates (joins together) all four Roman sub-strings in left-to-right order and returns the final string.

---

## Dry Run

### Case 1: Complex case with subtractive forms (`num = 1994`)

| Place Value | Expression | Digit Evaluated | Symbol Picked | Running Result |
| :--- | :--- | :--- | :--- | :--- |
| **Thousands** | `num / 1000` | 1994 / 1000 = 1 | `ths[1]` = "M" | "M" |
| **Hundreds** | `(num % 1000) / 100` | 994 / 100 = 9 | `hrns[9]` = "CM" | "MCM" |
| **Tens** | `(num % 100) / 10` | 94 / 10 = 9 | `tens[9]` = "XC" | "MCMXC" |
| **Ones** | `num % 10` | 1994 % 10 = 4 | `ones[4]` = "IV" | "MCMXCIV" |

**Final Return:** `"MCMXCIV"`

---

### Case 2: Smaller number with zeros (`num = 58`)

| Place Value | Expression | Digit Evaluated | Symbol Picked | Running Result |
| :--- | :--- | :--- | :--- | :--- |
| **Thousands** | `num / 1000` | 58 / 1000 = 0 | `ths[0]` = "" | "" |
| **Hundreds** | `(num % 1000) / 100` | 58 / 100 = 0 | `hrns[0]` = "" | "" |
| **Tens** | `(num % 100) / 10` | 58 / 10 = 5 | `tens[5]` = "L" | "L" |
| **Ones** | `num % 10` | 58 % 10 = 8 | `ones[8]` = "VIII" | "LVIII" |

**Final Return:** `"LVIII"`

---

## Time & Space Complexity

* **Time Complexity:** **O(1)** — The execution time is constant. Regardless of what `num` is, the code executes exactly 4 arithmetic calculations, 4 array lookups, and concatenates 4 short strings.
* **Space Complexity:** **O(1)** — Memory usage is fixed. The string arrays hold a total of 33 tiny strings that never change regardless of the input size.

### Is this optimal?
**Yes, this is already fully optimal.** 

Since the problem constraints limit inputs to 1 through 3999, an algorithm running in **O(1) time** and **O(1) space** achieves the theoretical lower bound. No algorithmic changes can improve upon constant time and space.

---

## Edge Cases Handled

* **Zero digits in intermediate place values (e.g., 1004, 50):** Handled cleanly because index `0` in `ones`, `tens`, `hrns`, and `ths` maps to an empty string `""`, adding nothing to the string output.
* **Boundary minimum value (`num = 1`):** `ths[0]`, `hrns[0]`, and `tens[0]` evaluate to `""`, while `ones[1]` produces `"I"`.
* **Boundary maximum value (`num = 3999`):** `ths[3]` produces `"MMM"`, `hrns[9]` produces `"CM"`, `tens[9]` produces `"XC"`, and `ones[9]` produces `"IX"`, correctly giving `"MMMCMXCIX"`.
* **Subtractive forms (4, 9, 40, 90, 400, 900):** Explicitly encoded inside the arrays (e.g., `"IV"`, `"IX"`, `"XL"`), bypassing the need for complex branch logic or conditional statements.
