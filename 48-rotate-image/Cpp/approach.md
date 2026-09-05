![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-10.2%20MB%20(beats%2036.52%25)-orange?style=for-the-badge)

---

## Problem Explained

You are given a square grid of numbers (an `n` by `n` 2D matrix) representing an image. Your goal is to turn the image 90 degrees to the right (clockwise).

The main rule is that you must do this **in-place**. That means you cannot create a new empty grid and copy numbers into it. You must rearrange the numbers directly inside the original grid you were given.

For example, if you start with this 3x3 grid:

```text
1  2  3
4  5  6
7  8  9
```

After a 90-degree clockwise rotation, it should look like this:

```text
7  4  1
8  5  2
9  6  3
```

Notice how the first row (`1, 2, 3`) becomes the last column (`1, 2, 3` going top to bottom).

---

## Intuition

Rotating a grid directly in a circle can be confusing because you have to move four numbers at once without overwriting them. 

There is a clever two-step trick that accomplishes a 90-degree clockwise rotation much more easily:

1. **Transpose the grid**: Flip the grid over its main top-left to bottom-right diagonal. This turns all rows into columns.
2. **Reverse each row**: Flip every row horizontally from left to right.

Let's trace this visually with a small example:

Start:
```text
1  2  3
4  5  6
7  8  9
```

Step 1 (Transpose - swap elements across diagonal):
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

By combining transpose and horizontal flip, you get a perfect 90-degree clockwise rotation!

---

## Approach

Here is how the code executes this two-step process:

* `for( int i=0 ; i<matrix.size() ; i++)`: Start a loop to go through each row index `i` from the top of the grid to the bottom.
* `for( int j=i ; j<matrix[i].size() ; j++)`: Start an inner loop for column index `j`. Notice `j` starts at `i` (not `0`). Starting at `i` keeps us on or above the diagonal so we only swap each pair once. If we started at `0`, we would swap items back to their original positions!
* `swap( matrix[i][j] , matrix[j][i] )`: Swap the element at row `i`, column `j` with the element at row `j`, column `i`. This completes the grid **transpose**.
* `for( int i=0 ; i<matrix.size() ; i++)`: Start a new loop to go through each row `i` again, now that the entire matrix is transposed.
* `reverse( matrix[i].begin() , matrix[i].end())`: Use C++'s standard built-in `reverse` function to flip row `matrix[i]` horizontally from left to right.

---

## Dry Run

### Case 1: 3x3 Matrix (`matrix = [[1,2,3],[4,5,6],[7,8,9]]`)

#### Step 1: Transposition Loop

| `i` | `j` | Action | Grid State After Step |
| :--- | :--- | :--- | :--- |
| - | - | Initial state | `[[1,2,3], [4,5,6], [7,8,9]]` |
| 0 | 0 | `swap(matrix[0][0], matrix[0][0])` (Diagonal, no change) | `[[1,2,3], [4,5,6], [7,8,9]]` |
| 0 | 1 | `swap(matrix[0][1], matrix[1][0])` (Swap 2 and 4) | `[[1,4,3], [2,5,6], [7,8,9]]` |
| 0 | 2 | `swap(matrix[0][2], matrix[2][0])` (Swap 3 and 7) | `[[1,4,7], [2,5,6], [3,8,9]]` |
| 1 | 1 | `swap(matrix[1][1], matrix[1][1])` (Diagonal, no change) | `[[1,4,7], [2,5,6], [3,8,9]]` |
| 1 | 2 | `swap(matrix[1][2], matrix[2][1])` (Swap 6 and 8) | `[[1,4,7], [2,5,8], [3,6,9]]` |
| 2 | 2 | `swap(matrix[2][2], matrix[2][2])` (Diagonal, no change) | `[[1,4,7], [2,5,8], [3,6,9]]` |

#### Step 2: Row Reversal Loop

| `i` | Action | Grid State After Step |
| :--- | :--- | :--- |
| 0 | `reverse(matrix[0])` | `[[7,4,1], [2,5,8], [3,6,9]]` |
| 1 | `reverse(matrix[1])` | `[[7,4,1], [8,5,2], [3,6,9]]` |
| 2 | `reverse(matrix[2])` | `[[7,4,1], [8,5,2], [9,6,3]]` |

---

### Case 2: 4x4 Matrix (`matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]`)

#### Step 1: Transposition Loop (Key Swaps)

| `i` | `j` | Action | Grid State Summary |
| :--- | :--- | :--- | :--- |
| - | - | Initial state | `[[5,1,9,11], [2,4,8,10], [13,3,6,7], [15,14,12,16]]` |
| 0 | 1..3 | Swap (0,1)<->(1,0), (0,2)<->(2,0), (0,3)<->(3,0) | Row 0 becomes column 0: `[5, 2, 13, 15]` |
| 1 | 2..3 | Swap (1,2)<->(2,1), (1,3)<->(3,1) | Row 1 becomes column 1: `[1, 4, 3, 14]` |
| 2 | 3 | Swap (2,3)<->(3,2) | Row 2 becomes column 2: `[9, 8, 6, 12]` |
| - | - | Transpose complete | `[[5,2,13,15], [1,4,3,14], [9,8,6,12], [11,10,7,16]]` |

#### Step 2: Row Reversal Loop

| `i` | Action | Grid Row Result |
| :--- | :--- | :--- |
| 0 | `reverse(matrix[0])` | `[15, 13, 2, 5]` |
| 1 | `reverse(matrix[1])` | `[14, 3, 4, 1]` |
| 2 | `reverse(matrix[2])` | `[12, 6, 8, 9]` |
| 3 | `reverse(matrix[3])` | `[16, 7, 10, 11]` |

---

## Time & Space Complexity

* **Time Complexity:** **O(n^2)** — where `n` is the number of rows (or columns) in the matrix.
  * Transposing the matrix takes `n * (n - 1) / 2` swap operations, which is O(n^2) time.
  * Reversing `n` rows of length `n` takes `n * (n / 2)` swap operations, which is also O(n^2) time.
  * Combining them gives O(n^2) total operations.

* **Space Complexity:** **O(1)** — auxiliary space.
  * All swaps and reversals are done directly inside the input vector `matrix`. No extra data structures are created.

### Is this solution optimal?

**Yes, this is already the most optimal possible complexity for this problem.**

* **Time:** An `n x n` matrix contains `n^2` elements in total. Because every single element must move to a new position during rotation, any algorithm must touch all `n^2` elements at least once. Therefore, O(n^2) is the theoretical best time complexity.
* **Space:** Since the problem explicitly requires modifying the matrix in-place, using O(1) auxiliary space is the theoretical best space complexity.

---

## Edge Cases Handled

* **Single Element Grid (`n = 1`)**:
  * Input: `[[1]]`
  * The outer loop runs once (`i = 0`), inner loop runs once (`j = 0`). It swaps `matrix[0][0]` with itself and reverses a 1-element row. It finishes safely without index errors.
* **Grid with Negative Numbers or Zeroes**:
  * The solution only modifies element positions based on indices. It never performs arithmetic on matrix values, so negative numbers, zeroes, or duplicate numbers do not affect correctness.
* **Even vs. Odd Matrix Dimensions**:
  * Works seamlessly for both even dimensions (e.g., 4x4) and odd dimensions (e.g., 3x3) because `j` starts at `i`, naturally adjusting the diagonal boundary for any matrix size.
