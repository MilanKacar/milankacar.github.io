---
layout: post  
title: "#112 💧 407. Trapping Rain Water II 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Breadth-First Search, Heap (Priority Queue), Matrix]
---

Welcome to another thrilling coding adventure! 🚀 Today, we’re tackling **LeetCode Problem #407: Trapping Rain Water II**, a mind-bending challenge that’ll have you imagining rainwater collecting in valleys of a 2D elevation map. 🌄 

💡 Before we get started, let’s set the scene. Imagine you're surveying a landscape after a rainstorm. The elevation varies, and you're curious about how much water gets trapped between the heights. Well, that's exactly what this problem is about—only this time, we’re calculating it programmatically! 🧑‍💻 Let’s jump in! 

---

## 📜 Problem Statement

Given an `m x n` integer matrix `heightMap`, where each value represents the height of a unit cell in a 2D elevation map, return the total volume of water trapped after raining. Water cannot flow out of the outermost boundaries of the map, and it is always trapped according to the heights of the neighboring cells.

### Constraints:
- $$ m == \text{heightMap.length} $$
- $$ n == \text{heightMap[i].length} $$
- $$ 1 \leq m, n \leq 200 $$
- $$ 0 \leq \text{heightMap[i][j]} \leq 20,000 $$

---

## 🛠️ Example Walkthrough

### Example 1:

**Input:**

```python
heightMap = [
  [1, 4, 3, 1, 3, 2],
  [3, 2, 1, 3, 2, 4],
  [2, 3, 3, 2, 3, 1]
]
```

**Output:** `4`

**Explanation:** 

After rainfall, two small ponds form:
1. A pond holding **1 unit** of water.  
2. Another pond holding **3 units** of water.

The total trapped water is **4 units**.

---

### Example 2:

**Input:**

```python
heightMap = [
  [3, 3, 3, 3, 3],
  [3, 2, 2, 2, 3],
  [3, 2, 1, 2, 3],
  [3, 2, 2, 2, 3],
  [3, 3, 3, 3, 3]
]
```

**Output:** `10`

**Explanation:** 

Here, the outermost boundary blocks water flow, trapping **10 units** of water in the inner valleys. 🌊

---

### Solution Approach for Trapping Rain Water II 🏞️💧

The problem of calculating the volume of water that can be trapped after raining over a 2D elevation map is a classic example of utilizing **data structures** like a **priority queue (min-heap)** in conjunction with **matrix traversal techniques**. Here's an in-depth explanation of the solution approach, broken into manageable steps for clarity.

---

#### Step 1: Visualizing the Problem 🌄

The first step in understanding this problem is to think about how water behaves in a 2D landscape. Water always flows towards lower elevations and gets trapped in areas surrounded by higher barriers. The goal is to simulate this behavior programmatically and calculate the total trapped water volume.

- **Key Insight**: The water level at any cell in the grid depends on the heights of the barriers surrounding it. The lowest surrounding barrier determines how much water the cell can hold.
- Imagine each boundary of the grid as a barrier, and consider how water might flow inward from these boundaries.

---

#### Step 2: Leveraging the Min-Heap for Efficiency 🧮

A **min-heap** is instrumental in solving this problem efficiently. Why? Because it allows us to process cells with the smallest heights first, ensuring that we consider lower barriers before higher ones. 

- Start by adding all the boundary cells of the grid to the heap. These act as the initial barriers that control water flow into the interior.
- Use a **visited set** to track which cells have been processed to avoid redundant computations.

---

#### Step 3: Simulating the Water Flow 🚰

With the boundaries initialized, the next task is to simulate how water flows inward, cell by cell:

1. **Pop the smallest height cell** from the heap. This represents the current barrier height.
2. Check its unvisited neighbors (up, down, left, right):
   - If the neighbor's height is lower than the current barrier height, water will be trapped. The volume of water trapped is the difference between the current barrier height and the neighbor's height.
   - Regardless of whether water is trapped, the neighbor cell becomes part of the boundary. Update its effective height in the heap to reflect the barrier controlling it.
3. Mark the neighbor as visited and repeat the process.

This method ensures that water flows are calculated in an orderly manner, starting from the lowest barriers and progressively moving inward.

---

#### Step 4: Handling Edge Cases 🔍

When working with a 2D matrix, edge cases often arise. Let’s consider some critical scenarios:

1. **Flat Elevation**:
   - If all cells in the height map have the same height, no water can be trapped since there are no depressions.
   - Example: `[[3, 3, 3], [3, 3, 3], [3, 3, 3]]`.

2. **Single Row or Column**:
   - If the matrix has only one row or column, water cannot be trapped because there’s no enclosed space.
   - Example: `[[1, 2, 3, 4]]`.

3. **Large Depressions**:
   - Consider deep pits surrounded by tall barriers. The algorithm must correctly identify the maximum possible water volume without overestimating or underestimating.

4. **Non-rectangular Shapes**:
   - Uneven grids or shapes that don’t align perfectly with boundaries might introduce complications. Although not directly applicable in this problem (as the grid is always rectangular), it’s a good thought experiment.

---

#### Step 5: Time Complexity Analysis ⏳

The efficiency of the algorithm is paramount, given the constraints (`1 <= m, n <= 200`). Let’s break down the time complexity:

