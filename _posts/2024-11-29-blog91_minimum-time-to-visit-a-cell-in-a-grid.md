---
layout: post  
title: "#91 🗺️ 2577. Minimum Time to Visit a Cell In a Grid 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Breadth-First Search, Graph, Heap (Priority Queue), Matrix, Shortest Path]
---
  
Imagine you are navigating a grid 🚦. Each cell in this grid has a "minimum unlock time," meaning you can only visit it after waiting a certain amount of time⏳. Starting from the top-left cell, your goal is to **reach the bottom-right cell** as quickly as possible while adhering to the timing rules. If no valid path exists, return `-1`.

---

### 📝 Problem Statement  

You are given an **m x n grid** where:  
- `grid[row][col]` represents the **minimum time** required to visit cell `(row, col)`.  
- You start at the **top-left corner** `(0, 0)` at `t = 0`.  
- You can move **up, down, left, or right**, and each move takes **1 second**.  

Your task is to return the **minimum time required** to reach the bottom-right corner of the grid, or `-1` if it's impossible.  

#### Constraints  
1. `m == grid.length`  
2. `n == grid[i].length`  
3. `2 <= m, n <= 1000`  
4. `4 <= m * n <= 10⁵`  
5. `0 <= grid[i][j] <= 10⁵`  
6. `grid[0][0] == 0`  

---

### 🧪 Examples  

#### Example 1:  

**Input**:  
```python
grid = [[0,1,3,2],[5,1,2,5],[4,3,8,6]]
```

**Output**:  
```python
7
```

**Explanation**:  
One of the valid paths to reach `(2,3)` is:  
1. Start at `(0,0)` at `t = 0`.  
2. Move to `(0,1)` at `t = 1` (allowed because `grid[0][1] <= 1`).  
3. Move to `(1,1)` at `t = 2` (allowed because `grid[1][1] <= 2`).  
4. Move to `(1,2)` at `t = 3`.  
5. Move back and forth between cells to adjust timing.  
6. Finally, move to `(2,3)` at `t = 7`.  

---

#### Example 2:  

**Input**:  
```python
grid = [[0,2,4],[3,2,1],[1,0,4]]
```

**Output**:  
```python
-1
```

**Explanation**:  
There is no valid path from the top-left to the bottom-right corner.

---

### 🚀 Approach to the Solution 🚀


### **1. Understand the Constraints**

- Each cell has a minimum time requirement (`grid[row][col]`), which dictates when the cell becomes accessible.
- Movement is allowed only to the adjacent cells (up, down, left, right), and each step takes exactly **1 second**.
- If the **current time** is less than the cell's minimum time requirement, you must **wait** until the cell becomes accessible.
- Waiting can involve time adjustments due to parity (odd/even differences in time).

---

### **2. Represent the Problem as a Graph**

The grid can be interpreted as a graph:
- Each cell is a **node**.
- Each adjacent cell is connected by an **edge** with a cost of **1 second** plus any required waiting time.

The task is to find the **shortest time** to traverse this graph from the top-left corner `(0, 0)` to the bottom-right corner `(m-1, n-1)`.

---

### **3. Edge Case Handling**

Before starting the traversal:
- Check if the top-left cell's neighbors (i.e., `(0, 1)` and `(1, 0)`) are accessible at the start.
- If both neighbors require a time greater than `1` to enter, it’s impossible to leave the starting cell, and the result is `-1`.

---

### **4. Algorithm Choice**

Since the problem involves finding the shortest path in a weighted graph, **Dijkstra’s algorithm** is an ideal choice. It efficiently finds the shortest paths by exploring nodes in the order of increasing cost (time, in this case).

---

### **5. Key Steps in Dijkstra’s Algorithm**

1. **Initialization:**
   - Use a priority queue (min-heap) to process cells in increasing order of time.
   - Maintain a `distance` matrix initialized to infinity (`inf`), where `distance[row][col]` represents the minimum time to reach a cell.
   - Start with `distance[0][0] = 0` and push `(0, 0, 0)` (time, row, column) into the priority queue.

