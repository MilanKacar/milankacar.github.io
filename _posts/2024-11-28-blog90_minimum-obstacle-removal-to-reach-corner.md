---
layout: post  
title: "#89 🔗 Connect Bitbucket to Dataform 🚀🧠"
categories: [Tutorial, Programming]
difficulty: Easy
tags: [GCP]
---

In this blog post, we'll tackle **LeetCode #2290: Minimum Obstacle Removal to Reach Corner**, an exciting graph-based problem that tests our algorithmic skills! We'll go step-by-step, exploring the problem statement, understanding the examples, diving into solutions, and optimizing our approach. Let's remove those obstacles and get coding! 💪✨

---

### Problem Statement 🧩

We are given a 2D grid `grid` of size `m x n`, where each cell can either be:
- **`0` (empty cell)**, allowing free movement, or
- **`1` (obstacle)**, which can be removed if necessary.

We need to determine the **minimum number of obstacles to remove** to create a path from the **top-left corner** `(0, 0)` to the **bottom-right corner** `(m - 1, n - 1)`.

You can move:
- Up, down, left, or right between adjacent cells, provided the target cell is an empty cell (`0`) or becomes empty after removing an obstacle.

---

### Examples 📝

#### Example 1:
**Input:**  
`grid = [[0, 1, 1], [1, 1, 0], [1, 1, 0]]`  
**Output:**  
`2`  

**Explanation:**  
- Remove the obstacles at `(0, 1)` and `(0, 2)`.  
- The resulting path is `(0,0) -> (0,1) -> (0,2) -> (1,2) -> (2,2)`.  
- At least `2` obstacles need to be removed.

---

#### Example 2:
**Input:**  
`grid = [[0, 1, 0, 0, 0], [0, 1, 0, 1, 0], [0, 0, 0, 1, 0]]`  
**Output:**  
`0`  

**Explanation:**  
- No obstacles need to be removed.  
- A direct path exists: `(0,0) -> (0,1) -> (0,2) -> ... -> (2,4)`.

---

### Constraints ⚙️

- $$ 1 \leq m, n \leq 10^5 $$
- $$ 2 \leq m \cdot n \leq 10^5 $$
- `grid[i][j]` is either `0` or `1`.
- `grid[0][0] == grid[m - 1][n - 1] == 0`.

---

### Edge Cases 🔍

1. **Smallest Grid:**  
   A 2x2 grid with no obstacles:  
   `grid = [[0, 0], [0, 0]]` -> Output: `0`.
   
2. **No Possible Path Without Removals:**  
   `grid = [[0, 1], [1, 0]]` -> Output: `1`.

3. **Dense Obstacles:**  
   A grid where most cells are `1`, except for the start and end points:  
   `grid = [[0, 1], [1, 0]]`.

4. **All Empty:**  
   `grid = [[0, 0, 0], [0, 0, 0], [0, 0, 0]]` -> Output: `0`.

5. **Large Grid with Complex Obstacles:**  
   A very large grid (e.g., $$ 1000 \times 1000 $$) with a mix of obstacles.

---

### Approach to the Solution 🚀

This problem challenges us to navigate from the top-left to the bottom-right corner of a grid while removing as few obstacles as possible. To design an effective solution, we must carefully model the problem, choose the right algorithm, and handle large input sizes efficiently. Let’s explore the key ideas step by step.

---

### 1️⃣ Modeling the Problem as a Graph 🌐

The **grid** can be visualized as a **graph**, where:
- **Nodes** represent the individual cells of the grid.
- **Edges** exist between adjacent cells (up, down, left, right).
- **Edge Weights** depend on the type of cell being visited:
  - If the cell is empty (`0`), the weight is **0**.
  - If the cell has an obstacle (`1`), the weight is **1** (representing the cost of removing the obstacle).

#### Key Insight: Weighted Graph with Costs
By representing the grid in this way:
- The problem reduces to finding the **shortest path** from the starting node `(0, 0)` to the ending node `(m-1, n-1)`, where the path cost is the **number of obstacles removed**.