- **Initialization**: Adding all boundary cells to the heap takes $$O(m + n)$$, where $$m$$ and $$n$$ are the dimensions of the matrix.
- **Heap Operations**: Each cell in the matrix is processed once, resulting in $$O(m \cdot n \cdot \log(m \cdot n))$$ operations due to heap insertion and deletion.
- **Total Complexity**: The overall time complexity is $$O(m \cdot n \cdot \log(m \cdot n))$$.

This efficiency is acceptable for the input limits and allows the algorithm to handle large grids effectively.

---

#### Step 6: Space Complexity Analysis 📦

The space complexity includes the following components:

- **Min-Heap**: Stores the boundary cells and their effective heights. At most, the heap contains $$m \cdot n$$ elements, leading to a space complexity of $$O(m \cdot n)$$.
- **Visited Set**: Tracks processed cells to prevent redundant computations, also requiring $$O(m \cdot n)$$ space.
- **Auxiliary Variables**: Directions array, counters, and temporary variables contribute negligibly.

Thus, the total space complexity is $$O(m \cdot n)$$, making the solution scalable for large inputs.

---

#### Step 7: Understanding the Core Idea with an Example 📊

Let’s revisit the example:

**Input**:  
``` 
heightMap = [[1, 4, 3, 1, 3, 2], 
             [3, 2, 1, 3, 2, 4], 
             [2, 3, 3, 2, 3, 1]]
```

- **Boundary Initialization**: Add all boundary cells to the heap, marking them as visited. The initial heap contains:
  - `[(1,0,0), (4,0,1), (3,0,2), ..., (2,2,5)]`.

- **First Iteration**:
  - Pop `(1,0,0)` from the heap.
  - Check its neighbors:
    - `(0,1)` has a height of 4. No water is trapped because it’s higher.
    - `(1,0)` has a height of 3. No water is trapped.
  - Add these cells to the heap and mark them as visited.

- **Subsequent Iterations**:
  - Process the heap iteratively, always expanding the boundary inward.
  - Update trapped water volumes as you encounter lower elevations.

By the end of the process, the total water trapped is `4`.

---

#### Step 8: Why This Approach Works 🧠

This method works because it mimics how water naturally behaves. The priority queue ensures that lower barriers are processed first, allowing water to flow naturally into depressions. By keeping track of visited cells and dynamically adjusting the barrier heights, the algorithm ensures accurate volume calculations without redundant checks.

---

#### Conclusion 🌟

This approach to solving the Trapping Rain Water II problem is both elegant and efficient, combining **heap-based traversal** with **matrix manipulation techniques**. It ensures that the solution is scalable, handles edge cases gracefully, and adheres to the constraints effectively. By breaking the problem into logical steps and leveraging key data structures, we achieve an optimal balance of clarity and performance.

---

### 🧑‍💻 Python Code

Here’s the Python implementation:

```python
import heapq

class Solution:
    def trapRainWater(self, heightMap: list[list[int]]) -> int:
        if not heightMap or not heightMap[0]:
            return 0

        m, n = len(heightMap), len(heightMap[0])
        minHeap = []
        visited = set()
        dirs = [(0, 1), (1, 0), (0, -1), (-1, 0)]
        trapped_water = 0

        # Add all boundary cells to the minHeap
        for i in range(m):
            for j in [0, n-1]:
                heapq.heappush(minHeap, (heightMap[i][j], i, j))
                visited.add((i, j))
        for j in range(1, n-1):
            for i in [0, m-1]:
                heapq.heappush(minHeap, (heightMap[i][j], i, j))
                visited.add((i, j))

        # Process cells in the minHeap
        while minHeap:
            height, x, y = heapq.heappop(minHeap)
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n and (nx, ny) not in visited:
                    visited.add((nx, ny))
                    # Calculate water trapped
                    trapped_water += max(0, height - heightMap[nx][ny])
                    # Push the higher of the two heights into the heap
                    heapq.heappush(minHeap, (max(height, heightMap[nx][ny]), nx, ny))

        return trapped_water
```

---

### 🌟 Example Walkthrough (Higher Numbers)

Let’s revisit the **second example** with more detail:

**Input:**

```python
heightMap = [
  [3, 3, 3, 3, 3],
  [3, 2, 2, 2, 3],
  [3, 2, 1, 2, 3],
  [3, 2, 2, 2, 3],
  [3, 3, 3, 3, 3]
]
```

1. **Step 1:** Add all boundary cells to the heap.  
   Initial Heap:  
   $$
   \{(3, 0, 0), (3, 0, 1), \dots, (3, 4, 4)\}
   $$

2. **Step 2:** Start processing.  
   - Pop the smallest height (3) and check neighbors.  
   - Trap water for neighbors with lower height (e.g., inner cells like `(1,1)`).

3. **Step 3:** Continue until all cells are visited.  

**Output:** `10`.

---

## 🔑 Key Takeaways

1. **Priority Queue:** Efficiently manage the smallest boundary height.  
2. **Boundary First Approach:** Ensures no water escapes the outer edges.  
3. **BFS with Heaps:** Combines search with dynamic boundary updates.

---

### ✨ Conclusion

This problem teaches us how to think **beyond brute force** and leverage efficient data structures to solve complex problems. It’s a perfect blend of **BFS**, **heaps**, and **matrix traversal**. 🎉
