---
layout: post  
title: "#112 ⊹ 2017. Grid Game 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Matrix, Prefix Sum]
---

Have you ever wondered what happens when two optimal robots play a grid-based game to outsmart each other? The **Grid Game** is a perfect example of strategic thinking and optimization! 🧠 Let's dive deep into this exciting problem and learn how to solve it optimally. 🚀  

---

## 📝 Problem Statement  

We are given a 2D grid of size $$2 \times n$$, where each cell $$(r, c)$$ contains a certain number of points. Two robots start at the top-left corner of the grid $$(0, 0)$$ and aim to reach the bottom-right corner $$(1, n-1)$$, but here's the twist:  

1. The **first robot** moves first and sets all the cells on its path to 0.  
2. The **second robot** follows afterward and collects as many points as possible.  
3. Both robots can move either **right** or **down**, but never **left** or **up**.  
4. The **first robot's goal** is to minimize the points collected by the second robot, while the **second robot's goal** is to maximize its score.  

Your task is to return the **maximum number of points** the second robot can collect when both robots play optimally. 🤔  

---

## 🌟 Example  

### Example 1  
**Input:**  
```python
grid = [[2, 5, 4], [1, 5, 1]]
```  
**Output:**  
```python
4
```  
**Explanation:**  
1. The first robot takes the **top-right path**: `(0, 0) → (0, 1) → (0, 2) → (1, 2)`. It collects points: $$2 + 5 + 4 = 11$$. All these cells are set to 0.  
2. The second robot follows the **bottom-left path**: `(0, 0) → (1, 0) → (1, 1) → (1, 2)`. It collects points: $$0 + 0 + 5 + 1 = 4$$.  

### Example 2  
**Input:**  
```python
grid = [[3, 3, 1], [8, 5, 2]]
```  
**Output:**  
```python
4
```  
**Explanation:**  
1. The first robot follows a **top-right path**: `(0, 0) → (0, 1) → (0, 2) → (1, 2)`.  
2. The second robot collects $$4$$ points using the **bottom-left path**.  

---

## 🛠️ Solution  

To solve the problem, we need to carefully analyze the optimal strategies for both robots and ensure their paths and scoring align with the rules of the game. The objective is to **minimize the points collected by the second robot** while ensuring it collects the **maximum possible points along its path**.

Here’s how we approach the problem step-by-step:

---

### Step 1: Observing the Problem Dynamics
1. **Path Choices for the First Robot**:  
   The first robot moves first and selects an optimal path from `(0, 0)` to `(1, n-1)`. After traversing this path, all the cells it has visited are set to `0`, meaning they no longer contribute to the second robot's score.  
   
   This introduces the key challenge:  
   - The first robot must try to leave as little value as possible for the second robot.

2. **Path Choices for the Second Robot**:  
   The second robot then selects its path from `(0, 0)` to `(1, n-1)`, aiming to maximize the remaining score. Its path must be computed after the first robot’s moves are finalized.

---

### Step 2: Optimizing the Game Plan
The **game dynamics** rely on the strategy of minimizing the **maximum possible score** that the second robot can collect after the first robot makes its move. Here’s how this is done:

1. **Row Sum Representation**:
   - Compute the sum of all values in the **top row** (`sumRow0`) and the **bottom row** (`sumRow1`).
   - Use `sumRow0` to represent the points still available above the current position and `sumRow1` for points already collected below it.

2. **Iterative Path Simulation**:
   - As the first robot progresses through the grid, the value of `sumRow0` decreases (because the first robot visits and nullifies cells in the top row).
   - Meanwhile, `sumRow1` accumulates points in the bottom row.

3. **Balancing Two Paths**:
   - At each column `i`, calculate the **maximum points the second robot can collect** if it chooses to go down after column `i`:
     - Points remaining in the top row (`sumRow0`)
     - Points already accumulated in the bottom row (`sumRow1`)

   - Take the **minimum of the maximums** over all possible splits. This ensures the first robot chooses a split point that minimizes the second robot’s score.

---

### Step 3: Core Insight
The **game plan** boils down to this:  
- The first robot analyzes all possible points where it could descend to the bottom row and leaves the **minimum possible maximum points** for the second robot.  
This approach ensures the first robot acts optimally to minimize the advantage of the second robot.

---

### Optimized Algorithm: Explanation
Here’s a deeper dive into the optimized solution:

1. **Initialization**:
   - Calculate the total points in the top row (`sumRow0`) and bottom row (`sumRow1`).

