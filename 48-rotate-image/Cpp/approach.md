![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-10.2%20MB%20(beats%2036.54%25)-orange?style=for-the-badge)

---

## Problem Explained

Imagine you have a square grid of numbers, like a photograph or a digital image made of pixels arranged in rows and columns. This grid is an N by N matrix, meaning it has the exact same number of rows as it has columns (for example, 3 rows and 3 columns). 

The problem asks you to rotate this entire grid 90 degrees clockwise. If the top row of your image is currently pointing upwards, after the 90-degree rotation, that original top row should now be running down the right-hand side of the grid. 

There is one major catch: you must do this **in-place**. This means you are not allowed to create a second, separate grid to copy the rotated result into. You have to take the exact grid you were given and rearrange its numbers directly until it is fully rotated.

## Intuition

The "aha" moment for this problem comes from breaking a complex 90-degree rotation down into two much simpler geometric steps. 

Instead of trying to figure out how to move every single number to its final rotated position all at once, you can achieve the exact same result by doing two things:
1. **Transpose the matrix:** This means flipping the matrix across its main diagonal (the line running from the top-left corner down to the bottom-right corner). You do this by swapping every element at row `i`, column `j` with the element at row `j`, column `i`. 
2. **Reverse each row:** Once your matrix has been transposed, every row is currently backward. If you simply reverse the order of the numbers in each individual row from left to right, magic happens—the matrix is now perfectly rotated 90 degrees clockwise.

This two-step trick lets you avoid complex mathematical formulas for moving items around in a circle. You just do two very clean, standard passes over the grid.

## Approach

Here is how the code implements this two-step strategy, line by line:

- `for( int i=0 ; i<matrix.size() ; i++)`: This starts an outer loop that looks at each row of the matrix one by one, using `i` to track the current row index.
- `for( int j=i ; j<matrix[i].size() ; j++)`: This starts an inner loop across the columns, but notice it starts `j` at `i` instead of `0`. This is crucial because it ensures we only visit elements on or above the main diagonal, preventing us from swapping the same pair of numbers twice.
- `swap( matrix[i][j] , matrix[j][i] );`: This is the heart of the transposition step. It takes the number at row `i`, column `j` and swaps its contents with the number at row `j`, column `i`, effectively flipping the grid diagonally.
- `for( int i=0 ; i<matrix.size() ; i++)`: After the transposition loop finishes, this second outer loop visits each row of the matrix again from top to bottom.
- `reverse( matrix[i].begin() , matrix[i].end());`: For every single row, this built-in function flips the elements horizontally, reversing their order from left to right and completing our 90-degree clockwise rotation.

## Dry Run

### Case 1: Standard 3x3 Matrix
Input matrix: [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

| Step | i | j | Action | Current Matrix State |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 0 | 0 to 2 | Swap diagonal elements (none change with themselves), then transpose row 0 | [[1, 2, 3], [4, 5, 6], [7, 8, 9]] |
| 2 | 1 | 1 to 2 | Swap `matrix[1][2]` (6) with `matrix[2][1]` (8) | [[1, 2, 4], [3, 5, 8], [7, 6, 9]] |
| 3 | 2 | 2 | Swap `matrix[2][2]` with itself | [[1, 2, 4], [3, 5, 8], [7, 6, 9]] -> Transpose complete: [[1, 4, 7], [2, 5, 8], [3, 6, 9]] |
| 4 | 0 | - | Reverse row 0 | [[7, 4, 1], [2, 5, 8], [3, 6, 9]] |
| 5 | 1 | - | Reverse row 1 | [[7, 4, 1], [8, 5, 2], [3, 6, 9]] |
| 6 | 2 | - | Reverse row 2 | Final Output: [[7, 4, 1], [8, 5, 2], [9, 6, 3]] |

### Case 2: Smallest Possible 1x1 Matrix
Input matrix: [[5]]

| Step | i | j | Action | Current Matrix State |
| :--- | :--- | :--- | :--- | :--- |
| 1 | 0 | 0 | Transpose loop runs for a single element, swapping it with itself | [[5]] |
| 2 | 0 | - | Reverse row 0 (single element stays the same) | Final Output: [[5]] |

## Time & Space Complexity

- **Time:** O(N^2) — where N is the number of rows (or columns) in the matrix. The first loop visits roughly half of the grid elements to transpose them, and the second loop visits every element to reverse the rows. Both operations scale with the total number of elements, which is N * N.
- **Space:** O(1) — constant extra space. We are modifying the input matrix directly in place without allocating any new matrices or large data structures.

**Is this already the most optimal possible complexity for this problem, or can it be improved?**

Yes, this is already optimal. 

- **Why no further improvement is possible:** To rotate an N by N matrix by 90 degrees, you ultimately have to touch and relocate every single one of the N * N elements. Any algorithm must examine every piece of data at least once, meaning the time complexity cannot go below O(N^2). Similarly, modifying the matrix in-place achieves the absolute minimum space complexity of O(1).

## Edge Cases Handled

- **Minimum Size Matrix (N = 1):** The constraints state that N starts at 1. If a matrix has only 1 row and 1 column, the loops run once, swaps happen with the element itself, and the reversal does nothing, returning the correct 1x1 matrix unchanged.
- **Negative Numbers and Zeros:** The values inside the matrix can range from -1000 to 1000. Because the code uses generic swapping and reversing logic that does not care about the actual mathematical value of the numbers, negative numbers and zeros are moved just as accurately as positive numbers.
