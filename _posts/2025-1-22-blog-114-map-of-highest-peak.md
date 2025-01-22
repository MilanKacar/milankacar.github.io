---
layout: post  
title: "#114 🗺️ 1765. Map of Highest Peak 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Breadth-First Search, Matrix]
---


Assigning heights to a matrix may sound straightforward, but this problem adds a fun twist with rules that require thought and logic! 🤔 Let's explore this engaging problem with examples, solutions, and walkthroughs! 💡

---

## Problem Statement 📜

We are given an integer matrix `isWater` of size `m x n` that represents a map where:

- `isWater[i][j] == 0`: Cell `(i, j)` is **land**.
- `isWater[i][j] == 1`: Cell `(i, j)` is **water**.

The goal is to assign **heights** to every cell in the matrix such that:

1. Heights are **non-negative**.
2. **Water cells** have a height of `0`.
3. The absolute difference between the heights of **adjacent cells** (north, south, east, or west) is at most `1`.

Our task is to maximize the **maximum height** in the matrix while following these rules.

Return a matrix `height` where `height[i][j]` represents the height of cell `(i, j)`.

---

## Examples 🖼️

### Example 1

#### Input:
```python
isWater = [[0,1],
           [0,0]]
```

#### Output:
```python
[[1,0],
 [2,1]]
```

#### Explanation:
- The water cell at `(0,1)` has a height of `0`.
- Land cells are assigned increasing heights moving away from water.

---

### Example 2

#### Input:
```python
isWater = [[0,0,1],
           [1,0,0],
           [0,0,0]]
```

#### Output:
```python
[[1,1,0],
 [0,1,1],
 [1,2,2]]
```

#### Explanation:
- Heights increase as we move away from water cells, while maintaining adjacency rules.

---

## Constraints ⚙️

- $$1 \leq m, n \leq 1000$$
- $$isWater[i][j] \in \{0, 1\}$$
- There is at least **one water cell**.

---

## Approach 💡

The heart of solving this problem lies in leveraging **Breadth-First Search (BFS)** effectively. To understand this better, let’s break down the problem into smaller components and explore the approach in detail.

---

### **Key Observations** 🔍

1. **Water Cells as the Starting Point**:  
   Since water cells have a height of `0`, they serve as the "roots" of our BFS. From these points, we propagate outward, incrementing the height as we move further away.

2. **Adjacent Rules**:  
   The problem imposes an adjacency rule that the height difference between two neighboring cells must not exceed `1`. BFS ensures that as we process cells level by level, the smallest valid height is assigned to each cell.

3. **Optimization Through BFS**:  
   BFS guarantees that the first time we encounter a cell, we are processing it with the smallest possible height. This avoids redundant checks and ensures correctness.

---

### **How BFS Works in This Context** 🛠️

The BFS here operates as a **multi-source BFS**:
- **Multi-Source**: All water cells (`isWater[i][j] == 1`) are treated as starting points.
- **Propagation**: Heights are propagated level by level, ensuring minimum height differences between adjacent cells.

---

### **Step-by-Step Approach** 🧭

#### **1. Initialization**
- Create a `height` matrix of the same size as `isWater` and initialize all cells to `-1`, indicating they are unvisited.
- Identify all water cells (`isWater[i][j] == 1`) and set their height to `0`. Add these cells to a queue as the starting points for BFS.

#### **2. BFS Traversal**
- Use a queue to store the cells to be processed, starting with the water cells.
- Define the four possible directions of movement (north, south, east, west) as a list of coordinate offsets: `[(-1, 0), (1, 0), (0, -1), (0, 1)]`.
- For each cell in the queue:
  1. Pop the cell `(x, y)` from the front of the queue.
  2. For each neighbor `(nx, ny)` (calculated using the direction offsets):
     - Check if `(nx, ny)` is within bounds and unvisited (`height[nx][ny] == -1`).
     - Assign the neighbor’s height as `height[x][y] + 1`.
     - Add `(nx, ny)` to the queue for further processing.

#### **3. Termination**
- The BFS completes when all cells have been visited and assigned heights. At this point, the `height` matrix contains the desired solution.

---

### **Why BFS?** 🤔

Using BFS ensures the following:
1. **Minimal Heights**: BFS explores all neighbors of a cell before moving further outward, ensuring that each cell receives the smallest valid height.
2. **Efficiency**: Each cell is visited only once, making BFS computationally efficient for this problem.
3. **Natural Propagation**: Heights naturally increase as BFS explores cells farther from the water sources.

---

### **Analysis of the Approach** 📊

#### **Time Complexity**
- **Initialization**: $$O(m \cdot n)$$ to create the `height` matrix and enqueue all water cells.
- **BFS Traversal**: Each cell is processed once, and all neighbors are checked, leading to a total complexity of $$O(m \cdot n)$$.
- **Overall**: **$$O(m \cdot n)$$**.

