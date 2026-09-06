![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.16%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.61%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to convert an integer `num` into a Roman numeral string. The input number `num` is always between 1 and 3999.

Roman numerals are written using seven basic symbols:
* **I** = 1
* **V** = 5
* **X** = 10
* **L** = 50
* **C** = 100
* **D** = 500
* **M** = 1000

Roman numerals are built from left to right, matching each decimal place value (thousands, hundreds, tens, ones). 

Usually, values are formed by adding symbols together (like 6 = `VI` or 70 = `LXX`). However, values starting with 4 or 9 use **subtractive forms** to avoid repeating a symbol 4 times:
* 4 = `IV` (1 before 5)
* 9 = `IX` (1 before 10)
* 40 = `XL` (10 before 50)
* 90 = `XC` (10 before 100)
* 400 = `CD` (100 before 500)
* 900 = `CM` (100 before 1000)

**Example:** If `num = 1994`:
* Thousands place: 1000 -> `M`
* Hundreds place: 900 -> `CM`
* Tens place: 90 -> `XC`
* Ones place: 4 -> `IV`
* Full result: `"MCMXCIV"`

---

## Intuition

Since the input `num` is capped at 3999, every digit place (thousands, hundreds, tens, ones) can only take a limited set of values (0 through 9, or 0 through 3 for thousands).

Instead of running loops or writing many `if-else` branches to handle subtractive pairs like 4 and 9, we can **precompute** the Roman string for every possible digit value at each place value.

For example, for the ones place, digit 4 is always `"IV"` and digit 9 is always `"IX"`. By storing all 10 possibilities for each position in array lookup tables, we can extract each digit using simple math (division and remainder) and directly pick out the matching Roman text.

---

## Approach

* `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};`
  Stores all Roman forms for the ones digit (0 through 9). Index 0 gives an empty string `""`, index 4 gives `"IV"`, index 9 gives `"IX"`, and so on.
* `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};`
  Stores all Roman forms for the tens digit (0, 10, 20, ..., 90), indexed directly by the digit value 0 through 9.
* `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};`
  Stores all Roman forms for the hundreds digit (0, 100, 200, ..., 900), indexed directly by the digit value 0 through 9.
* `string ths[]={"","M","MM","MMM"};`
  Stores all Roman forms for the thousands digit (0, 1000, 2000, 3000), indexed by digit value 0 through 3.
* `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];`
  Uses integer division (`/`) and modulo (`%`) to isolate the individual digit at each place value. It looks up the matching string fragment from each array and concatenates them from highest place value to lowest.

---

## Dry Run

### Case 1: Standard number with subtractive values (`num = 1994`)

| Step | Place Value | Math Expression | Extracted Digit | Array Looked Up | Looked Up String | Running Result |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Thousands | `num / 1000` | 1 | `ths[1]` | `"M"` | `"M"` |
| 2 | Hundreds | `(num % 1000) / 100` | 9 | `hrns[9]` | `"CM"` | `"MCM"` |
| 3 | Tens | `(num % 100) / 10` | 9 | `tens[9]` | `"XC"` | `"MCMXC"` |
| 4 | Ones | `num % 10` | 4 | `ones[4]` | `"IV"` | `"MCMXCIV"` |

Final Output: `"MCMXCIV"`

### Case 2: Number with zero place values (`num = 58`)

| Step | Place Value | Math Expression | Extracted Digit | Array Looked Up | Looked Up String | Running Result |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Thousands | `num / 1000` | 0 | `ths[0]` | `""` | `""` |
| 2 | Hundreds | `(num % 1000) / 100` | 0 | `hrns[0]` | `""` | `""` |
| 3 | Tens | `(num % 100) / 10` | 5 | `tens[5]` | `"L"` | `"L"` |
| 4 | Ones | `num % 10` | 8 | `ones[8]` | `"VIII"` | `"LVIII"` |

Final Output: `"LVIII"`

---

## Time & Space Complexity

* **Time:** O(1) — The code performs a fixed set of basic arithmetic operations (4 divisions/modulos) and four direct array lookups. The execution time does not grow with `num`.
* **Space:** O(1) — The lookup arrays have a fixed size containing 33 string elements in total. The resulting output string is at most 15 characters long.

### Is this optimal?

**Yes, this solution is optimal.** 

The theoretical limit for this problem is O(1) time and O(1) space because the input size is bounded (up to 3999). Direct array lookups run in constant time, which cannot be improved algorithmically.

A minor C++ micro-optimization is declaring the lookup arrays as `const static`. This prevents creating array objects on the stack every time the function is called:

```cpp
const static string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};
const static string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};
const static string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};
const static string ths[]  = {"","M","MM","MMM"};
```

This tiny adjustment avoids repetitive string allocation overhead without changing the O(1) Big-O time complexity.

---

## Edge Cases Handled

* **Smallest boundary value (`num = 1`)**: Extracts digit 0 for thousands, hundreds, and tens, mapping them to `""`. Extracts digit 1 for ones, giving `"I"`.
* **Largest boundary value (`num = 3999`)**: Extracts 3 for thousands (`"MMM"`), 9 for hundreds (`"CM"`), 9 for tens (`"XC"`), and 9 for ones (`"IX"`), returning `"MMMCMXCIV"`.
* **Numbers with zeros in digit positions (e.g., `num = 1000` or `num = 508`)**: Digit 0 maps to `""` in the lookup arrays, safely skipping empty place values during string concatenation.
* **Numbers requiring subtractive forms (4s and 9s)**: All subtractive combinations (`IV`, `IX`, `XL`, `XC`, `CD`, `CM`) are stored directly in the arrays, handling special Roman rules automatically.