2. **Traverse the Grid:**
   - While the priority queue is not empty:
     - Pop the cell `(time, row, col)` with the smallest time.
     - For each adjacent cell `(new_row, new_col)`:
       - Check if it is within grid bounds.
       - Calculate the **time to reach** the cell:
         - If the current time plus 1 (`time + 1`) is **less than** the cell's minimum time requirement, compute the **waiting time**:
           - Adjust for parity to ensure that the new time aligns with the cell's accessibility condition.
       - If the computed time is **better** (smaller) than the current `distance[new_row][new_col]`, update it and push the cell into the priority queue.

3. **Stop When the Target is Reached:**
   - If the bottom-right cell `(m-1, n-1)` is dequeued, return its time as the result.
   - If the queue is exhausted and the target is not reached, return `-1`.

---

### **6. Handle Waiting Time and Parity**

If the current time (`t`) is less than `grid[new_row][new_col]`, calculate the waiting time:
- The waiting time must align the **parity** (odd/even) of the current time to match the cell's accessibility time.

For example:
- If `grid[new_row][new_col] = 4` and `t = 3`, wait until `t = 4`.
- If `grid[new_row][new_col] = 5` and `t = 4`, wait until `t = 5` (or another odd number).

---

### 🛠️ Basic Solution  

We can treat the grid as a **graph**, where each cell is a node, and the edges represent possible moves. The challenge is that each cell has a "waiting time"⏳. If you arrive at a cell before its unlock time, you must wait until it is accessible.  

We can solve this using **Dijkstra's algorithm** 🐍 with a priority queue to always explore the shortest available path.  

---

### 🐍 Python Implementation 🐍

Let’s dive deeper into the Python implementation of the solution. We'll walk through the logic, the key components, and how they work together to achieve the goal of finding the minimum time to traverse the grid.

---

Here's the complete implementation:

```python
from heapq import heappush, heappop
from math import inf
from typing import List

class Solution:
    def minimumTime(self, grid: List[List[int]]) -> int:
        # Step 1: Handle edge case where starting cell neighbors are inaccessible
        if grid[0][1] > 1 and grid[1][0] > 1:
            return -1

        # Step 2: Initialize dimensions and data structures
        rows, cols = len(grid), len(grid[0])
        distance = [[inf] * cols for _ in range(rows)]
        distance[0][0] = 0  # Starting point has distance 0
        priority_queue = [(0, 0, 0)]  # (time, row, col)
        
        # Directions for movement: up, down, left, right
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]

        # Step 3: Implement Dijkstra's algorithm using a priority queue
        while priority_queue:
            time, row, col = heappop(priority_queue)

            # If we reach the bottom-right corner, return the minimum time
            if row == rows - 1 and col == cols - 1:
                return time

            # Explore all neighbors
            for dr, dc in directions:
                new_row, new_col = row + dr, col + dc

                # Check boundaries
                if 0 <= new_row < rows and 0 <= new_col < cols:
                    # Calculate new time to reach the neighbor
                    new_time = time + 1
                    if new_time < grid[new_row][new_col]:
                        # Adjust for waiting time based on parity
                        wait_time = (grid[new_row][new_col] - new_time) % 2
                        new_time = grid[new_row][new_col] + wait_time
                    
                    # Update distance if a shorter path is found
                    if new_time < distance[new_row][new_col]:
                        distance[new_row][new_col] = new_time
                        heappush(priority_queue, (new_time, new_row, new_col))

        # If we cannot reach the bottom-right corner, return -1
        return -1
```

---

### **Explanation of Code**

#### **1. Handle Edge Cases**
- The very first step checks whether the starting point’s neighbors are immediately inaccessible:
  ```python
  if grid[0][1] > 1 and grid[1][0] > 1:
      return -1
  ```
  If both neighbors require more than `1` second to enter, it’s impossible to leave the starting cell, and we return `-1`.

---

#### **2. Initialize Variables**
- `rows` and `cols`: Dimensions of the grid.
- `distance`: A 2D list initialized with `inf`, representing the shortest time to each cell. The starting point `(0, 0)` is set to `0`.
- `priority_queue`: A min-heap that processes cells in order of their time. Initially, it contains only the starting point `(0, 0, 0)` (time, row, column).

---

