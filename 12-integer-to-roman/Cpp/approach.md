![Runtime](https://img.shields.io/badge/Runtime-1%20ms%20(beats%2076.20%25)-green?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-9.2%20MB%20(beats%2082.71%25)-brightgreen?style=for-the-badge)

---

## Problem Explained

The goal is to convert a standard base-10 integer (from 1 to 3999) into a string representing its **Roman numeral** equivalent.

Roman numerals are written from left to right, starting with the largest place value (thousands) down to the smallest (ones). The standard symbol values are:
* **I** = 1
* **V** = 5
* **X** = 10
* **L** = 50
* **C** = 100
* **D** = 500
* **M** = 1000

Normally, symbols are repeated to add up to a value (for example, 3 is **III**). However, standard Roman numerals do not allow repeating a symbol 4 times in a row. Instead, special **subtractive forms** are used for values starting with 4 or 9:
* 4 is **IV** (1 before 5) and 9 is **IX** (1 before 10)
* 40 is **XL** (10 before 50) and 90 is **XC** (10 before 100)
* 400 is **CD** (100 before 500) and 900 is **CM** (100 before 1000)

**Example:**
If the input is `3749`, we break it into digit places:
* 3000 -> **MMM**
* 700 -> **DCC**
* 40 -> **XL**
* 9 -> **IX**

Putting them together gives `"MMMDCCXLIX"`.

---

## Intuition

Every integer between 1 and 3999 can be split into four distinct place values: **thousands**, **hundreds**, **tens**, and **ones**.

Since each place value only ever has a single digit (0 through 9), there are only a tiny, finite set of Roman patterns for each place. For instance, the ones place can only ever be one of ten options: `""`, `"I"`, `"II"`, `"III"`, `"IV"`, `"V"`, `"VI"`, `"VII"`, `"VIII"`, or `"IX"`.

The "aha" moment is that we do not need complex conditional statements or loops to deal with subtraction rules. We can pre-build lookup tables for each digit position from 0 to 9. Then, we simply extract each digit using math (division and modulo) and grab the matching Roman string directly.

---

## Approach

* `string ones[] = {"","I","II","III","IV","V","VI","VII","VIII","IX"};` — Creates a lookup table for the ones digit (values 0 through 9). It directly embeds the special subtractive forms `"IV"` for 4 and `"IX"` for 9.
* `string tens[] = {"","X","XX","XXX","XL","L","LX","LXX","LXXX","XC"};` — Creates a lookup table for the tens digit (values 0, 10, 20, ..., 90). It includes `"XL"` for 40 and `"XC"` for 90.
* `string hrns[] = {"","C","CC","CCC","CD","D","DC","DCC","DCCC","CM"};` — Creates a lookup table for the hundreds digit (values 0, 100, 200, ..., 900). It includes `"CD"` for 400 and `"CM"` for 900.
* `string ths[]={"","M","MM","MMM"};` — Creates a lookup table for the thousands digit (values 0, 1000, 2000, 3000). Since input goes up to 3999, index 3 (`"MMM"`) is the maximum needed.
* `return ths[num/1000] + hrns[(num%1000)/100] + tens[(num%100)/10] + ones[num%10];` — Breaks down `num` into four single digits using integer arithmetic, looks up the corresponding Roman string for each position, concatenates them from left to right, and returns the result.

---

## Dry Run

### Test Case 1: Standard case with subtractive forms (`num = 1994`)

| `num` | `ths[num/1000]` | `hrns[(num%1000)/100]` | `tens[(num%100)/10]` | `ones[num%10]` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `1994` | `ths[1]` -> `"M"` | — | — | — | Extract thousands digit: `1994 / 1000 = 1`. |
| `1994` | `"M"` | `hrns[9]` -> `"CM"` | — | — | Extract hundreds digit: `(1994 % 1000) / 100 = 9`. |
| `1994` | `"M"` | `"CM"` | `tens[9]` -> `"XC"` | — | Extract tens digit: `(1994 % 100) / 10 = 9`. |
| `1994` | `"M"` | `"CM"` | `"XC"` | `ones[4]` -> `"IV"` | Extract ones digit: `1994 % 10 = 4`. |
| `1994` | `"M"` | `"CM"` | `"XC"` | `"IV"` | Combine strings to get `"MCMXCIV"`. |

### Test Case 2: Number with zero place values (`num = 58`)

| `num` | `ths[num/1000]` | `hrns[(num%1000)/100]` | `tens[(num%100)/10]` | `ones[num%10]` | Action |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `58` | `ths[0]` -> `""` | — | — | — | Extract thousands digit: `58 / 1000 = 0`. |
| `58` | `""` | `hrns[0]` -> `""` | — | — | Extract hundreds digit: `(58 % 1000) / 100 = 0`. |
| `58` | `""` | `""` | `tens[5]` -> `"L"` | — | Extract tens digit: `(58 % 100) / 10 = 5`. |
| `58` | `""` | `""` | `"L"` | `ones[8]` -> `"VIII"` | Extract ones digit: `58 % 10 = 8`. |
| `58` | `""` | `""` | `"L"` | `"VIII"` | Combine strings to get `"LVIII"`. |

---

## Time & Space Complexity

* **Time Complexity:** **O(1)** — The operations consist of basic arithmetic (four division/modulo steps) and four constant-time array lookups. The execution steps are fixed regardless of how large `num` is.
* **Space Complexity:** **O(1)** — The lookup arrays (`ones`, `tens`, `hrns`, `ths`) store a fixed total of 33 short strings. Memory usage is constant and does not grow.

**Is this optimal?**
Yes, this code is fully optimal. The maximum possible integer is bounded at 3999, meaning the input size is strictly constrained. Because both runtime and extra space execute in a constant number of operations, this approach achieves theoretical optimal bounds of **O(1) Time** and **O(1) Space**. No further algorithmic improvements are possible.

---

## Edge Cases Handled

* **Minimum Constraint Value (`num = 1`):** Thousands, hundreds, and tens digits evaluate to 0 (`""`), mapping correctly to output `"I"`.
* **Maximum Constraint Value (`num = 3999`):** Uses the upper boundary of the `ths` array (`ths[3]` = `"MMM"`) along with maxed-out lower places to yield `"MMMCMXCIX"`.
* **Numbers with Zeros in Middle/End (`num = 1000` or `num = 104`):** Zero digits evaluate to index `0` in their respective arrays, returning empty strings (`""`) without adding unwanted characters.
* **All Subtractive Combinations (4, 9, 40, 90, 400, 900):** Subtractive rule combinations are pre-written into the string lookup arrays, completely avoiding special branching logic or invalid consecutive repetitions.
