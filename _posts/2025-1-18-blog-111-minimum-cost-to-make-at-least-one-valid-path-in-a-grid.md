---
layout: post  
title: "#110 💰 1368. Minimum Cost to Make at Least One Valid Path in a Grid 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Breadth-First Search, Graph, Heap (Priority Queue), Matrix, Shortest Path]
---

Imagine you're navigating a grid where each cell has a sign directing you to the next cell. Your mission? Make at least one valid path from the top-left corner to the bottom-right corner, but it might cost you! Each time you change a sign, you incur a cost of 1. Our goal is to minimize this cost to create a valid path. Let's dive into the problem, explore a solution, and dissect it step-by-step! 🤓

---

## 📜 Problem Statement  

You are given an `m x n` grid. Each cell in the grid has a direction sign indicating where to move next:  
1. **1** → Move **right**.  
2. **2** → Move **left**.  
3. **3** → Move **down**.  
4. **4** → Move **up**.  

You start at the top-left cell `(0, 0)` and aim to reach the bottom-right cell `(m - 1, n - 1)`. A valid path is one that follows the grid’s directions.  

### Rules:  
- You can change the direction of a cell's sign, but it costs 1 per change.  
- Your goal is to minimize the cost to make at least one valid path from the start to the destination.  

### Constraints:  
- `m == grid.length`  
- `n == grid[i].length`  
- `1 <= m, n <= 100`  
- `1 <= grid[i][j] <= 4`  

---

## 🔍 Examples  

### Example 1:  
#### Input:  
```python
grid = [
  [1, 1, 1, 1],
  [2, 2, 2, 2],
  [1, 1, 1, 1],
  [2, 2, 2, 2]
]
```  
#### Output:  
```
3
```  
#### Explanation:  
- Follow the path `(0, 0) → (0, 1) → (0, 2) → (0, 3)`.  
- Change the sign at `(0, 3)` to move down `(cost = 1)`.  
- Continue to `(1, 3)` → `(1, 2)` → `(1, 1)` → `(1, 0)`.  
- Change the sign at `(1, 0)` to move down `(cost = 1)`.  
- Finally, change the sign at `(2, 3)` to move down `(cost = 1)`.  
- Total cost: `3`.  

---

### Example 2:  
#### Input:  
```python
grid = [
  [1, 1, 3],
  [3, 2, 2],
  [1, 1, 4]
]
```  
#### Output:  
```
0
```  
#### Explanation:  
- The path `(0, 0) → (0, 1) → (0, 2) → (1, 2) → (2, 2)` is valid without any changes.  

---

### Example 3:  
#### Input:  
```python
grid = [
  [1, 2],
  [4, 3]
]
```  
#### Output:  
```
1
```  
#### Explanation:  
- Change the direction of `(0, 1)` to move down.  
- Follow `(0, 0) → (0, 1) → (1, 1)`.  

---

## 💡 Solution Overview  

This problem revolves around navigating a **grid 🗺️**, where each cell contains a **direction ➡️⬅️⬇️⬆️** pointing to where you should ideally go next. Think of it as a **map with arrows 🧭**, but the paths it suggests may not always lead to your destination 🏁. 

To ensure you can travel from the **starting cell 🟩** at the top-left corner `(0, 0)` to the **destination cell 🟥** at the bottom-right corner `(m-1, n-1)`, you might need to change the directions in some cells 🛠️. 

The twist? Changing a direction comes with a **cost 💰** of `1`, while following the arrow as is costs **nothing 🆓**. The task is to figure out the **minimum cost 💵** to create at least one valid path from start to finish.

---

### Solution Intuition 💡

This problem can be visualized as finding the **shortest path 🚗** in a weighted graph 🎢, where:

1. Each cell in the grid is a **node 🟨**.
2. Moving between nodes involves a **cost**, determined by whether the direction needs to be changed or not 🔄.
3. The objective is to minimize the total cost 💸 to navigate from the **top-left** to the **bottom-right corner**.

---

### Breaking Down the Approach 🛠️

To solve this problem efficiently, we need to think systematically about exploring the grid 🗺️ while minimizing costs:

#### 1. **Understanding Directions 🚦**
Each cell has a predefined direction encoded as:
- `1` → Move **Right ➡️**
- `2` → Move **Left ⬅️**
- `3` → Move **Down ⬇️**
- `4` → Move **Up ⬆️**

From any given cell, you can either follow its arrow 🏹 (incurring **no cost 🆓**) or modify the arrow 🔄 to move to a different neighboring cell (incurring a cost of `1 💰`).

#### 2. **Core Idea: Breadth-First Search (BFS) 🔍**
The shortest path in a grid-like structure can often be solved using **BFS**. In this scenario:
- We explore all neighboring cells of the current cell 📍.
- If a neighbor can be reached by following the current direction, we prioritize it (**cost = 0** 🟢).
- If a neighbor requires changing the direction, we explore it later (**cost = 1** 🔴).

This ensures that we visit all possible paths 🌟, starting with those that cost **less**.

#### 3. **Using a Priority Queue ⏳**
To manage the order in which cells are visited based on their cost:
- We use a **priority queue 🗂️** (think of it as a min-heap 🌐).
- Cells are added to the queue along with their **current cumulative cost**. The queue always prioritizes cells with the **smallest cost 🔑**.

For example:
- If a cell `(i, j)` has a cost of `2` 💸 and another cell `(x, y)` has a cost of `3`, `(i, j)` will be explored first 🚀.