This is not a standard **unweighted shortest path** problem, so algorithms like **Breadth-First Search (BFS)** need to be adapted or replaced.

---

### 2️⃣ Why Not Use Standard BFS? 🤔

In a standard BFS:
- Each node is processed once, and all adjacent nodes are visited without considering edge weights.

However, here:
- Moving to an obstacle (`1`) is more "expensive" than moving to an empty cell (`0`).
- Standard BFS cannot differentiate between "cheap" and "costly" moves, leading to incorrect results.

#### The Need for Weighted BFS
To account for weights, we need a strategy that:
1. Prioritizes paths with lower weights (fewer obstacles removed).
2. Dynamically adjusts the order of exploration based on the cost.

---

### 3️⃣ Choosing the Right Algorithm 🚀

Two algorithms can efficiently solve this problem:

#### **A. 0-1 Breadth-First Search (0-1 BFS)**

This variant of BFS is ideal for graphs where edge weights are either `0` or `1`. It uses a **deque** to maintain the order of exploration:
- Nodes with edge weight `0` are added to the **front** of the deque (higher priority).
- Nodes with edge weight `1` are added to the **back** of the deque (lower priority).

**Advantages of 0-1 BFS:**
- Highly efficient for problems with binary weights (`0` or `1`).
- Avoids unnecessary exploration of higher-cost paths early.

#### **B. Dijkstra’s Algorithm**

This classic shortest-path algorithm can handle graphs with arbitrary non-negative weights. It uses a **priority queue** (min-heap) to always explore the "cheapest" path first:
- Each node is processed in order of its current total cost.
- All adjacent nodes are updated based on their edge weights.

**Advantages of Dijkstra’s Algorithm:**
- Handles more general cases with varied weights.
- Guaranteed to find the optimal path.

However, **0-1 BFS** is faster in this specific problem because it leverages the binary nature of the edge weights (`0` and `1`), avoiding the overhead of a priority queue.

---

### 4️⃣ Implementing 0-1 BFS 🏃‍♂️

#### Core Idea of 0-1 BFS:
1. Start with the top-left corner `(0, 0)` and initialize its cost as `0`.
2. Use a **deque** to keep track of cells to be processed:
   - Add empty cells (`0`) to the **front** of the deque.
   - Add obstacle cells (`1`) to the **back** of the deque.
3. Process cells in the deque:
   - For each cell, check all valid adjacent cells.
   - Update their costs based on the current cell's cost and edge weight.
   - Push them to the deque appropriately (front or back).

4. Stop when the bottom-right corner `(m-1, n-1)` is reached.

#### Why Does This Work?
- By always prioritizing moves with cost `0` (empty cells), the algorithm ensures that lower-cost paths are explored first.
- The **deque** efficiently handles reordering of cells without the need for a more complex data structure like a heap.

---

### 5️⃣ Complexity Analysis 🔬

Let’s analyze why this approach is efficient:

#### **Time Complexity**
- Each cell is processed at most twice:
  - Once for cost `0` (added to the front of the deque).
  - Once for cost `1` (added to the back of the deque).
- Checking all neighbors takes $$ O(4) $$ time per cell.
- Overall, the time complexity is **$$ O(m \cdot n) $$**, where $$ m $$ and $$ n $$ are the grid dimensions.

#### **Space Complexity**
- The algorithm maintains:
  - A `costs` array of size $$ m \cdot n $$.
  - A `deque` that can hold up to $$ m \cdot n $$ elements in the worst case.
- Total space complexity is **$$ O(m \cdot n) $$**.

---

### 6️⃣ Adapting for Dijkstra’s Algorithm 🏗️

If we were to use **Dijkstra’s Algorithm** instead of 0-1 BFS:
1. Replace the **deque** with a **priority queue** (min-heap).
2. Instead of pushing cells to the front or back, push them into the heap with their respective costs.
3. Always process the cell with the smallest current cost.

#### Complexity of Dijkstra’s Algorithm:
- **Time Complexity:** $$ O((m \cdot n) \log(m \cdot n)) $$, due to heap operations.
- **Space Complexity:** $$ O(m \cdot n) $$, similar to 0-1 BFS.

