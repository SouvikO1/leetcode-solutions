![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-10.2%20MB%20(beats%2036.54%25)-orange?style=for-the-badge)

---

## Problem Explained

Imagine you have a square grid of numbers, like a picture made of pixels, where the number of rows equals the number of columns. We call this a square matrix or an N x N grid. 

The goal is to **rotate the entire grid 90 degrees clockwise**. 

For example, if the top row is [1, 2, 3], after a 90-degree clockwise rotation, that row should become the rightmost column, reading 1, then 2, then 3 from top to bottom. 

There is a major catch: you **must do this in-place**. This means you cannot create a second empty grid to copy the rotated numbers into. You have to rearrange the numbers inside the exact same grid given to you.

## Intuition

Rotating a grid directly in a single step without extra memory is tricky because items move across complex circular paths. 

The "aha" moment is that you can break a 90-degree clockwise rotation into **two very simple, standard grid operations**:

1. **Transpose the matrix:** Flip the grid diagonally. Swap the row and column coordinates for every cell. If you imagine a diagonal line running from the top-left corner to the bottom-right corner, every number on one side of that line swaps places with its mirror image on the other side.
2. **Reverse each row:** Take every row and flip it horizontally so the left elements go right and the right elements go left.

If you take any grid, transpose it, and then reverse each of its rows, it magically ends up rotated exactly 90 degrees clockwise. This completely avoids complex index math and extra memory.

## Approach

Here is how the code implements this two-step trick:

- `for( int i=0 ; i<matrix.size() ; i++)`: This outer loop walks down every row of the matrix one by one, using the variable `i` to keep track of the current row.
- `for( int j=i ; j<matrix[i].size() ; j++)`: This inner loop goes across the columns in row `i`. Notice that `j` starts at `i` instead of zero. This ensures we only visit elements on or above the main diagonal, preventing us from swapping the same pair of numbers twice.
- `swap( matrix[i][j] , matrix[j][i] );`: This swaps the element at row `i`, column `j` with the element at row `j`, column `i`. This is the **transpose** step that flips the matrix across its main diagonal.
- `for( int i=0 ; i<matrix.size() ; i++)`: After the grid is fully transposed, this loop iterates through every row again, using `i` as the row index.
- `reverse( matrix[i].begin() , matrix[i].end());`: This takes the entire row `i` and reverses the order of its elements from front to back. Doing this for every row completes the **horizontal flip**, finishing the 90-degree clockwise rotation.

## Dry Run

### Case 1: 3 x 3 Matrix (Problem Example)
Input: [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

| Step | i | j | Action / State of Matrix |
| :--- | :--- | :--- | :--- |
| Start | - | - | Matrix is [[1,2,3], [4,5,6], [7,8,9]] |
| Transpose i=0 | 0 | 0, 1, 2 | Row 0 stays [1,2,3]. Swaps (0,1) with (1,0), and (0,2) with (2,0). Matrix becomes [[1,4,7], [2,5,6], [3,8,9]] |
| Transpose i=1 | 1 | 1, 2 | Row 1 element at (1,1) stays 5. Swaps (1,2) with (2,1). Matrix becomes [[1,4,7], [2,5,8], [3,6,9]] |
| Transpose i=2 | 2 | 2 | Row 2 element at (2,2) stays 9. Matrix is now [[1,4,7], [2,5,8], [3,6,9]] |
| Reverse Row 0 | 0 | - | Reverses [1, 4, 7] to [7, 4, 1]. Matrix becomes [[7,4,1], [2,5,8], [3,6,9]] |
| Reverse Row 1 | 1 | - | Reverses [2, 5, 8] to [8, 5, 2]. Matrix becomes [[7,4,1], [8,5,2], [3,6,9]] |
| Reverse Row 2 | 2 | - | Reverses [3, 6, 9] to [9, 6, 3]. Final matrix: [[7,4,1], [8,5,2], [9,6,3]] |

### Case 2: 1 x 1 Matrix (Minimum Size Edge Case)
Input: [[5]]

| Step | i | j | Action / State of Matrix |
| :--- | :--- | :--- | :--- |
| Start | - | - | Matrix is [[5]] |
| Transpose | 0 | 0 | Loops run once. Swaps element (0,0) with itself. Matrix remains [[5]] |
| Reverse Row | 0 | - | Reverses a single-element row. Matrix remains [[5]] |
| End | - | - | Final output is [[5]] |

## Time & Space Complexity

- **Time:** O(N^2) — where N is the number of rows or columns in the matrix. The transpose step visits roughly half of the total N * N cells, and the reverse step visits all N rows. Both operations scale proportionally to the total number of elements in the grid.
- **Space:** O(1) — constant auxiliary space. The rotation is performed entirely in-place by swapping values within the existing matrix, using no extra data structures or memory arrays.

**Is this already the most optimal possible complexity, or can it be improved?**

This is already optimal. 

- **Time Complexity:** You must touch every single element in an N x N matrix at least once to move it to its new rotated position. Therefore, visiting all elements takes N * N time, making O(N^2) the theoretical time limit.
- **Space Complexity:** The problem strictly requires in-place modification, meaning you cannot allocate another 2D matrix. An O(1) auxiliary space footprint is the absolute best possible outcome.

## Edge Cases Handled

- **Minimum Size Matrix (N = 1):** The loops handle a 1 x 1 grid cleanly. The transpose loop runs once on the single element, and the reverse loop processes a row of length one without errors.
- **Negative Numbers and Zero:** The constraints allow values from -1000 to 1000. Because the code uses basic `swap` and `reverse` operations based entirely on grid coordinates rather than the values themselves, it handles negative numbers, zeros, and duplicates without issues.
- **Even and Odd Dimensions:** Whether N is even (like 2 or 4) or odd (like 3), the diagonal boundary logic (`j = i`) correctly skips already-swapped pairs during the transpose phase.