#### 4. **Steps to Reach the Solution 🚶**
   - **Start** at the top-left corner `(0, 0)` with a **cost of `0` 🟢**.
   - At each step:
     - Check all four possible neighbors (up ⬆️, down ⬇️, left ⬅️, right ➡️).
     - If the direction from the current cell matches the neighbor’s position, **add it to the queue** with the same cost (no change needed ✅).
     - If the direction doesn’t match, **add the neighbor to the queue** with an increased cost of `1 🔄`.
   - Continue this process until you reach the **destination 🏁** `(m-1, n-1)`.
   - Keep track of visited cells 🛑 to avoid redundant work.

#### 5. **When to Stop 🛑**
The process stops as soon as you reach the **bottom-right corner 🟥**. At this point, the cost accumulated represents the **minimum cost 💰** required to make at least one valid path.

---

### Visualization of the Solution 🔎

Let’s walk through an example grid 🌟:

```
grid = [[1, 1, 3], 
        [3, 2, 2], 
        [1, 1, 4]]
```

1. Start at `(0, 0)` with a cost of `0` 🟢. The direction is `1` (**right ➡️**), so move to `(0, 1)` without any additional cost.
2. From `(0, 1)`, follow the direction `1` (**right ➡️**) to `(0, 2)`, still at cost `0` 🆓.
3. At `(0, 2)`, the direction is `3` (**down ⬇️**). This aligns with the arrow pointing down to `(1, 2)`, so no modification is needed ✅.
4. Finally, move from `(1, 2)` down ⬇️ to the destination `(2, 2)` at no extra cost 🎉.

This path ensures the **minimum cost 💰** to reach the destination.

---

### Why BFS Works Well Here 🤔

BFS is perfect for this scenario because it explores all possible paths in layers of **increasing cost**:
1. Paths with `cost = 0 🟢` are explored first.
2. If the destination isn’t reached, paths with `cost = 1 🔴` are considered next.
3. This ensures we find the **minimum cost 💵** efficiently without unnecessary exploration.

Using a **priority queue ⏳** enhances BFS by always prioritizing the least costly path, avoiding unnecessary exploration of higher-cost paths until absolutely needed 🚀.

---

### Key Concepts Recap 📝

1. **Graph Representation 🎢**: Each cell is a node 🟨, and moving to a neighbor has a weight of `0` 🟢 or `1` 🔴.
2. **Shortest Path 🛣️**: BFS with a priority queue ensures that paths are explored in the order of **increasing cost**.
3. **Optimization 🚀**: By following the natural direction whenever possible, we minimize the total modifications required 💰.


---

### 🧑‍💻 Python Implementation  

Here’s the complete code:  

```python
from collections import deque

class Solution:
    def minCost(self, grid: list[list[int]]) -> int:
        rows, cols = len(grid), len(grid[0])
        directions = [[0, 0], [0, 1], [0, -1], [1, 0], [-1, 0]]  # Right, Left, Down, Up
        queue = deque([(0, 0, 0)])  # (row, col, cost)
        visited = set()

        while queue:
            i, j, cost = queue.popleft()

            if (i, j) in visited:
                continue
            visited.add((i, j))

            if i == rows - 1 and j == cols - 1:
                return cost

            for k in range(1, 5):
                x, y = i + directions[k][0], j + directions[k][1]
                if 0 <= x < rows and 0 <= y < cols:
                    if grid[i][j] == k:
                        queue.appendleft((x, y, cost))
                    else:
                        queue.append((x, y, cost + 1))

        return -1
```

---

## 🔍 Complexity Analysis  

### Time Complexity:  
- Each cell is processed once, leading to `O(m * n)` operations.  
- Directional checks for each cell are constant.  
- **Overall**: `O(m * n)`.  

### Space Complexity:  
- Space is used for the queue and the visited set: `O(m * n)`.  

---

## 🛠 Example Walkthrough  

Let’s walk through **Example 1** in detail with larger numbers:  

#### Input:  
```python
grid = [
  [1, 1, 1, 1],
  [2, 2, 2, 2],
  [1, 1, 1, 1],
  [2, 2, 2, 2]
]
```  

1. Start at `(0, 0)`. The initial cost is `0`.  
2. Move right `(0, 0) → (0, 1) → (0, 2) → (0, 3)`.  
   - Change the sign at `(0, 3)` to move down. Cost: `1`.  
3. Move down `(0, 3) → (1, 3)`.  
   - Change the sign at `(1, 3)` to move left. Cost: `2`.  
4. Continue `(1, 3) → (1, 2) → (1, 1) → (1, 0)`.  
   - Change the sign at `(1, 0)` to move down. Cost: `3`.  
5. Move `(1, 0) → (2, 0) → (2, 1) → (2, 2) → (2, 3)`.  
6. Change the sign at `(2, 3)` to move down. Cost: `4`.  
7. Finally, reach `(3, 3)`.  

---

## 🧩 Edge Cases  

1. **Single-cell grid**:  
   ```python
   grid = [[1]]
   Output: 0
   ```

2. **Straight-line path**:  
   ```python
   grid = [[1, 1, 1, 1]]
   Output: 0
   ```

3. **Maximum grid size**:  
   Ensure efficiency with `100 x 100` grids.  

---

## 🎉 Conclusion  

This problem challenges us to think about optimization and graph traversal. Using BFS with a priority queue ensures minimal cost while exploring all possible paths. Whether you're navigating a grid or solving a real-world logistics problem, this approach showcases the power of systematic exploration.  

Let me know how I can improve this further! 😊