#### Why Choose 0-1 BFS Over Dijkstra?
- **Faster:** 0-1 BFS avoids the overhead of heap operations, making it more efficient for grids with binary weights.
- **Simplicity:** Using a deque simplifies implementation compared to managing a priority queue.

---

### 7️⃣ Handling Edge Cases ⚠️

To ensure robustness, the algorithm must handle edge cases, such as:
1. **No Obstacles:**  
   If all cells are `0`, the path can be directly traversed without any removals.

2. **Completely Blocked Path:**  
   If all possible paths are blocked, the algorithm should terminate without entering an infinite loop.

3. **Dense Grids:**  
   In grids with many obstacles, the algorithm efficiently prioritizes paths that minimize removals.

4. **Large Grid Sizes:**  
   For grids approaching the upper constraint of $$ 10^5 $$ cells, the $$ O(m \cdot n) $$ complexity ensures scalability.

---

### Summary of Approaches 💡

| **Algorithm**          | **Strengths**                                   | **Weaknesses**                       |
|-------------------------|------------------------------------------------|--------------------------------------|
| **0-1 BFS**             | Fast for binary weights, simple implementation | Limited to weights `0` and `1`       |
| **Dijkstra's Algorithm**| Handles general weights, widely applicable     | Slower due to priority queue overhead|

For this problem, **0-1 BFS** is the clear winner due to its efficiency and suitability for binary-weighted graphs.

---

### Solution 💡

#### Optimized Solution: Using 0-1 BFS 🏃‍♂️

**Why 0-1 BFS?**  
In this approach:
- **`deque`** is used to maintain the order of exploration.
- Cells with weight $$ 0 $$ are added to the **front** of the deque (priority for free moves).
- Cells with weight $$ 1 $$ are added to the **back** of the deque (less priority for obstacle removal).

---

### Python Implementation 🐍

### 🐍 Python Implementation: Bringing It to Life! 🚀

Implementing the solution in Python requires careful attention to both clarity and efficiency. We’ll focus on **0-1 BFS** as it is more efficient for this problem. Below is a detailed explanation of the code, followed by the complete implementation.

---

### 🔧 Step-by-Step Python Implementation

#### 1️⃣ **Initialization**
- Import necessary modules, such as `deque` from `collections` for the BFS queue.
- Define a `directions` array to facilitate exploring the four possible moves (up, down, left, right).

#### 2️⃣ **Using a Deque for 0-1 BFS**
- Initialize the deque with the starting cell `(0, 0)` and an initial cost of `0`.
- Use a 2D `cost` array to track the minimum cost of reaching each cell. Initialize it with infinity (`float('inf')`) except for the starting cell, which has a cost of `0`.

#### 3️⃣ **Processing the Deque**
- While the deque is not empty:
  1. Pop the cell at the front of the deque.
  2. Explore all four neighbors of the current cell.
  3. If a neighbor can be reached with a smaller cost, update its cost:
     - If the cell is `0`, append it to the **front** of the deque.
     - If the cell is `1`, append it to the **back** of the deque.

#### 4️⃣ **Termination**
- The loop ends when all reachable cells are processed. The value at the bottom-right corner `(m-1, n-1)` in the `cost` array gives the result.

#### 5️⃣ **Edge Cases**
- Handle edge cases like grids with no obstacles, grids with only one cell, or completely blocked grids.

---

### 💻 Python Implementation

