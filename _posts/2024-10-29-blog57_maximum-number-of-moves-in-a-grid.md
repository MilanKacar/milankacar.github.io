---
layout: post  
title: "#57 2684. Maximum Number of Moves in a Grid 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Dynamic Programming, Matrix]
---

In this problem, we’re dealing with a matrix of numbers, representing a grid where each number is a cell. Our task is to start from any cell in the first column and traverse rightwards across the grid, only moving to cells with a higher value than our current cell. The goal? To find the maximum number of moves we can make in such a path! Let’s explore how to achieve this optimally using **Dynamic Programming (DP)**. 🤩

---

#### 📜 **Problem Statement Recap**
We are given a **0-indexed** \( m \times n \) matrix `grid` where:
1. We can start at any cell in the **first column**.
2. Our movement options from each cell `(row, col)` are:
   - Moving to the **right diagonally up** `(row - 1, col + 1)`
   - Moving **right horizontally** `(row, col + 1)`
   - Moving to the **right diagonally down** `(row + 1, col + 1)`
3. The constraint is that we can only move to a cell if its value is **strictly greater** than our current cell’s value.

**Objective**: Return the maximum number of moves we can perform starting from any cell in the first column, following the given movement constraints.

---

### 🚀 **Solution Overview: Why Dynamic Programming Works Here**

Given the nature of the problem, **Dynamic Programming (DP)** is a natural choice:
1. **Overlapping Subproblems**: As we traverse the grid, certain paths intersect, leading to redundant calculations if not optimized.
2. **Optimal Substructure**: Each cell’s maximum moves depends on its neighboring cells in the next column. This property makes DP a great fit, as we can store the maximum moves for each cell and use them to build up the solution from right to left.

The main insight is to avoid recalculating moves by using a **memoization** technique with a `dp` table, where `dp[i][j]` stores the maximum moves possible from cell `(i, j)`.

---

### 🧠 **Detailed Solution Strategy**

Here’s the step-by-step approach to solving the problem:

1. **Initialize a DP Table**:
   - Create a 2D array `dp` of the same size as `grid`. Initially, set all values to `0`, since starting from any cell without a valid move would yield `0` moves.

2. **Process from Right to Left**:
   - We start filling the DP table from the second-to-last column (`n - 2`) and work our way to the first column. This allows us to calculate the maximum moves from each cell based on cells to its right, building our solution incrementally.
   
3. **Three Possible Moves**:
   - For each cell `(i, j)`, check:
     - The **right diagonal-up** cell `(i - 1, j + 1)`
     - The **right** cell `(i, j + 1)`
     - The **right diagonal-down** cell `(i + 1, j + 1)`
   - Each time we find a cell with a **higher value** than `(i, j)`, we update `dp[i][j]` as `dp[i][j] = max(dp[i][j], dp[new_i][j + 1] + 1)`.

4. **Result Calculation**:
   - After filling the DP table, the result is the **maximum value in the first column of `dp`**. This value represents the maximum possible moves starting from any cell in the first column.

---

### 🐍 **Python Code for the Optimized DP Solution**

Here’s the code for our optimized DP approach, capturing all the logic discussed:

```python
def maxMoves(grid):
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]  # Initialize DP table with all zeros

    # Traverse the DP table from right to left
    for j in range(n - 2, -1, -1):  # Start from second last column
        for i in range(m):
            # Explore the three possible moves
            for new_i in [i - 1, i, i + 1]:
                if 0 <= new_i < m and grid[new_i][j + 1] > grid[i][j]:
                    dp[i][j] = max(dp[i][j], dp[new_i][j + 1] + 1)

    # The answer is the maximum value in the first column of the DP table
    return max(dp[i][0] for i in range(m))
```

This code leverages the three movement options to calculate `dp[i][j]`, representing the maximum moves from each cell, ensuring an efficient solution for large grids.

---

### 🔍 **Example Walkthrough**

Let’s walk through Example 1 in detail using the DP approach to fill our table and understand the logic:

For `grid = [[2, 4, 3, 5], [5, 4, 9, 3], [3, 4, 2, 11], [10, 9, 13, 15]]`:

1. **Step 1**: Initialize a DP table with dimensions \(4 \times 4\) (matching the grid dimensions), and set all values to `0`.
   
   ```plaintext
   dp = [[0, 0, 0, 0],
         [0, 0, 0, 0],
         [0, 0, 0, 0],
         [0, 0, 0, 0]]
   ```

2. **Step 2**: Start from the second-last column (column `2`) and fill the table from right to left:
   
   - **Column `2`**:
     - At `(0, 2)`, value is `3`. Possible moves:
       - `(1, 3)` with value `11` (valid, update `dp[0][2] = dp[1][3] + 1 = 1`)
     - At `(1, 2)`, value is `9`. Possible moves:
       - `(2, 3)` with value `11` (valid, update `dp[1][2] = dp[2][3] + 1 = 1`)
     - At `(3, 2)`, value is `13`. Possible moves:
       - `(3, 3)` with value `15` (valid, update `dp[3][2] = dp[3][3] + 1 = 1`)
   
   Updated DP table after processing column `2`:

   ```plaintext
   dp = [[0, 0, 1, 0],
         [0, 0, 1, 0],
         [0, 0, 0, 0],
         [0, 0, 1, 0]]
   ```

   We continue this approach for remaining columns, filling `dp` values based on possible moves.

3. **Result Calculation**: Finally, the maximum value in the first column (`max(dp[i][0] for i in range(m))`) gives us `3`, representing the maximum number of moves we can make.

---

### ⚠️ **Edge Cases to Consider**
1. **Single Row or Single Column Grid**: If `m` or `n` is `1`, movements are limited. Handle separately if necessary.
2. **No Valid Moves**: If all cells have equal values or there are no cells with higher values to move to.
3. **Large Grid with Uniform Values**: May appear deceptively simple but should confirm that DP avoids redundant computations.

---

### 🔚 **Conclusion**

In summary, this problem illustrates the power of Dynamic Programming for grid traversal, especially when constraints make exhaustive search infeasible. By building the solution backward, our DP approach ensures we can maximize moves efficiently even for large grids. This problem is a great example of optimizing recursive solutions and shows how DP tables reduce complexity from exponential to polynomial time. 🚀
