---
layout: post
title: "#43 🌳 427. Construct Quad Tree - LeetCode 🧠🚀"
categories: [LeetCode, Programming]
---

In this post, we're going to explore how to construct a **Quad-Tree** from a given `n x n` grid filled with 0's and 1's. A Quad-Tree is a tree data structure that is particularly useful for partitioning a two-dimensional space.

### Problem Statement 💡

We are given an `n x n` grid of `0`s and `1`s. The goal is to represent this grid using a **Quad-Tree**.

#### Key Features of a Quad-Tree:
1. Each **internal node** has exactly four children: `topLeft`, `topRight`, `bottomLeft`, and `bottomRight`.
2. Each node has two attributes:
   - `val`: It can be `True` (for 1's) or `False` (for 0's). If the node is not a leaf, this value can be arbitrary.
   - `isLeaf`: This is `True` if the node is a leaf (meaning it represents a uniform grid with all 0's or all 1's), and `False` otherwise.

The process for constructing a Quad-Tree involves recursively dividing the grid into four sub-grids until each region is uniform (either all 0's or all 1's).

### Example 1:
#### Input:
```python
grid = [[0, 1], [1, 0]]
```
#### Output:
```
[[0, 1], [1, 0], [1, 1], [1, 1], [1, 0]]
```

### Example 2:
#### Input:
```python
grid = [
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0]
]
```
#### Output:
```
[[0,1],[1,1],[0,1],[1,1],[1,0],null,null,null,null,[1,0],[1,0],[1,1],[1,1]]
```

---

### Approach 1: Recursive Quad-Tree Construction 🧠

#### Key Idea:
The basic approach is to **recursively divide** the grid into four sub-grids and check if all elements in each sub-grid are the same:
1. If all the values in the current grid are the same, create a **leaf node**.
2. If they are not the same, create an **internal node** and divide the grid into four smaller grids.
3. Recursively build the Quad-Tree for each of the four sub-grids.

#### Python Code 🐍

```python
# Definition for a QuadTree node.
class Node:
    def __init__(self, val, isLeaf, topLeft=None, topRight=None, bottomLeft=None, bottomRight=None):
        self.val = val
        self.isLeaf = isLeaf
        self.topLeft = topLeft
        self.topRight = topRight
        self.bottomLeft = bottomLeft
        self.bottomRight = bottomRight

def construct(grid):
    def build(x1, y1, length):
        # Base case: If the grid is a leaf (all 0's or all 1's)
        val = grid[x1][y1]
        is_leaf = True
        
        for i in range(x1, x1 + length):
            for j in range(y1, y1 + length):
                if grid[i][j] != val:
                    is_leaf = False
                    break
            if not is_leaf:
                break
        
        if is_leaf:
            return Node(val == 1, True)
        
        # Recursive case: Divide the grid into 4 sub-grids
        half = length // 2
        topLeft = build(x1, y1, half)
        topRight = build(x1, y1 + half, half)
        bottomLeft = build(x1 + half, y1, half)
        bottomRight = build(x1 + half, y1 + half, half)
        
        return Node(True, False, topLeft, topRight, bottomLeft, bottomRight)
    
    return build(0, 0, len(grid))
```

### Walkthrough 🛣️

Let's dive deeper with a more complex example and represent the Quad-Tree graphically using Markdown to illustrate the structure of nodes and their connections.

### Complex Example

#### Input Grid:

```python
grid = [
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 1, 1, 1, 1],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0],
    [1, 1, 1, 1, 0, 0, 0, 0]
]
```

In this example, the grid has mixed values, so it will require several levels of recursion to construct the Quad-Tree. 

### Steps to Construct the Quad-Tree

1. **Check Entire Grid**: The grid has mixed values (both `0` and `1`), so it's not a leaf node. We'll divide it into 4 equal parts (top-left, top-right, bottom-left, bottom-right).