2. **Iterate Through Each Column**:
   - For each column `i`, simulate the first robot's traversal:
     - Subtract the points of the current column from `sumRow0` as they are visited by the first robot.
     - Add the points of the current column to `sumRow1` to reflect the potential points for the second robot if it switches to the bottom row.

   - Compute the **maximum score** the second robot could collect if the first robot descends at column `i` (i.e., `max(sumRow0, sumRow1)`).

3. **Update the Minimum Maximum**:
   - Maintain the minimum of these maximum scores across all possible split points.

4. **Return the Result**:
   - The result is the **minimum maximum score** that the second robot can collect when the first robot plays optimally.

---

### Why Is This Optimal?
This strategy ensures that the first robot accounts for every possible move the second robot might make and minimizes the second robot’s scoring potential at each step. This guarantees the **minimum worst-case score** for the second robot.

---

#### 🕒 Time Complexity  
- The solution iterates over the columns of the grid exactly once, making the time complexity **O(n)**.  

#### 🛠️ Space Complexity  
- Since we use only a few variables, the space complexity is **O(1)**.  

---

### 🚀 Optimized Solution  

#### 🧑‍💻 Code Implementation  

```python
import math

class Solution:
    def gridGame(self, grid: list[list[int]]) -> int:
        n = len(grid[0])  # Number of columns
        ans = math.inf  # Minimum points the second robot can collect
        sumRow0 = sum(grid[0])  # Total points in the first row
        sumRow1 = 0  # Points collected in the second row so far

        for i in range(n):
            sumRow0 -= grid[0][i]  # Simulate first robot's path
            ans = min(ans, max(sumRow0, sumRow1))  # Minimize second robot's max path
            sumRow1 += grid[1][i]  # Accumulate points in the second row

        return ans
```  



---

## 🤔 Example Walkthrough  

Let's walk through the example with **higher numbers**:  

### Input:  
```python
grid = [[10, 15, 20, 25], [5, 10, 5, 10]]
```  

### Step-by-Step Execution  

1. **Initial State:**  
   $$
   \text{grid[0]} = [10, 15, 20, 25] \quad \text{grid[1]} = [5, 10, 5, 10]
   $$  
   $$\text{sumRow0} = 70, \text{sumRow1} = 0, \text{ans} = \infty$$.  

2. **First Iteration (i = 0):**  
   - **First robot skips $$10$$**: $$\text{sumRow0} = 60$$.  
   - **Second robot accumulates $$5$$**: $$\text{sumRow1} = 5$$.  
   - **Update ans**: $$ans = \min(\infty, \max(60, 5)) = 60$$.  

3. **Second Iteration (i = 1):**  
   - **First robot skips $$15$$**: $$\text{sumRow0} = 45$$.  
   - **Second robot accumulates $$10$$**: $$\text{sumRow1} = 15$$.  
   - **Update ans**: $$ans = \min(60, \max(45, 15)) = 45$$.  

4. **Third Iteration (i = 2):**  
   - **First robot skips $$20$$**: $$\text{sumRow0} = 25$$.  
   - **Second robot accumulates $$5$$**: $$\text{sumRow1} = 20$$.  
   - **Update ans**: $$ans = \min(45, \max(25, 20)) = 25$$.  

5. **Fourth Iteration (i = 3):**  
   - **First robot skips $$25$$**: $$\text{sumRow0} = 0$$.  
   - **Second robot accumulates $$10$$**: $$\text{sumRow1} = 30$$.  
   - **Update ans**: $$ans = \min(25, \max(0, 30)) = 25$$.  

**Final Output:**  
The second robot collects **25 points**.  

---

## 📋 Edge Cases  

1. **Single column grid:**  
   ```python
   grid = [[10], [20]]
   Output: 10
   ```  

2. **All cells have the same value:**  
   ```python
   grid = [[5, 5, 5], [5, 5, 5]]
   Output: 10
   ```  

3. **Large grid with random values:**  
   Ensure the algorithm efficiently handles $$n = 50,000$$.  

---

## 🔍 Conclusion  

This problem demonstrates the beauty of **greedy algorithms** in minimizing and maximizing strategies. 🤖 The optimized solution efficiently balances both robots' goals while ensuring a linear runtime. It's a perfect exercise to sharpen your **problem-solving skills** and understand strategic gameplay. 🚀  

**Key Takeaways:**  
- Think about **edge cases** and **optimal paths** for both players.  
- The simplicity of **O(n)** algorithms can outperform brute force approaches.  
- Always validate your solution with diverse test cases!  