#### **3. Dijkstra’s Algorithm**
- The algorithm explores the grid while always processing the cell with the smallest time first:
  ```python
  while priority_queue:
      time, row, col = heappop(priority_queue)
  ```
- For each cell, its neighbors are checked:
  ```python
  for dr, dc in directions:
      new_row, new_col = row + dr, col + dc
      if 0 <= new_row < rows and 0 <= new_col < cols:
  ```
- The **new time** to reach the neighbor is calculated:
  ```python
  new_time = time + 1
  ```
  If the cell’s minimum time requirement (`grid[new_row][new_col]`) is greater than `new_time`, waiting is applied:
  ```python
  wait_time = (grid[new_row][new_col] - new_time) % 2
  new_time = grid[new_row][new_col] + wait_time
  ```

---

#### **4. Update the Priority Queue**
- If the computed `new_time` is less than the previously known `distance[new_row][new_col]`, the distance is updated, and the cell is pushed into the priority queue:
  ```python
  if new_time < distance[new_row][new_col]:
      distance[new_row][new_col] = new_time
      heappush(priority_queue, (new_time, new_row, new_col))
  ```

---

#### **5. Return the Result**
- If the bottom-right cell is reached, its time is returned:
  ```python
  if row == rows - 1 and col == cols - 1:
      return time
  ```
- If the queue is exhausted and the target cell isn’t reached, return `-1`:
  ```python
  return -1
  ```

---

### **Key Features of the Implementation**

1. **Efficient Priority Queue**: 
   - The min-heap ensures that the cell with the smallest time is always processed first.
   - This minimizes unnecessary computations.

2. **Handling Waiting Time**:
   - The modulo operation aligns the waiting time to the cell’s required parity.

3. **Boundary Checks**:
   - All neighbors are validated to ensure they’re within grid boundaries.

4. **Optimal Time Complexity**:
   - The algorithm runs in $$O(m \cdot n \cdot \log(m \cdot n))$$, making it efficient for large grids.

---

### ⚠️ **Edge Cases**
1. **Starting Neighbors Blocked**:
   - Grid like `[[0, 3], [3, 3]]` → Should return `-1`.

2. **Single Path**:
   - Grid like `[[0, 1, 2, 3]]` → Ensure the algorithm handles linear paths correctly.

3. **Immediate Accessibility**:
   - Grid like `[[0, 0], [0, 0]]` → Should return the minimum time of moving directly to the target.

4. **Waiting in Place**:
   - Grid like `[[0, 2], [1, 3]]` → Requires waiting at specific cells before advancing.

By thoroughly testing these cases, the implementation can be validated for correctness.


---

#### ⏳ Time Complexity  
- **Initialization**: $$O(m \cdot n)$$  
- **Heap Operations**: $$O((m \cdot n) \cdot \log(m \cdot n))$$  
**Total**: $$O(m \cdot n \cdot \log(m \cdot n))$$  

#### 🗄️ Space Complexity  
- $$O(m \cdot n)$$ for the distance matrix and heap.  

---

### ⚡ Optimized Solution  

The basic solution already employs an optimized approach by leveraging Dijkstra’s algorithm and minimizing redundant computations.

---

### 🧮 Example Walkthrough  

Let’s take a **larger and more detailed example** to illustrate the problem and solution step by step. This walkthrough will include **specific grid configurations**, the decisions made during traversal, and the logic applied at each step.

---

#### **Example**

**Input Grid**:

```python
grid = [
    [0, 2, 4, 6],
    [3, 1, 7, 5],
    [8, 3, 2, 10],
    [9, 12, 4, 8]
]
```

**Output**: `12`

**Explanation**: Let’s go step-by-step to find the **minimum time** required to traverse from the top-left cell `(0,0)` to the bottom-right cell `(3,3)`.

---

#### **Step 1: Starting at (0,0)**
- At time `t=0`, we are at `(0,0)`. This cell is accessible because `grid[0][0] = 0`.
- Push `(0,0,0)` into the priority queue: `(time, row, col)`.

---

#### **Step 2: Exploring Neighbors**
At `t=0`, we process `(0,0)`. We explore its neighbors:

