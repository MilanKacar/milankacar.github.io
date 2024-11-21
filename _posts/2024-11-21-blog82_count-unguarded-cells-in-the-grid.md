---
layout: post  
title: "#82 🛡️ 2257. Count Unguarded Cells in the Grid 🧠🚀"  
categories: [LeetCode, Programming]  
---

Have you ever wondered how to ensure maximum coverage with minimal resources, like setting up surveillance cameras in a building or guarding the boundaries of a field? 🏢🔍 While it might seem like a logistics puzzle, problems like these often have practical solutions grounded in computational thinking. One such fascinating challenge is **LeetCode Problem #2257: Count Unguarded Cells in the Grid**. 🧩👀

Imagine a grid 📏 representing a room, with **guards** 👮 stationed to monitor the area and **walls** 🧱 creating obstacles. Each guard can "see" in all four cardinal directions—north, south, east, and west—until their line of sight is blocked. Your task? Figure out how many cells remain **unguarded and unblocked** after accounting for all the guards' visibility. 

This problem combines elements of **grid simulation**, **spatial traversal**, and efficient **line-of-sight calculations**. While it might sound complex at first, breaking it down step by step makes it surprisingly approachable! ✨ 

In this blog post, we’ll:
- Unpack the problem’s requirements 🔍,
- Simulate guard visibility step by step 👁️‍🗨️,
- Solve the problem with a clean Python implementation 🐍,
- Analyze the solution’s performance to ensure efficiency 🏎️.

By the end, you’ll have a crystal-clear understanding of how to tackle grid-based problems using computational strategies. Ready to dive in? Let’s get started! 🚀

### Problem Statement 🚀

In this exciting 🧩 **medium-level challenge**, you're tasked with calculating how many unoccupied cells 🟦 are neither **guarded** nor **blocked by walls** in an `m x n` grid. Here's the setup:

- **Guards** 👮 and **walls** 🧱 occupy specific grid cells.
- Each **guard** can see (and guard) all cells in the **four cardinal directions**—north, south, east, and west—until their view is blocked by a wall or another guard.
- Your mission is to count all **unguarded cells** 🟦 while considering guards' views and obstacles.

### ✨ Key Intuition
The solution to this problem involves **simulating** the guards' lines of sight in all four directions. Here's how we approach it:

1. **Mark the grid**: Walls 🧱 and guards 👮 occupy some cells, so we first mark these in our grid.
2. **Simulate guards' visibility**: For each guard, simulate the cells they can "see" in the four directions until blocked by a wall or another guard.
3. **Count the leftovers**: Any cell still unmarked after processing all guards is **unguarded** 🟦.

This approach uses **basic simulation** and **grid traversal**, which keeps it simple and efficient. Let's break it down! 🧑‍💻

---

### 🖍️ Solution Approach

#### Step-by-Step Breakdown
1. **Initialize the Grid** 🗂️: Create a grid `g` of size `m x n` with all cells initialized to `0` 🟦, representing **unguarded and empty cells**.

2. **Mark Guards and Walls** 🚧: Loop through the lists of guards 👮 and walls 🧱 to mark their positions with `2` in the grid.

3. **Simulate Guard Visibility** 👀:
   - For each guard, simulate visibility in all four directions using simple movement vectors.
   - Stop marking cells as guarded when you encounter a wall 🧱, another guard 👮, or reach the boundary of the grid.

4. **Count Unguarded Cells** 🔢: Finally, iterate through the grid and count all cells still marked as `0` 🟦.

---

### 🌟 Example Walkthrough

Let’s take an example grid of size `4 x 4` with the following inputs:

#### Input:
- `guards = [[1, 1], [2, 3]]` 👮
- `walls = [[0, 2], [2, 1]]` 🧱

#### 🟦 Initial Grid:
```
[ ][ ][ ][ ]
[ ][ ][ ][ ]
[ ][ ][ ][ ]
[ ][ ][ ][ ]
```

#### 📋 Step 1: Mark Guards and Walls
- Place guards 👮 at `(1, 1)` and `(2, 3)`.
- Place walls 🧱 at `(0, 2)` and `(2, 1)`.

```
[ ][ ][W][ ]
[ ][G][ ][ ]
[ ][W][ ][G]
[ ][ ][ ][ ]
```

#### 👀 Step 2: Simulate Guard Visibility
Let’s process each guard one by one:

1. **Guard at `(1, 1)`**:
   - **North** (upwards): Guards `(0, 1)`.
   - **East** (rightwards): Guards `(1, 2)` and stops at `(1, 3)`.
   - **South** (downwards): Stops at `(2, 1)` due to a wall.
   - **West** (leftwards): Guards `(1, 0)`.

```
[ ][1][W][ ]
[1][G][1][ ]
[ ][W][ ][G]
[ ][ ][ ][ ]
```

2. **Guard at `(2, 3)`**:
   - **North**: Guards `(1, 3)` and stops at `(0, 3)`.
   - **East**: Stops immediately at the grid boundary.
   - **South**: Guards `(3, 3)`.
   - **West**: Guards `(2, 2)` and stops at `(2, 1)` (wall).

```
[ ][1][W][ ]
[1][G][1][1]
[ ][W][1][G]
[ ][ ][ ][1]
```

#### 🔢 Step 3: Count Unguarded Cells
After processing all guards, the grid looks like this:

```
[ ][1][W][ ]
[1][G][1][1]
[ ][W][1][G]
[ ][ ][ ][1]
```

Count the number of `0`s 🟦 (unguarded and unoccupied cells):
- **Answer**: 5 unguarded cells.

---

### 🐍 Python Implementation

Here’s the Python code for the solution:

```python
from typing import List

class Solution:
    def countUnguarded(self, m: int, n: int, guards: List[List[int]], walls: List[List[int]]) -> int:
        # Initialize the grid
        grid = [[0] * n for _ in range(m)]
        
        # Mark guards and walls
        for r, c in guards:
            grid[r][c] = 2
        for r, c in walls:
            grid[r][c] = 2
        
        # Define movement directions
        directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]  # Up, Down, Left, Right
        
        # Simulate guards' visibility
        for r, c in guards:
            for dr, dc in directions:
                nr, nc = r, c
                while 0 <= nr + dr < m and 0 <= nc + dc < n and grid[nr + dr][nc + dc] == 0:
                    nr, nc = nr + dr, nc + dc
                    grid[nr][nc] = 1
        
        # Count unguarded cells
        return sum(cell == 0 for row in grid for cell in row)
```

---

### 📈 Complexity Analysis

#### **Time Complexity**:
1. **Grid Initialization**: $$O(m \times n)$$
2. **Marking Guards/Walls**: $$O(g + w)$$, where $$g$$ is the number of guards and $$w$$ is the number of walls.
3. **Simulating Visibility**: $$O(g \times (m + n))$$ for guards' line-of-sight simulation.

**Total**: $$O(m \times n + g \times (m + n))$$

#### **Space Complexity**:
- $$O(m \times n)$$ for the grid representation.

---

### 🏁 Conclusion

In this problem, we’ve explored how to simulate guards' lines of sight effectively using a grid-based approach. By marking walls and guards, simulating visibility, and counting leftover cells, we arrive at a clean and efficient solution. 🚀
