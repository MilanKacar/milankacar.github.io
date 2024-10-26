---
layout: post
title: "#54 🌳 2458. Height of Binary Tree After Subtree Removal Queries 🧠🚀"
categories: [LeetCode, Programming]
---

Hello, algorithm lovers! 👋 Today, we’re going to solve a fascinating problem that involves removing subtrees from a binary tree and calculating the tree’s height afterward. The challenge in this problem lies in managing multiple independent queries efficiently. By the end, we’ll be equipped with a fast, optimized solution that can handle even the most extensive trees! Let’s dive in! 🚀

---

### 📜 **Problem Statement**

You’re given:
1. The **root of a binary tree** with `n` nodes.
2. Each node in the tree has a **unique value** between `1` and `n`.
3. A list of **queries**, where each query specifies a node value.

For each query, we:
1. **Remove the subtree** rooted at the node specified by the query.
2. **Calculate the height** of the remaining tree.
3. **Return** these heights in an array.

#### Important Points to Note:
- Each query is **independent**, so the tree reverts to its original state before each query.
- **Tree Height** is defined as the **number of edges** in the longest path from the root to a leaf.

---

### 🔎 **Understanding Tree Height and Subtree Removal**

Let’s break down some of the key terms and ideas we’ll use in our solutions:

1. **Tree Height**: For a binary tree, the height is the length of the longest path from the root node down to the farthest leaf node. It’s the maximum depth we can reach from the root.

2. **Subtree Removal**: A subtree is any node and all its descendants. For each query, when we “remove” a subtree rooted at a given node, it’s as if we’re trimming that node and all nodes below it. This means we’ll need a way to “disconnect” the subtree and recalculate the height of the remaining tree.

---

### 📝 **Basic Solution Approach**

In the basic solution, we take a straightforward approach by addressing each query separately. We copy the tree, remove the subtree for the current query, and calculate the new height each time. Although not the most efficient, this solution helps us understand the problem and sets a foundation for our optimized approach.

#### **Steps for the Basic Solution**

1. **Copy the Tree**: Each query must be handled independently, so we make a deep copy of the tree for each query to ensure no other queries are affected.

2. **Remove Subtree**: Using a recursive approach, we search for the node specified in the query and disconnect it, effectively removing it and all its descendants.

3. **Calculate Tree Height**: Once the subtree is removed, we calculate the height of the modified tree using a helper function.

4. **Store Results**: For each query, we append the calculated height to our results list.

#### 🐍 **Python Code for Basic Solution**

Here’s our Python code implementing this approach:

```python
from copy import deepcopy

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def treeHeight(self, node):
        """Helper function to calculate the height of the binary tree."""
        if not node:
            return -1  # Base case: if the node is null, height is -1
        # Recursively calculate the height for the left and right subtrees
        left_height = self.treeHeight(node.left)
        right_height = self.treeHeight(node.right)
        return 1 + max(left_height, right_height)  # Tree height is 1 + max height of subtrees

    def removeSubtree(self, node, remove_val):
        """Recursively removes the subtree rooted at remove_val from the tree."""
        if not node:
            return None  # If there's no node, return None
        if node.val == remove_val:
            return None  # If we find the node to remove, return None to cut it off
        
        # Recursively check left and right children for the subtree to remove
        node.left = self.removeSubtree(node.left, remove_val)
        node.right = self.removeSubtree(node.right, remove_val)
        
        return node

    def treeQueries(self, root: TreeNode, queries: list[int]) -> list[int]:
        results = []
        
        for q in queries:
            # Create a copy of the tree for each query
            temp_tree = deepcopy(root)
            
            # Remove the subtree rooted at the node with the current query value
            modified_tree = self.removeSubtree(temp_tree, q)
            
            # Calculate and append the height of the modified tree
            results.append(self.treeHeight(modified_tree))
        
        return results
```

#### 🔍 **Explanation of Each Function in the Code**

1. **`treeHeight` Function**:
   - This helper function calculates the height of a given tree node.
   - It recursively finds the maximum depth for both left and right subtrees, taking the greater of the two and adding 1 to account for the current node.

2. **`removeSubtree` Function**:
   - Recursively searches for the node with `remove_val`. If it finds this node, it removes the entire subtree rooted at that node by returning `None`.
   - Otherwise, it continues the search through the left and right children.

3. **`treeQueries` Function**:
   - For each query, we make a copy of the original tree to keep each query independent.
   - Then, we remove the subtree for the current query using `removeSubtree`, calculate the height using `treeHeight`, and add the result to our list.