#### **Space Complexity**
- **Queue**: At most, all cells may be in the queue simultaneously, requiring $$O(m \cdot n)$$ space.
- **Height Matrix**: The `height` matrix also requires $$O(m \cdot n)$$.
- **Overall**: **$$O(m \cdot n)$$**.

---

### **Why Not Use DFS?** 🤔

DFS could theoretically be used for this problem, but it’s less suitable because:
1. DFS would require backtracking to ensure all valid neighbors are explored with the correct heights.
2. Managing height propagation correctly with DFS becomes more complex due to its depth-first nature.
3. BFS inherently aligns with the problem's requirements of spreading outward in levels, making it a more intuitive and efficient choice.

---

### **Edge Case Considerations** 🧩

1. **All Water Cells**:
   - If every cell is a water cell, the output will simply be a matrix of zeros. No BFS propagation is needed.

2. **Sparse Water Cells**:
   - When water cells are sparse and scattered, BFS ensures efficient propagation from multiple sources simultaneously.

3. **Large Matrices**:
   - For $$m, n = 1000$$, the solution handles the worst-case scenario efficiently due to its linear time complexity relative to the number of cells.

---

### **Real-World Analogy** 🌏

Imagine you’re standing on an island (water cell) in a flat landscape. You start building steps upward as you move away from the water, ensuring each step is only one unit higher than the previous one. The BFS ensures every island grows its steps outward uniformly until the entire landscape is covered. This process is similar to how heights are propagated in this problem.

---

### **Conclusion on the Approach** 🏁

The BFS-based approach for **Map of Highest Peak** is not just efficient but also intuitive. By treating water cells as starting points and spreading outward, we naturally satisfy all constraints while maximizing the height differences in the matrix. It's an elegant solution to a seemingly complex problem! 🌟

---

### Code Implementation 💻

Here’s the Python implementation:

```python
from collections import deque
from itertools import pairwise

class Solution:
    def highestPeak(self, isWater):
        m, n = len(isWater), len(isWater[0])
        height = [[-1] * n for _ in range(m)]
        queue = deque()

        # Initialize the queue with water cells
        for i in range(m):
            for j in range(n):
                if isWater[i][j] == 1:
                    height[i][j] = 0
                    queue.append((i, j))

        # BFS to calculate heights
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
        while queue:
            x, y = queue.popleft()
            for dx, dy in directions:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n and height[nx][ny] == -1:
                    height[nx][ny] = height[x][y] + 1
                    queue.append((nx, ny))
        
        return height
```

---

### Time Complexity ⏱️

- **Initialization**: $$O(m \cdot n)$$ for creating the matrix and adding water cells to the queue.
- **BFS Traversal**: Each cell is visited once, so $$O(m \cdot n)$$.
- Overall: **$$O(m \cdot n)$$**.

---

### Space Complexity 📦

- Queue stores up to $$m \cdot n$$ elements: $$O(m \cdot n)$$.
- Height matrix: $$O(m \cdot n)$$.
- Total: **$$O(m \cdot n)$$**.

---

## Example Walkthrough 🧭

Let’s walk through **Example 2** step-by-step for clarity:

#### Input:
```python
isWater = [[0,0,1],
           [1,0,0],
           [0,0,0]]
```

1. **Initialization**:
   - Mark all cells as `-1` in the `height` matrix.
   - Add all water cells to the queue:
     ```
     queue = [(0,2), (1,0)]
     height = [[-1, -1,  0],
               [ 0, -1, -1],
               [-1, -1, -1]]
     ```

2. **BFS Iteration 1**:
   - Process `(0,2)`:
     - Update `(0,1)` and `(1,2)` to `1`.
   - Process `(1,0)`:
     - Update `(0,0)` and `(1,1)` to `1`.

3. **BFS Iteration 2**:
   - Process newly updated cells, setting neighbors to `2` and so on.

4. **Final Output**:
   ```
   height = [[1, 1, 0],
             [0, 1, 1],
             [1, 2, 2]]
   ```

---

## Edge Cases 🧩

1. **All Water**:
   ```python
   isWater = [[1,1],
              [1,1]]
   Output: [[0,0],
            [0,0]]
   ```

2. **All Land except one Water**:
   ```python
   isWater = [[0,0,0],
              [0,1,0],
              [0,0,0]]
   Output: [[2,1,2],
            [1,0,1],
            [2,1,2]]
   ```

3. **Large Matrix**: Ensure it runs within the constraints for $$m, n = 1000$$.

---

## Conclusion 🏁

This problem teaches us to think in terms of **level-by-level traversal** using BFS. It’s an excellent example of applying graph algorithms to solve real-world-like matrix problems. 🌟

We hope this guide helped you understand how to tackle this fun problem! 🚀