```python
from collections import deque

def minimumObstacles(grid):
    # Dimensions of the grid
    m, n = len(grid), len(grid[0])
    
    # Initialize deque for 0-1 BFS
    deque_bfs = deque([(0, 0)])  # Start at (0, 0) with 0 cost
    costs = [[float('inf')] * n for _ in range(m)]
    costs[0][0] = 0  # Cost to reach the starting cell is 0
    
    # Directions for moving in the grid (up, down, left, right)
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    
    while deque_bfs:
        x, y = deque_bfs.popleft()
        
        # Explore all 4 neighbors
        for dx, dy in directions:
            nx, ny = x + dx, y + dy
            
            # Check if the neighbor is within bounds
            if 0 <= nx < m and 0 <= ny < n:
                # Calculate new cost
                new_cost = costs[x][y] + grid[nx][ny]
                
                # Update cost if a cheaper path is found
                if new_cost < costs[nx][ny]:
                    costs[nx][ny] = new_cost
                    if grid[nx][ny] == 0:
                        # Push to the front if it's an empty cell
                        deque_bfs.appendleft((nx, ny))
                    else:
                        # Push to the back if it's an obstacle
                        deque_bfs.append((nx, ny))
    
    # Return the cost to reach the bottom-right corner
    return costs[m-1][n-1]
```

---

### 🚀 Example Walkthrough: Step-by-Step

Let’s walk through an example using the function:

#### Input
```python
grid = [
    [0, 1, 1],
    [1, 1, 0],
    [1, 1, 0]
]
```

#### Initial Setup
1. **Grid Dimensions:** $$ m = 3, n = 3 $$
2. **Deque Initialization:** `deque([(0, 0)])`
3. **Cost Array:**
   ```plaintext
   0    inf    inf
   inf  inf    inf
   inf  inf    inf
   ```

#### Step 1: Process Cell `(0, 0)`
- Current deque: `deque([(0, 0)])`
- Explore neighbors:
  1. `(1, 0)` → Update cost to `1` → Add to the back.
  2. `(0, 1)` → Update cost to `1` → Add to the back.
- Deque after processing: `deque([(1, 0), (0, 1)])`

#### Step 2: Process Cell `(1, 0)`
- Current deque: `deque([(1, 0), (0, 1)])`
- Explore neighbors:
  1. `(2, 0)` → Update cost to `2` → Add to the back.
  2. `(1, 1)` → Update cost to `2` → Add to the back.
- Deque after processing: `deque([(0, 1), (2, 0), (1, 1)])`

#### Final Cost Array
After processing all cells:
```plaintext
0    1    2
1    2    2
2    3    2
```
- **Output:** `2`

---

### 🛠️ Optimizations and Scalability

1. **Memory Management:**
   - Instead of maintaining a full `costs` array, use a dictionary to store only the cells that are visited.

2. **Dynamic Grid Sizes:**
   - The implementation scales well for large grids due to its $$ O(m \cdot n) $$ complexity.

3. **Parallelism:**
   - For even faster performance, parts of the grid could be processed in parallel.

---

### Example Walkthrough 🎯

#### Input:
`grid = [[0, 1, 1], [1, 1, 0], [1, 1, 0]]`

**Step 1:** Initialize:  
- `costs` =  
  $$
  \begin{bmatrix}
  0 & \infty & \infty \\
  \infty & \infty & \infty \\
  \infty & \infty & \infty
  \end{bmatrix}
  $$
- `deque` = `[(0, 0)]`.

**Step 2:** Start BFS:  
1. Visit `(0, 0)`, explore its neighbors:
   - `(0, 1)` -> cost `1`, add to deque.
   - `(1, 0)` -> cost `1`, add to deque.

**Step 3:** Update Costs and Continue:  
- Costs after first round:
  $$
  \begin{bmatrix}
  0 & 1 & \infty \\
  1 & \infty & \infty \\
  \infty & \infty & \infty
  \end{bmatrix}
  $$

Continue until `(2, 2)` is reached with cost `2`.

---

### Complexity Analysis 🔬

- **Time Complexity:**  
  $$ O(m \cdot n) $$, as each cell is processed at most twice (once for cost $$ 0 $$, once for cost $$ 1 $$).
  
- **Space Complexity:**  
  $$ O(m \cdot n) $$, for the `costs` array and `deque`.

---

### Conclusion 🎉

- This problem showcases the power of **graph traversal** techniques like **0-1 BFS**.  
- It emphasizes the importance of efficient algorithms for large-scale problems.  
- By carefully managing priorities (obstacle vs. free moves), we can find the optimal path with minimal cost!  