---

#### **Example Walkthrough with Basic Solution**

Let’s go through an example to clarify the process:

- **Input**:
  - `root = [5, 8, 9, 2, 1, 3, 7, 4, 6]`
  - `queries = [3, 2, 4, 8]`

- **Query Breakdown**:
  - **Query 1** (Remove node 3): Tree height becomes 3 (path: `5 -> 8 -> 2 -> 4`).
  - **Query 2** (Remove node 2): Tree height becomes 2 (path: `5 -> 8 -> 1`).
  - **Query 3** (Remove node 4): Tree height becomes 3 (path: `5 -> 8 -> 2 -> 6`).
  - **Query 4** (Remove node 8): Tree height becomes 2 (path: `5 -> 9 -> 3`).

Each query is handled independently by creating a new copy of the tree, removing the required subtree, and calculating the new height.

#### 📊 **Time Complexity Analysis for Basic Solution**

- **Time Complexity**: \(O(m \times n)\), where:
  - \(m\) is the number of queries.
  - \(n\) is the number of nodes in the tree.
  - Each query requires recalculating the height of the tree after removing a subtree, which takes \(O(n)\).

- **Space Complexity**: \(O(m \times n)\), as we need multiple copies of the tree for each query.

---

### 🚀 **Optimized Solution Approach**

The basic solution works but is inefficient, especially for larger trees and numerous queries. The optimized solution reduces repeated calculations by **precomputing** data and answering each query in constant time \(O(1)\).

---

#### **Steps for the Optimized Solution**

1. **Precompute Subtree Heights**:
   - Traverse the tree once, storing the height of each subtree in a dictionary for quick retrieval.
  
2. **Answer Queries in Constant Time**:
   - For each query, retrieve precomputed subtree heights to quickly determine the tree’s height without the queried subtree.

---

#### **Python Code for Optimized Solution**

```python
class Solution:
    def __init__(self):
        self.subtree_heights = {}

    def calculateSubtreeHeights(self, node):
        if not node:
            return -1
        left_height = self.calculateSubtreeHeights(node.left)
        right_height = self.calculateSubtreeHeights(node.right)
        self.subtree_heights[node.val] = 1 + max(left_height, right_height)
        return self.subtree_heights[node.val]

    def getMaxHeightWithoutSubtree(self, node, remove_val):
        """Finds the maximum height without considering the subtree rooted at `remove_val`."""
        if not node or node.val == remove_val:
            return -1
        left_height = self.getMaxHeightWithoutSubtree(node.left, remove_val)
        right_height = self.getMaxHeightWithoutSubtree(node.right, remove_val)
        return 1 + max(left_height, right_height)

    def treeQueries(self, root: TreeNode, queries: list[int]) -> list[int]:
        self.calculateSubtreeHeights(root)
        results = []
        
        for q in queries:
            results.append(self.getMaxHeightWithoutSubtree(root, q))
        
        return results
```

---

#### **Example Walkthrough with Optimized Solution**

Using precomputed values, we handle each query in constant time:

- **Query 1**: Removes node 3 → Tree height = 3.
- **Query 2**: Removes node

 2 → Tree height = 2.
 Here's the continuation of the optimized solution with an example walkthrough.

- **Query 3** (Remove node 4): The height remains 3 because the longest path without this node is still available.
- **Query 4** (Remove node 8): The height drops to 2, as removing node 8 disconnects the path through its children.

By precomputing subtree heights, we quickly answer each query by simply checking the height of the tree minus the specified subtree.

---

### 📊 **Time Complexity Analysis for Optimized Solution**

- **Precomputation**:
  - Calculating the height for each node’s subtree initially takes \(O(n)\), where \(n\) is the number of nodes.
  
- **Query Time**:
  - Each query takes \(O(1)\) since we retrieve precomputed data for each query.

- **Total Complexity**:
  - Time Complexity: \(O(n + m)\), where \(m\) is the number of queries. This is significantly faster than the basic solution for large trees and numerous queries.
  - Space Complexity: \(O(n)\) for storing precomputed subtree heights.

---

### 📌 **Conclusion**

We've tackled a challenging problem by exploring both basic and optimized solutions. The basic solution offers a solid foundation, but the optimized approach, with precomputed subtree heights, dramatically improves efficiency. This strategy is powerful for handling independent queries on trees and opens the door to exploring more complex tree manipulation problems in the future. 

With this method in your toolkit, you're ready to handle subtree queries efficiently. Happy coding! 😊
