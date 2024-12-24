---
layout: post  
title: "#105 🌳 3203. Find Minimum Diameter After Merging Two Trees 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Tree, Breadth-First Search, Depth-First Tree, Graph]
---

Imagine you have two trees 🛠️ and need to merge them by connecting one node from each tree with a single edge. Your goal? Minimize the diameter of the resulting tree! 🌟  

This problem is not just about finding the longest path (diameter) but strategically combining two structures to optimize the result. Let’s explore the logic behind this fascinating problem step by step.

---

### 📜 Problem Statement  

You are given two trees represented as edge lists:  
- `edges1` for Tree 1  
- `edges2` for Tree 2  

Your task is to connect the two trees with a single edge and return the **minimum possible diameter** of the resulting tree.  

The diameter of a tree is the **longest path** between any two nodes.

---

### 🖋️ Examples  

#### Example 1:  
**Input:**  
```python
edges1 = [[0,1],[0,2],[0,3]]
edges2 = [[0,1]]
```

**Output:**  
`3`  

**Explanation:**  
Connecting node `0` from the first tree to any node in the second tree results in a tree with a diameter of `3`.

---

#### Example 2:  
**Input:**  
```python
edges1 = [[0,1],[0,2],[0,3],[2,4],[2,5],[3,6],[2,7]]
edges2 = [[0,1],[0,2],[0,3],[2,4],[2,5],[3,6],[2,7]]
```

**Output:**  
`5`  

**Explanation:**  
Connecting the centers of the diameters of both trees minimizes the resulting tree's diameter.

---

### 🧠 Intuition  