1. **Right to `(0,1)`**:
   - `grid[0][1] = 2` requires `t ≥ 2`. 
   - We’ll wait at `(0,0)` until `t=2`, then move to `(0,1)`.
   - Update distance for `(0,1)` to `t=2` and push `(2,0,1)` into the queue.

2. **Down to `(1,0)`**:
   - `grid[1][0] = 3` requires `t ≥ 3`.
   - We’ll wait at `(0,0)` until `t=3`, then move to `(1,0)`.
   - Update distance for `(1,0)` to `t=3` and push `(3,1,0)` into the queue.

---

#### **Step 3: Process (0,1)**
At `t=2`, we process `(0,1)`. We explore its neighbors:

1. **Right to `(0,2)`**:
   - `grid[0][2] = 4` requires `t ≥ 4`.
   - We’ll wait at `(0,1)` until `t=4`, then move to `(0,2)`.
   - Update distance for `(0,2)` to `t=4` and push `(4,0,2)` into the queue.

2. **Down to `(1,1)`**:
   - `grid[1][1] = 1` is accessible at `t=3`.
   - Update distance for `(1,1)` to `t=3` and push `(3,1,1)` into the queue.

---

#### **Step 4: Process (1,0)**
At `t=3`, we process `(1,0)`. We explore its neighbors:

1. **Right to `(1,1)`**:
   - Already reached with `t=3`. Skip.

2. **Down to `(2,0)`**:
   - `grid[2][0] = 8` requires `t ≥ 8`.
   - We’ll wait at `(1,0)` until `t=8`, then move to `(2,0)`.
   - Update distance for `(2,0)` to `t=8` and push `(8,2,0)` into the queue.

---

#### **Step 5: Process (1,1)**
At `t=3`, we process `(1,1)`. We explore its neighbors:

1. **Right to `(1,2)`**:
   - `grid[1][2] = 7` requires `t ≥ 7`.
   - We’ll wait at `(1,1)` until `t=7`, then move to `(1,2)`.
   - Update distance for `(1,2)` to `t=7` and push `(7,1,2)` into the queue.

2. **Down to `(2,1)`**:
   - `grid[2][1] = 3` is accessible at `t=4`.
   - Update distance for `(2,1)` to `t=4` and push `(4,2,1)` into the queue.

---

#### **Step 6: Process (0,2), (2,1), and Beyond**
Following the same logic:

1. At `t=4`, process `(0,2)`:
   - Move to `(0,3)` at `t=6`.
   - Push `(6,0,3)`.

2. At `t=4`, process `(2,1)`:
   - Move to `(2,2)` at `t=5`.
   - Push `(5,2,2)`.

3. At `t=6`, process `(0,3)`:
   - Move to `(1,3)` at `t=7`.
   - Push `(7,1,3)`.

4. At `t=8`, process `(2,0)`:
   - Move to `(3,0)` at `t=9`.
   - Push `(9,3,0)`.

---

#### **Step 7: Reach (3,3)**
Finally, at `t=12`, process `(3,3)`. Since we’ve reached the bottom-right corner, return `t=12`.

---

### **Path Taken**

The final path looks like this (with time values in parentheses):
```
(0,0) → (0,1)(2) → (1,1)(3) → (2,1)(4) → (2,2)(5) → (3,3)(12)
```

---

### **Key Observations in the Example**
1. **Waiting Periods**:
   - At cells where the required time is greater than the arrival time, waiting is necessary. This ensures alignment with the cell’s accessibility condition.

2. **Optimal Movement**:
   - The algorithm always processes the cell with the smallest time first, ensuring that the shortest path is evaluated before longer alternatives.

3. **Efficient Queue Management**:
   - Using a priority queue allows the algorithm to efficiently select the next cell to process.

4. **Dynamic Exploration**:
   - All directions are explored at each step, enabling the algorithm to handle complex grids with backtracking or detours.


---

### 📜 Conclusion  

This problem highlights the importance of **shortest-path algorithms** like Dijkstra's for grid-based challenges, especially when additional constraints (like waiting times) are involved. Understanding how to manipulate these constraints efficiently can drastically improve performance in complex scenarios. 🧠✨
