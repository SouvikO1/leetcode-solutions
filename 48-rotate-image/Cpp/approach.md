![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-10.2%20MB%20(beats%2036.48%25)-orange?style=for-the-badge)

---

## Problem Explained

You are given an `n x n` square grid of numbers representing an image. Your goal is to rotate the image **90 degrees clockwise** (to the right). 

The key constraint is that you must do this **in-place**. This means you cannot create a second matrix to build the answer. You must rearrange the elements directly inside the original matrix without using extra matrix storage.

For example, given this 3x3 matrix:
```text
1  2  3
4  5  6
7  8  9
```

After a 90-degree clockwise rotation, it should become:
```text
7  4  1
8  5  2
9  6  3
```

## Intuition

Rotating a matrix directly cell-by-cell can involve tricky math to keep track of four moving corners at once. 

A cleaner, two-step trick simplifies this completely:
1. **Transpose the matrix**: Swap elements across the main diagonal (top-left to bottom-right). This turns every row into a column.
2. **Reverse each row horizontally**: Flip each row from left to right.

Combining these two simple transformations yields the exact same result as rotating the entire grid 90 degrees clockwise.

## Approach

Here is how the code executes this two-step process:

* `for( int i=0 ; i<matrix.size() ; i++)`: Loops through each row index `i` from the top of the matrix to the bottom.
* `for( int j=i ; j<matrix[i].size() ; j++)`: Loops through column index `j`. Starting `j` at `i` ensures we only process elements on and above the main diagonal, preventing double-swapping back to the original layout.
* `swap( matrix[i][j] , matrix[j][i] )`: Transposes the matrix by swapping the element at row `i`, column `j` with the element at row `j`, column `i`.
* `for( int i=0 ; i<matrix.size() ; i++)`: Loops through every row index `i` a second time after the full transpose step finishes.
* `reverse( matrix[i].begin() , matrix[i].end())`: Reverses the elements in row `i` from left to right, completing the clockwise rotation.

## Dry Run

### Case 1: Standard 3x3 Matrix
Input: `matrix = [[1,2,3],[4,5,6],[7,8,9]]`

**Step 1: Transpose phase**

| Step / Phase | `i` | `j` | Action | Matrix State |
| :--- | :--- | :--- | :--- | :--- |
| Start | - | - | Initial input | `[[1,2,3],[4,5,6],[7,8,9]]` |
| Transpose | 0 | 0 | Swap `matrix[0][0]` (1) with `matrix[0][0]` (1) | `[[1,2,3],[4,5,6],[7,8,9]]` |
| Transpose | 0 | 1 | Swap `matrix[0][1]` (2) with `matrix[1][0]` (4) | `[[1,4,3],[2,5,6],[7,8,9]]` |
| Transpose | 0 | 2 | Swap `matrix[0][2]` (3) with `matrix[2][0]` (7) | `[[1,4,7],[2,5,6],[3,8,9]]` |
| Transpose | 1 | 1 | Swap `matrix[1][1]` (5) with `matrix[1][1]` (5) | `[[1,4,7],[2,5,6],[3,8,9]]` |
| Transpose | 1 | 2 | Swap `matrix[1][2]` (6) with `matrix[2][1]` (8) | `[[1,4,7],[2,5,8],[3,6,9]]` |
| Transpose | 2 | 2 | Swap `matrix[2][2]` (9) with `matrix[2][2]` (9) | `[[1,4,7],[2,5,8],[3,6,9]]` |

**Step 2: Row reverse phase**

| Step / Phase | `i` | Action | Matrix State |
| :--- | :--- | :--- | :--- |
| Reverse | 0 | Reverse row 0: `[1, 4, 7]` becomes `[7, 4, 1]` | `[[7,4,1],[2,5,8],[3,6,9]]` |
| Reverse | 1 | Reverse row 1: `[2, 5, 8]` becomes `[8, 5, 2]` | `[[7,4,1],[8,5,2],[3,6,9]]` |
| Reverse | 2 | Reverse row 2: `[3, 6, 9]` becomes `[9, 6, 3]` | `[[7,4,1],[8,5,2],[9,6,3]]` |

---

### Case 2: 4x4 Matrix
Input: `matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]`

| Step / Phase | `i` | Action | Matrix State |
| :--- | :--- | :--- | :--- |
| Start | - | Initial input | `[[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]` |
| Transpose | - | Swap all `(i, j)` with `(j, i)` where `j >= i` | `[[5,2,13,15],[1,4,3,14],[9,8,6,12],[11,10,7,16]]` |
| Reverse Row 0 | 0 | Reverse row 0 | `[[15,13,2,5],[1,4,3,14],[9,8,6,12],[11,10,7,16]]` |
| Reverse Row 1 | 1 | Reverse row 1 | `[[15,13,2,5],[14,3,4,1],[9,8,6,12],[11,10,7,16]]` |
| Reverse Row 2 | 2 | Reverse row 2 | `[[15,13,2,5],[14,3,4,1],[12,6,8,9],[11,10,7,16]]` |
| Reverse Row 3 | 3 | Reverse row 3 | `[[15,13,2,5],[14,3,4,1],[12,6,8,9],[16,7,10,11]]` |

## Time & Space Complexity

* **Time:** **O(n^2)** — where `n` is the width and height of the matrix. The grid has `n * n` total cells. Transposing touches half the elements, and reversing touches all rows. Both steps scale directly with the total number of cells, `n^2`.
* **Space:** **O(1)** — no extra vectors or matrices are created. All standard memory swaps happen directly inside the original structure.

This code is **already optimal**. Every number in an `n x n` matrix must be moved to a new position, so any valid solution must look at all `n * n` elements at least once. Therefore, **O(n^2)** time is the best possible runtime, and **O(1)** extra memory is the best possible space complexity for an in-place algorithm.

## Edge Cases Handled

* **1x1 Matrix (`n = 1`)**: When `matrix` has a single cell, `i = 0` and `j = 0`. Swapping an element with itself leaves it untouched, and reversing a 1-element row does nothing. The output correctly stays identical.
* **Negative numbers and zeroes**: The swap and reverse routines operate purely on cell positions rather than math values, so negative or zero values are safely relocated without issues.
* **Small matrix limits (`1 <= n <= 20`)**: The code uses basic array indices within bound loops, staying safely within constraint limits.
