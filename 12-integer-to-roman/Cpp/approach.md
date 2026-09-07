![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.16%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.61%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to convert a standard decimal number (like 3749) into its Roman numeral string representation (like "MMMDCCXLIX"). 

Roman numerals build values by combining these basic symbols:
* **I** = 1
* **V** = 5
* **X** = 10
* **L** = 50
* **C** = 100
* **D** = 500
* **M** = 1000

Roman numbers are written from largest digit to smallest digit, left to right. However, instead of repeating a symbol 4 times (like "IIII" for 4), Roman numerals use a subtractive pattern:
* 4 is written as **IV** (1 less than 5)
* 9 is written as **IX** (1 less than 10)
* 40 is written as **XL** (10 less than 50)
* 90 is written as **XC** (10 less than 100)
* 400 is written as **CD** (100 less than 500)
* 900 is written as **CM** (100 less than 1000)

The problem guarantees the input `num` is between 1 and 3999.

---

## Intuition

Every decimal number can be broken down into place values: thousands, hundreds, tens, and ones. For example, 1994 is 1000 + 900 + 90 + 4.

Because Roman numerals treat each place value independently, the digit at each place value (0 through 9) always maps to the exact same Roman numeral string. 

* In the ones place, a 4 is always `"IV"`.
* In the tens place, a 4 represents 40, which is always `"XL"`.
* In the hundreds place, a 4 represents 400, which is always `"CD"`.
* In the thousands place, a 3 represents 3000, which is always `"MMM"`.

Instead of running loops or doing subtraction repeatedly, we can hardcode the Roman representations for every digit (0–9) across all four place values into lookup arrays. Then, we extract each digit using simple math and combine the results.

---

## Approach

* `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`: Defines a lookup table for the ones place (0 to 9). Index 0 is an empty string `""` because a zero adds no symbols.
* `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`: Defines a lookup table for the tens place (0, 10, 20, ..., 90).
* `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`: Defines a lookup table for the hundreds place (0, 100, 200, ..., 900).
* `string ths[]={"","M","MM","MMM"};`: Defines a lookup table for the thousands place (0, 1000, 2000, 3000). Since `num` is at most 3999, we only need values up to 3000.
* `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];`: Calculates the digit at each place value using division and modulo arithmetic, retrieves the matching string from each array, and concatenates them from left to right:
  * `num / 1000` gets the digit in the thousands place.
  * `(num % 1000) / 100` strips the thousands and gets the digit in the hundreds place.
  * `(num % 100) / 10` strips the hundreds and gets the digit in the tens place.
  * `num % 10` gets the digit in the ones place.

---

## Dry Run

### Case 1: Complex number with 4s and 9s (`num = 1994`)

| Step | Expression Evaluated | Index Calculated | Value Looked Up | Running String |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `num / 1000` | 1994 / 1000 = **1** | `ths[1]` -> `"M"` | `"M"` |
| 2 | `(num % 1000) / 100` | 994 / 100 = **9** | `hrns[9]` -> `"CM"` | `"MCM"` |
| 3 | `(num % 100) / 10` | 94 / 10 = **9** | `tens[9]` -> `"XC"` | `"MCMXC"` |
| 4 | `num % 10` | 1994 % 10 = **4** | `ones[4]` -> `"IV"` | `"MCMXCIV"` |

**Final Result:** `"MCMXCIV"`

---

### Case 2: Small number under 100 (`num = 58`)

| Step | Expression Evaluated | Index Calculated | Value Looked Up | Running String |
| :--- | :--- | :--- | :--- | :--- |
| 1 | `num / 1000` | 58 / 1000 = **0** | `ths[0]` -> `""` | `""` |
| 2 | `(num % 1000) / 100` | 58 / 100 = **0** | `hrns[0]` -> `""` | `""` |
| 3 | `(num % 100) / 10` | 58 / 10 = **5** | `tens[5]` -> `"L"` | `"L"` |
| 4 | `num % 10` | 58 % 10 = **8** | `ones[8]` -> `"VIII"` | `"LVIII"` |

**Final Result:** `"LVIII"`

---

## Time & Space Complexity

* **Time Complexity:** **O(1)** — Constant time. The code performs exactly four math operations and four array lookups regardless of the input value.
* **Space Complexity:** **O(1)** — Constant space. The lookup arrays have fixed sizes (34 string elements total across all arrays), consuming a small, fixed amount of memory.

### Is this optimal?
Yes, this solution is already optimal. Because the input constraint limits `num` to a maximum of 3999, the number of digits is bounded at 4. Any algorithm for this problem will run in constant time O(1) and use constant space O(1). No further optimizations can improve the time or space complexity classes.

---

## Edge Cases Handled

* **Zeroes in place values (e.g., `num = 1004`):** The code evaluates the zero digits in the hundreds and tens place to index `0`. Indexes `hrns[0]` and `tens[0]` return empty strings `""`, producing `"MIV"` cleanly without extra characters or errors.
* **Subtractive cases (4s and 9s):** Values like 4, 9, 40, 90, 400, and 900 are pre-coded directly into the lookup arrays (`"IV"`, `"IX"`, `"XL"`, `"XC"`, `"CD"`, `"CM"`), eliminating any need for special conditional logic.
* **Minimum bound (`num = 1`):** Thousands, hundreds, and tens resolve to index `0` (`""`), while `ones[1]` returns `"I"`.
* **Maximum bound (`num = 3999`):** Thousands evaluates to `ths[3]` (`"MMM"`), hundreds to `hrns[9]` (`"CM"`), tens to `tens[9]` (`"XC"`), and ones to `ones[9]` (`"IX"`), producing `"MMMCMXCIX"`.
