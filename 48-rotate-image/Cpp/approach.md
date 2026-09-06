![Runtime](https://img.shields.io/badge/Runtime-0%20ms%20(beats%20100.00%25)-brightgreen?style=for-the-badge)
![Memory](https://img.shields.io/badge/Memory-10.2%20MB%20(beats%2036.48%25)-orange?style=for-the-badge)

---

## Problem Explained

You are given a square grid of numbers called `matrix` with size `n x n`. Your goal is to rotate the entire grid **90 degrees clockwise**.

Rotating 90 degrees clockwise means:
* The top row becomes the rightmost column.
* The middle row becomes the middle column.
* The bottom row becomes the leftmost column.

There is one key catch: you must do this **in-place**. That means you cannot create a new grid to hold the answers. You must rearrange the numbers directly inside the given `matrix`.

For example, given this 3 x 3 grid:

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

---

## Intuition

Rotating a grid directly in circles can be tricky to keep track of. Fortunately, there is a neat two-step linear algebra trick that gives the exact same result:

1. **Transpose the matrix**: Swap values across the main diagonal (from top-left to bottom-right). This turns all rows into columns.
2. **Reverse each row**: Flip each row from left to right.

Let us trace this on a simple grid:

```text
Original:
1  2  3
4  5  6
7  8  9

Step 1: Transpose (swap matrix[i][j] with matrix[j][i])
1  4  7
2  5  8
3  6  9

Step 2: Reverse each row
7  4  1
8  5  2
9  6  3
```

This two-step process gives the exact 90-degree clockwise rotation!

---

## Approach

Here is how the code executes this two-step process:

* `for( int i=0 ; i<matrix.size() ; i++)`: Loops through every row index `i` from the top to the bottom of `matrix`.
* `for( int j=i ; j<matrix[i].size() ; j++)`: Loops through column indices `j` starting at `i`. Starting `j` at `i` processes only the upper triangle of the matrix, which prevents double-swapping elements back to their original positions.
* `swap( matrix[i][j] , matrix[j][i] )`: Swaps the element at row `i`, column `j` with the element at row `j`, column `i`. This completes **Step 1 (Transpose)**.
* `for( int i=0 ; i<matrix.size() ; i++)`: Loops through each row index `i` again to perform the second step.
* `reverse( matrix[i].begin() , matrix[i].end())`: Flips the elements of row `matrix[i]` left-to-right in-place. This completes **Step 2 (Reverse)**.

---

## Dry Run

### Case 1: 3 x 3 Grid

Input: `matrix = [[1,2,3],[4,5,6],[7,8,9]]`

#### Step 1: Transpose

| `i` | `j` | Action | `matrix` state after action |
| :--- | :--- | :--- | :--- |
| 0 | 0 | Swap `matrix[0][0]` and `matrix[0][0]` | `[[1,2,3],[4,5,6],[7,8,9]]` |
| 0 | 1 | Swap `matrix[0][1]` (2) and `matrix[1][0]` (4) | `[[1,4,3],[2,5,6],[7,8,9]]` |
| 0 | 2 | Swap `matrix[0][2]` (3) and `matrix[2][0]` (7) | `[[1,4,7],[2,5,6],[3,8,9]]` |
| 1 | 1 | Swap `matrix[1][1]` and `matrix[1][1]` | `[[1,4,7],[2,5,6],[3,8,9]]` |
| 1 | 2 | Swap `matrix[1][2]` (6) and `matrix[2][1]` (8) | `[[1,4,7],[2,5,8],[3,6,9]]` |
| 2 | 2 | Swap `matrix[2][2]` and `matrix[2][2]` | `[[1,4,7],[2,5,8],[3,6,9]]` |

#### Step 2: Reverse Rows

| `i` | Action | `matrix` state after action |
| :--- | :--- | :--- |
| 0 | Reverse row 0: `[1,4,7]` -> `[7,4,1]` | `[[7,4,1],[2,5,8],[3,6,9]]` |
| 1 | Reverse row 1: `[2,5,8]` -> `[8,5,2]` | `[[7,4,1],[8,5,2],[3,6,9]]` |
| 2 | Reverse row 2: `[3,6,9]` -> `[9,6,3]` | `[[7,4,1],[8,5,2],[9,6,3]]` |

---

### Case 2: 4 x 4 Grid

Input: `matrix = [[5,1,9,11],[2,4,8,10],[13,3,6,7],[15,14,12,16]]`

#### Step 1: Transpose (Key Swaps)

| `i` | `j` | Action | Resulting `matrix` |
| :--- | :--- | :--- | :--- |
| 0 | 1 | Swap `matrix[0][1]` (1) & `matrix[1][0]` (2) | `[[5,2,9,11],[1,4,8,10],...]` |
| 0 | 2 | Swap `matrix[0][2]` (9) & `matrix[2][0]` (13) | `[[5,2,13,11],[1,4,8,10],[9,3,6,7],...]` |
| 0 | 3 | Swap `matrix[0][3]` (11) & `matrix[3][0]` (15) | `[[5,2,13,15],[1,4,8,10],[9,3,6,7],[11,14,12,16]]` |
| 1 | 2 | Swap `matrix[1][2]` (8) & `matrix[2][1]` (3) | `[[5,2,13,15],[1,4,3,10],[9,8,6,7],[11,14,12,16]]` |
| 1 | 3 | Swap `matrix[1][3]` (10) & `matrix[3][1]` (14) | `[[5,2,13,15],[1,4,3,14],[9,8,6,7],[11,10,12,16]]` |
| 2 | 3 | Swap `matrix[2][3]` (7) & `matrix[3][2]` (12) | Transpose completed! |

Transposed matrix state:
```text
[[ 5,  2, 13, 15],
 [ 1,  4,  3, 14],
 [ 9,  8,  6, 12],
 [11, 10,  7, 16]]
```

#### Step 2: Reverse Rows

| `i` | Action | Resulting `matrix` row |
| :--- | :--- | :--- |
| 0 | Reverse `[5, 2, 13, 15]` | `[15, 13, 2, 5]` |
| 1 | Reverse `[1, 4, 3, 14]` | `[14, 3, 4, 1]` |
| 2 | Reverse `[9, 8, 6, 12]` | `[12, 6, 8, 9]` |
| 3 | Reverse `[11, 10, 7, 16]` | `[16, 7, 10, 11]` |

---

## Time & Space Complexity

* **Time Complexity:** **O(n^2)** — Where `n` is the number of rows (or columns). The grid contains `n * n` total elements. Transposing reads half the elements, and reversing touches all `n * n` elements once. Total operations scale directly with `n^2`.
* **Space Complexity:** **O(1)** — We swap values in-place inside the existing `matrix`. No extra grids or helper arrays are allocated.

### Is this optimal?

**Yes, this solution is already optimal.**
* **Time:** You must visit every single element at least once to move it to its new location. Since there are `n * n` elements, any rotation algorithm takes at least **O(n^2)** time.
* **Space:** The problem strictly requires an in-place solution, which means **O(1)** extra space is the absolute best possible memory limit.

---

## Edge Cases Handled

* **Single Element Grid (`1 x 1`):** The matrix size is `n = 1`. Loops execute once, swapping `matrix[0][0]` with itself and reversing a 1-element vector. The function handles this correctly without index errors.
* **Negative Numbers and Zeroes:** Grid values do not affect index calculations or memory layout. Swaps move values regardless of sign or magnitude.
* **Even vs. Odd Matrix Dimensions:** Setting `j = i` in the inner loop ensures exact traversal across the diagonal for both odd grid sizes (like 3 x 3) and even grid sizes (like 4 x 4).