2. **Divide into 4 Sub-Grids**:
    - **Top-Left (4x4 grid)**:
      ```
      [
        [1, 1, 1, 1],
        [1, 1, 1, 1],
        [1, 1, 1, 1],
        [1, 1, 1, 1]
      ]
      ```
      All values are `1`, so this is a **leaf node** with `val = 1`.

    - **Top-Right (4x4 grid)**:
      ```
      [
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [1, 1, 1, 1],
        [1, 1, 1, 1]
      ]
      ```
      Mixed values, so it needs further division into 4 sub-grids:
      
        - **Top-Left**: All `0`, leaf node with `val = 0`.
        - **Top-Right**: All `0`, leaf node with `val = 0`.
        - **Bottom-Left**: All `1`, leaf node with `val = 1`.
        - **Bottom-Right**: All `1`, leaf node with `val = 1`.

    - **Bottom-Left (4x4 grid)**:
      ```
      [
        [1, 1, 1, 1],
        [1, 1, 1, 1],
        [1, 1, 1, 1],
        [1, 1, 1, 1]
      ]
      ```
      All values are `1`, so this is a **leaf node** with `val = 1`.

    - **Bottom-Right (4x4 grid)**:
      ```
      [
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0],
        [0, 0, 0, 0]
      ]
      ```
      All values are `0`, so this is a **leaf node** with `val = 0`.

---

### Quad-Tree Structure in Markdown

Here’s a graphical representation of the Quad-Tree constructed from this grid. Each node has either 4 children (if it's not a leaf) or no children (if it's a leaf).

#### Root Node:
```
       [Internal, 1]
        /   |   |   \
       /    |   |    \
      /     |   |     \
   [1]    [Internal]  [1]   [0]
         /   |   |   \
        /    |   |    \
       [0]  [0]  [1]  [1]
```

#### Explanation:

- The **Root Node** is an internal node because the grid contains both `0`'s and `1`'s.
- Its children represent the 4 quadrants of the grid:
  - The **Top-Left** quadrant is a leaf with all `1`'s.
  - The **Top-Right** quadrant is an internal node because it contains mixed values. Its children are:
    - **Top-Left and Top-Right** are leaves with all `0`'s.
    - **Bottom-Left and Bottom-Right** are leaves with all `1`'s.
  - The **Bottom-Left** quadrant is a leaf with all `1`'s.
  - The **Bottom-Right** quadrant is a leaf with all `0`'s.

---

### Python Code Representation

```python
# Node representation for the complex example
root = Node(val=True, isLeaf=False,
            topLeft=Node(val=True, isLeaf=True),  # All 1's
            topRight=Node(val=True, isLeaf=False,  # Mixed, internal node
                          topLeft=Node(val=False, isLeaf=True),  # All 0's
                          topRight=Node(val=False, isLeaf=True),  # All 0's
                          bottomLeft=Node(val=True, isLeaf=True),  # All 1's
                          bottomRight=Node(val=True, isLeaf=True)),  # All 1's
            bottomLeft=Node(val=True, isLeaf=True),  # All 1's
            bottomRight=Node(val=False, isLeaf=True))  # All 0's
```

This structure mirrors the graphical tree shown earlier.

### Time Complexity Analysis ⏱️

- **Time Complexity**: \(O(n^2 \log n)\), where \(n\) is the size of the grid. The reason is that in the worst case, the grid will need to be subdivided into smaller grids until each contains only one element, and for each subdivision, we inspect each element in the grid to see if it’s uniform.
- **Space Complexity**: \(O(n^2)\), since we create a node for each grid cell in the worst case.


### Conclusion 🏁

This problem gives us an opportunity to explore **Quad-Trees**, a useful data structure for efficiently representing two-dimensional space. The recursive approach breaks the problem down into manageable sub-problems and constructs the Quad-Tree by dividing the grid into smaller sections.

For small grids, this approach works well, but for very large grids, optimizations might be necessary to avoid excessive recursion.

