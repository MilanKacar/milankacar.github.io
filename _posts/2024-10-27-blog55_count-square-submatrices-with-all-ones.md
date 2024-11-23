---
layout: post  
title: "#55 📐📏 1277. Count Square Submatrices with All Ones 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Dynamic Programming, Matrix]
---

Counting square submatrices with all ones in a binary matrix challenges our understanding of matrix traversal and dynamic programming! In this problem, we want to find how many square submatrices consist entirely of ones. We’ll explore a brute-force solution, understand its limitations, and then optimize it with dynamic programming. Ready to uncover all the squares? Let’s dive in! 🎉🔍

---

## Problem Statement 📜
Given an $$ m \times n $$ binary matrix, where each element is either a 1 or 0, count the number of square submatrices that contain only ones.

### Example

#### Example 1
**Input:**
```
matrix = [
  [0,1,1,1],
  [1,1,1,1],
  [0,1,1,1]
]
```

**Output:** `15`

**Explanation:**
- 10 squares of side 1.
- 4 squares of side 2.
- 1 square of side 3.

**Total**: `10 + 4 + 1 = 15` squares 🎉

#### Example 2
**Input:**
```
matrix = [
  [1,0,1],
  [1,1,0],
  [1,1,0]
]
```

**Output:** `7`

**Explanation:**
- 6 squares of side 1.
- 1 square of side 2.

**Total**: `6 + 1 = 7` squares 🎉

### Constraints
- $$ 1 \leq m, n \leq 300 $$
- $$ 0 \leq matrix[i][j] \leq 1 $$

---

## Basic Solution (Brute Force) 🚧

The most straightforward approach is to check every possible submatrix and see if it’s a square filled with ones. While this solution might work for small matrices, it can be inefficient for larger ones due to the number of checks involved.

### Steps 📝
1. **Loop** over all cells in the matrix as starting points for submatrices.
2. For each cell, **expand** the square size (1x1, 2x2, etc.) until you can no longer form a valid square of ones.
3. If a square is valid, **increment the count** of squares.

### Python Code 💻
Here’s the code to implement the brute-force solution:

```python
def countSquares(matrix):
    rows, cols = len(matrix), len(matrix[0])
    total_squares = 0

    # For each starting cell
    for i in range(rows):
        for j in range(cols):
            # Check for possible squares starting at (i, j)
            max_side = min(rows - i, cols - j)  # Limit side length to matrix bounds
            for side in range(1, max_side + 1):
                if is_square_of_ones(matrix, i, j, side):
                    total_squares += 1

    return total_squares

# Helper function to check if a square of given side length from (i, j) is all ones
def is_square_of_ones(matrix, i, j, side):
    for x in range(i, i + side):
        for y in range(j, j + side):
            if matrix[x][y] == 0:
                return False
    return True
```

### Time Complexity ⏳
For each cell in the matrix, we check all possible square sizes. This results in **$$O(n^3)$$** time complexity for a matrix of size $$ n \times n $$, making it infeasible for large matrices.

### Limitations 🛑
With $$ m, n \leq 300 $$, the brute-force approach can take too long due to repeated checks for ones, making it unsuitable for large matrices. Let’s explore an optimized solution! 🎯

---

## Optimized Solution (Dynamic Programming) 🚀

The dynamic programming (DP) approach reduces redundant calculations by utilizing smaller sub-problems to build up larger solutions. The idea is to use a `dp` matrix, where `dp[i][j]` stores the size of the largest square submatrix ending at cell `(i, j)`.

### Key Insight 💡
The cell `(i, j)` can form a square submatrix if and only if:
1. The cell above `(i-1, j)`,
2. The cell to the left `(i, j-1)`, and
3. The top-left diagonal cell `(i-1, j-1)`

are also part of squares. The size of the square ending at `(i, j)` is the minimum of these three neighbors plus one.

### DP Formula 📐
For each cell `(i, j)`, if `matrix[i][j] == 1`, then:
$$
dp[i][j] = \text{min}(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
$$

This builds larger squares using smaller squares already computed.

### Steps 📝
1. **Initialize** a `dp` matrix with the same dimensions as `matrix`.
2. Fill in `dp`, using the rule above, and **add up** all values in `dp` to get the total count of squares.
3. **Return** the sum as the total number of squares.

### Python Code 💻
Here’s the code for the DP approach:

```python
def countSquares(matrix):
    rows, cols = len(matrix), len(matrix[0])
    dp = [[0] * cols for _ in range(rows)]
    total_squares = 0

    for i in range(rows):
        for j in range(cols):
            if matrix[i][j] == 1:
                if i == 0 or j == 0:  # If on the border, size is 1
                    dp[i][j] = 1
                else:
                    dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
                total_squares += dp[i][j]

    return total_squares
```

### Example Walkthrough 🧐
Let’s go through the optimized solution using our example matrix:

```
matrix = [
  [0,1,1,1],
  [1,1,1,1],
  [0,1,1,1]
]
```

1. **Initialize `dp` matrix** (all zeroes initially):
   ```
   dp = [
     [0,0,0,0],
     [0,0,0,0],
     [0,0,0,0]
   ]
   ```

2. **First Row and First Column**: If the original cell is 1, set `dp[i][j] = 1`.
   ```
   dp = [
     [0,1,1,1],
     [1,0,0,0],
     [0,0,0,0]
   ]
   ```

3. **Remaining Cells**:
   - At `(1, 1)`, `matrix[1][1] == 1`, so `dp[1][1] = min(1, 1, 0) + 1 = 1`.
   - At `(1, 2)`, `matrix[1][2] == 1`, so `dp[1][2] = min(1, 1, 1) + 1 = 2`.
   - Continue this way for all cells...

The final `dp` matrix looks like:
```
dp = [
  [0,1,1,1],
  [1,1,2,2],
  [0,1,2,3]
]
```

### Total Squares 🧮
Summing up all values in `dp`, we get `1 + 1 + 1 + 1 + 1 + 2 + 2 + 1 + 2 + 3 = 15`.

### Time Complexity ⏳
This solution has a time complexity of **$$O(m \times n)$$**, making it efficient even for larger matrices. It’s significantly faster than the brute-force approach.

---

## Conclusion 🎉
In this blog post, we explored both the brute-force and optimized (dynamic programming) solutions to the problem of counting square submatrices with all ones in a binary matrix.

### Summary
- **Basic Solution**: Inefficient for large matrices due to repeated checks.
- **Optimized Solution**: Dynamic programming enables efficient counting by reusing previously computed values, resulting in an $$O(m \times n)$$ time complexity.

**Dynamic programming** provides a practical and powerful approach for handling matrix-based problems with overlapping subproblems. For this problem, DP enabled us to count squares effectively and efficiently, transforming an initially complex problem into a manageable one. 👏
