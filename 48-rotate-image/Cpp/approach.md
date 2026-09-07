![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-10.2%20MB%20(beats%2036.54%25)-orange?style=for-the-badge)

---

## Problem Explained

You are given an **n x n** square grid (a 2D matrix) filled with numbers. Your job is to turn the whole grid 90 degrees to the right (clockwise).

There is one important rule: you must do this **in-place**. This means you cannot create a brand-new grid to copy numbers into. You must swap elements directly inside the grid you were given, using minimal extra memory.

For example, if you start with this grid:

```text
1  2  3
4  5  6
7  8  9
```

After turning it 90 degrees clockwise, it becomes:

```text
7  4  1
8  5  2
9  6  3
```

Notice how the first column `[1, 4, 7]` turned sideways to become the top row `[7, 4, 1]`.

---

## Intuition

Rotating a matrix directly cell-by-cell can feel confusing because four numbers need to rotate in a circle simultaneously. 

Instead, there is a simple two-step trick that produces the exact same result:

1. **Transpose the matrix**: Swap elements across the main diagonal (from top-left to bottom-right). This flips rows into columns.
2. **Reverse each row**: Flip every row left-to-right.

Let's watch what happens to our example grid:

Start state:
```text
1  2  3
4  5  6
7  8  9
```

Step 1 (Transpose): Swap element `(row, col)` with `(col, row)`.
```text
1  4  7
2  5  8
3  6  9
```

Step 2 (Reverse each row):
```text
7  4  1
8  5  2
9  6  3
```

By breaking one complex rotation into two standard matrix operations, the code stays clean and easy to write.

---

## Approach

Here is how the C++ code performs this two-step process:

* `for( int i=0 ; i<matrix.size() ; i++)` — Outer loop that steps through each row index `i` from `0` to `n - 1`.
* `for( int j=i ; j<matrix[i].size() ; j++)` — Inner loop that steps through column index `j`. Notice `j` starts at `i` (not `0`). Starting at `i` ensures we only visit elements on or above the diagonal. If we started at `0`, we would swap elements twice and end up right back where we started.
* `swap( matrix[i][j] , matrix[j][i] )` — Swaps the number at row `i`, column `j` with the number at row `j`, column `i`. This completes the transpose step.
* `for( int i=0 ; i<matrix.size() ; i++)` — Starts a new loop through each row `i` to begin the second step.
* `reverse( matrix[i].begin() , matrix[i].end())` — Reverses all numbers inside the row `matrix[i]` in-place. This flips the row left-to-right and completes the 90-degree rotation.

---

## Dry Run

### Case 1: Standard 3x3 Matrix

Input: `matrix = [[1,2,3],[4,5,6],[7,8,9]]`

| Step | `i` | `j` | Action | Current `matrix` |
| --- | --- | --- | --- | --- |
| Start | - | - | Matrix loaded | `[[1,2,3],[4,5,6],[7,8,9]]` |
| Transpose | 0 | 1 | Swap `matrix[0][1]` (2) & `matrix[1][0]` (4) | `[[1,4,3],[2,5,6],[7,8,9]]` |
| Transpose | 0 | 2 | Swap `matrix[0][2]` (3) & `matrix[2][0]` (7) | `[[1,4,7],[2,5,6],[3,8,9]]` |
| Transpose | 1 | 2 | Swap `matrix[1][2]` (6) & `matrix[2][1]` (8) | `[[1,4,7],[2,5,8],[3,6,9]]` |
| Reverse | 0 | - | Reverse row 0 | `[[7,4,1],[2,5,8],[3,6,9]]` |
| Reverse | 1 | - | Reverse row 1 | `[[7,4,1],[8,5,2],[3,6,9]]` |
| Reverse | 2 | - | Reverse row 2 | `[[7,4,1],[8,5,2],[9,6,3]]` |

---

### Case 2: Even-Sized 4x4 Matrix

Input: `matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]`

| Step | `i` | `j` | Action | Current `matrix` |
| --- | --- | --- | --- | --- |
| Start | - | - | Matrix loaded | `[[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]` |
| Transpose | All | All | Transpose completed across main diagonal | `[[5,2,13,15],[1,4,3,14],[9,8,6,12],[11,10,7,16]]` |
| Reverse | 0 | - | Reverse row 0 | `[[15,13,2,5],[1,4,3,14],[9,8,6,12],[11,10,7,16]]` |
| Reverse | 1 | - | Reverse row 1 | `[[15,13,2,5],[14,3,4,1],[9,8,6,12],[11,10,7,16]]` |
| Reverse | 2 | - | Reverse row 2 | `[[15,13,2,5],[14,3,4,1],[12,6,8,9],[11,10,7,16]]` |
| Reverse | 3 | - | Reverse row 3 | `[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]` |

---

## Time & Space Complexity

**Time:** O(n^2) — where n is the number of rows (or columns) in the grid. The total number of elements in the grid is n * n. The transpose pass visits roughly half of these elements, and the reversal pass visits all elements once. This leads to a total time proportional to n^2.

**Space:** O(1) — all changes are made directly inside the input matrix using build-in swap and reverse functions. No extra arrays or data structures are allocated.

### Is this optimal?

**Yes, this solution is already optimal.**

* **Time:** Any valid algorithm must visit every cell at least once to move its value to the new location. Since there are n * n cells, the absolute theoretical minimum time is O(n^2).
* **Space:** The problem explicitly requires modifying the matrix in-place. O(1) extra space is the theoretical best possible memory usage.

---

## Edge Cases Handled

* **Single Element Matrix (`n = 1`):** For input like `[[1]]`, `i` and `j` stay at `0`. Swapping `matrix[0][0]` with itself does nothing, and reversing a single-element row does nothing. The output remains `[[1]]`, which is correct.
* **Negative Numbers and Zeros:** The values inside the grid do not affect the logic. Elements are moved purely based on their grid positions `(i, j)`.
* **Duplicate Values:** Equal numbers are safely swapped without causing infinite loops or incorrect state tracking.
* **Even vs. Odd Grid Sizes:** Starting the inner column loop at `j = i` naturally works for both odd grid dimensions (like 3x3) and even grid dimensions (like 4x4) without requiring special logic for center elements.