To solve this problem, we must focus on a tree's **diameter**, the longest path between any two nodes. When merging two trees, the resulting diameter is influenced by:  
1. The **diameter of Tree 1** (let's call it `d1`).  
2. The **diameter of Tree 2** (`d2`).  
3. The path formed by connecting the two trees.

The key insight is this:  
- The optimal nodes to connect are the **midpoints of the diameters** of both trees.  
- Midpoints minimize the new diameter because they balance the length of paths between the connected trees.

To compute this effectively, we leverage the **double DFS technique**:  
- First DFS: Find the farthest node from an arbitrary starting point.  
- Second DFS: Use the farthest node as a starting point to calculate the actual diameter.

This strategy ensures accuracy and efficiency, helping us merge the trees optimally.

---

Let’s take a deeper dive into the solution for minimizing the diameter of the merged trees. We'll break it into key steps, explain the intuition in detail, and visualize how it all works.

---

### **Step-by-Step Solution Walkthrough**

---

#### **1. Understanding the Tree Diameter**
The **diameter of a tree** is the longest path between any two nodes in the tree.  
To compute it:
- Perform a **DFS (Depth-First Search)** from any node to find the farthest node, say `farthest_node`.
- Perform another **DFS from `farthest_node`** to calculate the maximum distance from it. This distance is the tree's diameter.

**Why does this work?**
- The diameter always passes through the two farthest nodes of the tree.
- The two DFS steps ensure that we "trace out" the longest path efficiently.

#### Visualization:

Consider this tree:
```
    0
   /|\
  1 2 3
    |\
    4 5
```
1. Start DFS from node `0`:
   - Farthest node found is `4`.

2. Start DFS from node `4`:
   - Farthest node from `4` is `5`.  
   - The diameter is the path `4 -> 2 -> 0 -> 3` (length = 4).

---

#### **2. Connecting Two Trees**
When we connect two trees with an edge, the **new diameter** depends on three factors:
1. The diameter of Tree 1 (`d1`).
2. The diameter of Tree 2 (`d2`).
3. The path created by the new edge.

The path formed by the new edge includes:
- The "radius" of Tree 1: Half the diameter, rounded up ($$ \lceil d1 / 2 \rceil $$).
- The "radius" of Tree 2: Half the diameter, rounded up ($$ \lceil d2 / 2 \rceil $$).

**New Diameter Formula:**
$$
\text{New Diameter} = \max(d1, d2, \lceil d1 / 2 \rceil + \lceil d2 / 2 \rceil + 1)
$$

**Why this formula?**
- The first two terms ensure the new diameter accounts for the existing diameters of Tree 1 and Tree 2.
- The third term adds the contribution of the new edge, which connects the centers of the two trees.

#### Visualization:

Tree 1:
```
    0
   /|\
  1 2 3
    |\
    4 5
```
Tree 2:
```
    6
   /|\
  7 8 9
    |
   10
```
If we connect node `3` in Tree 1 to node `6` in Tree 2:
- $$ d1 = 4 $$, $$ d2 = 4 $$.
- New path: From the center of Tree 1 to the center of Tree 2 via the new edge.  
  New diameter = $$ \max(4, 4, \lceil 4 / 2 \rceil + \lceil 4 / 2 \rceil + 1) = 5 $$.

---

#### **3. Why Choose the Center?**
The center of a tree minimizes the "spread" of paths to other nodes:
- Nodes farther from the center contribute more to the tree's diameter.  
- Connecting two centers ensures the merged tree's diameter is minimized.

Consider this scenario:
- Connecting the **root of one tree to a leaf of the other** results in a longer path.  
- By connecting **midpoints (centers)**, we minimize the contribution of the new edge to the diameter.

---

### **Python Code Explained**
Let’s revisit the code with more comments and a deeper breakdown.

#### **Main Function**
```python
def minimumDiameterAfterMerge(self, edges1, edges2):
    # Compute the diameters of the two trees
    d1 = self.treeDiameter(edges1)
    d2 = self.treeDiameter(edges2)
    
    # Calculate the minimum possible diameter after merging
    # Consider the existing diameters and the new path formed by connecting centers
    return max(d1, d2, (d1 + 1) // 2 + (d2 + 1) // 2 + 1)
```

Here:
1. `d1` and `d2` are the diameters of the two trees.
2. `(d1 + 1) // 2` computes the radius of Tree 1. Adding 1 before division ensures we handle odd diameters correctly.
3. The `max` function ensures the final diameter considers all possible paths.

---

#### **Diameter Calculation**
```python
def treeDiameter(self, edges):
    # Helper function for Depth-First Search
    def dfs(node, parent, depth):
        # Traverse all neighbors of the current node
        for neighbor in graph[node]:
            if neighbor != parent:  # Avoid revisiting the parent
                dfs(neighbor, node, depth + 1)
        
        # Update the farthest node and maximum distance
        nonlocal max_distance, farthest_node
        if depth > max_distance:
            max_distance = depth
            farthest_node = node

    # Build the graph from the edge list
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        graph[v].append(u)

    # Perform the first DFS
    max_distance = 0
    farthest_node = 0
    dfs(0, -1, 0)

    # Perform the second DFS
    max_distance = 0
    dfs(farthest_node, -1, 0)

    # Return the diameter
    return max_distance
```

1. **Graph Construction:**  
   - We represent the tree as an adjacency list.  
   - Each edge connects two nodes bidirectionally.

2. **DFS Traversal:**  
   - We track the farthest node and its distance during each DFS.

3. **Two-Step Process:**  
   - The first DFS finds the farthest node from an arbitrary start.
   - The second DFS calculates the actual diameter starting from this farthest node.

---

### **Why This Works**
The approach is efficient because:
1. **Graph Representation:** Using adjacency lists allows us to traverse each node and edge only once.
2. **Two DFS Runs:** This guarantees the correct diameter without redundant checks.
3. **Optimal Center Connection:** By connecting midpoints, the new path length is minimized.

---


### 🏎️ Solution and Python Code  

Here’s the complete solution with comments:

```python
from collections import defaultdict
from typing import List

class Solution:
    def minimumDiameterAfterMerge(self, edges1: List[List[int]], edges2: List[List[int]]) -> int:
        # Step 1: Compute the diameter of both trees
        d1 = self.treeDiameter(edges1)
        d2 = self.treeDiameter(edges2)
        
        # Step 2: Calculate the minimum diameter after merging
        # Use the formula: max(d1, d2, (d1 + 1) // 2 + (d2 + 1) // 2 + 1)
        # The third term represents the new diameter formed by connecting midpoints
        return max(d1, d2, (d1 + 1) // 2 + (d2 + 1) // 2 + 1)

    def treeDiameter(self, edges: List[List[int]]) -> int:
        # Helper function to perform DFS and find the farthest node and its distance
        def dfs(node: int, parent: int, depth: int):
            for neighbor in graph[node]:
                if neighbor != parent:  # Avoid revisiting the parent node
                    dfs(neighbor, node, depth + 1)
            nonlocal max_distance, farthest_node
            # Update the farthest node and maximum distance found so far
            if depth > max_distance:
                max_distance = depth
                farthest_node = node

        # Step 1: Build the graph from the edge list
        graph = defaultdict(list)
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        # Step 2: Perform the first DFS to find the farthest node from an arbitrary starting point (node 0)
        max_distance = 0
        farthest_node = 0
        dfs(0, -1, 0)

        # Step 3: Perform the second DFS from the farthest node to calculate the diameter
        max_distance = 0
        dfs(farthest_node, -1, 0)

        # Step 4: Return the diameter
        return max_distance
```

---

### 🌟 Example Walkthrough  

#### Example Input:  
```python
edges1 = [[0,1],[0,2],[0,3],[2,4],[2,5],[3,6],[2,7]]
edges2 = [[0,1],[0,2],[0,3],[2,4],[2,5],[3,6],[2,7]]
```

#### Step-by-Step Execution:

1. **Compute the Diameter of Tree 1 (`d1`):**  
   - Perform DFS from node `0`. The farthest node found is `7`.  
   - Perform DFS from node `7`. The farthest node found is `3`.  
   - **Diameter of Tree 1 (`d1`) = 7.**

2. **Compute the Diameter of Tree 2 (`d2`):**  
   - Similarly, perform two DFS traversals.  
   - **Diameter of Tree 2 (`d2`) = 7.**

3. **Calculate Merged Diameter:**  
   - Connect the centers of both trees.  
   - **New Diameter:**  
     $$
     \text{max}(d1, d2, \frac{d1 + 1}{2} + \frac{d2 + 1}{2} + 1) = \text{max}(7, 7, 5) = 5
     $$

---

### 🔍 Edge Cases  

1. **Single-Node Trees:**  
   If one tree has only one node, the diameter of the merged tree equals the diameter of the other tree.  

2. **Highly Imbalanced Trees:**  
   For trees with very different sizes, the connecting edge may significantly affect the resulting diameter.

---

### ⏱️ Time Complexity  

- **Tree Diameter Calculation:**  
  Each DFS traversal takes $$O(N + M)$$, where $$N$$ and $$M$$ are the number of nodes in the respective trees.  
- **Overall Complexity:**  
  $$O(N + M)$$.

---

### ✨ Conclusion  

This problem is an excellent example of combining graph theory and optimization. By strategically selecting the connection points between two trees, we achieve the minimum diameter efficiently. Whether you're tackling coding interviews or sharpening your graph traversal skills, this problem is a must-try! 🚀
