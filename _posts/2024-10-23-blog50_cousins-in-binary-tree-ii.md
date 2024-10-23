---
layout: post
title: "#50 🔍🌳 2641. Cousins in Binary Tree II 🧠🚀"
categories: [LeetCode, Programming]
---


In this problem, we’re tasked with modifying a binary tree 🌲 by replacing the value of each node with the sum of all its cousins' values. Two nodes are considered cousins if they are at the same level but have different parents. This problem requires us to explore tree traversal techniques and depth-based operations, making it an exciting challenge for those familiar with binary trees and recursive algorithms! 😎✨

---

### Problem Statement 📝

Given the root of a binary tree, we must replace the value of each node with the sum of all its cousins' values. Cousins in a binary tree are nodes that:
1. Are located at the same depth (i.e., have the same distance from the root).
2. Have different parent nodes.

Our task is to return the modified tree, where every node's value is the sum of its cousins.

**Key Terms**:
- **Binary Tree**: A tree structure in which each node has at most two children (left and right).
- **Depth**: The number of edges on the path from the root node to a given node. For example, the root node has a depth of 0.
- **Cousins**: Nodes that share the same depth but are not siblings (do not have the same parent).

---

### Example Walkthroughs 🧩

#### Example 1:

```plaintext
Input: root = [5,4,9,1,10,null,7]
Output: [0,0,0,7,7,null,11]
```

#### Explanation:

Here’s the binary tree structure before modification:

```plaintext
        5
       / \
      4   9
     / \    \
    1  10   7
```

Let's break down the cousin relationships:
- Node `5` is at the root, so it has no cousins. Thus, its value is replaced with `0`.
- Nodes `4` and `9` are at depth 1 but have no cousins, so their values are replaced with `0`.
- Node `1` (depth 2) has a cousin `7`, so its value becomes `7`.
- Node `10` (depth 2) also has a cousin `7`, so its value becomes `7`.
- Node `7` (depth 2) has cousins `1` and `10`, so its value becomes `1 + 10 = 11`.

The tree after modification looks like this:

```plaintext
        0
       / \
      0   0
     / \    \
    7   7   11
```

#### Example 2:

```plaintext
Input: root = [3,1,2]
Output: [0,0,0]
```

Explanation:
- Node `3` is the root, so its value becomes `0`.
- Nodes `1` and `2` have no cousins, so their values become `0`.

The tree after modification:

```plaintext
      0
     / \
    0   0
```

---

### Tree Traversal and Cousin Relationships 🌳🤔

To understand this problem, it’s crucial to revisit the concept of **binary tree traversal**. A **binary tree** is a hierarchical structure in which each node has a value, and each node may have up to two children: a left and a right child. To solve this problem, we need to traverse the tree in two passes, using **Depth-First Search (DFS)**.

### Depth-First Search (DFS) 🌲🔍

DFS is a traversal technique where we explore as far as possible along each branch before backtracking. In this problem, we’ll use DFS to accomplish two things:
1. **First pass**: Calculate the sum of node values at each depth.
2. **Second pass**: For each node, update its value based on the sum of its cousins, which can be derived from the depth-level sum minus the values of the node itself and its sibling.

---

### Step-by-Step Breakdown of the Solution 🚶‍♂️🔧

#### Basic Solution Approach 💡

##### Step 1: Calculate the Sum of Nodes at Each Depth

We begin by traversing the tree and recording the sum of node values at each depth (or level). For instance, in the tree:

```plaintext
        5
       / \
      4   9
     / \    \
    1  10   7
```

- At **depth 0**, the sum is `5`.
- At **depth 1**, the sum is `4 + 9 = 13`.
- At **depth 2**, the sum is `1 + 10 + 7 = 18`.

We can store these sums in a dictionary where the key is the depth and the value is the sum at that depth.

##### Step 2: Replace Node Values with Cousin Sums

Next, we perform a second traversal of the tree. For each node, we calculate the sum of its cousins by subtracting its value and its sibling’s value (if any) from the sum of values at its depth.

For example, in the tree:

```plaintext
        5
       / \
      4   9
     / \    \
    1  10   7
```

For node `1`:
- The sum of all nodes at depth 2 is `18`.
- Node `1`'s sibling is `10`, so the sum of node `1`'s cousins is `18 - 1 - 10 = 7`.

For node `7`:
- The sum of all nodes at depth 2 is `18`.
- Node `7`'s cousins are `1` and `10`, so its new value is `1 + 10 = 11`.

---

### Full Python Solution 🐍

Let's implement the solution using Python:

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def replaceValueInTree(root: TreeNode) -> TreeNode:
    if not root:
        return None

    # Step 1: DFS to calculate level sums
    level_sums = {}
    
    def dfs_sum(node, depth):
        if not node:
            return
        if depth not in level_sums:
            level_sums[depth] = 0
        level_sums[depth] += node.val
        dfs_sum(node.left, depth + 1)
        dfs_sum(node.right, depth + 1)
    
    dfs_sum(root, 0)

    # Step 2: DFS to replace values with cousin sums
    def dfs_replace(node, depth, sibling_val):
        if not node:
            return
        # Cousin sum = total sum of the level - value of this node - sibling value
        node.val = level_sums[depth] - node.val - sibling_val
        left_val = node.left.val if node.left else 0
        right_val = node.right.val if node.right else 0
        dfs_replace(node.left, depth + 1, right_val)
        dfs_replace(node.right, depth + 1, left_val)
    
    dfs_replace(root, 0, 0)
    
    return root
```

### Walkthrough Example 🌟

Let's walk through a larger example to understand the solution in action.

#### Input:

```plaintext
        8
       / \
      3   10
     / \    \
    1   6   14
       / \   /
      4   7 13
```

#### Step 1: Calculate Level Sums

- **Level 0**: Sum = `8`
- **Level 1**: Sum = `3 + 10 = 13`
- **Level 2**: Sum = `1 + 6 + 14 = 21`
- **Level 3**: Sum = `4 + 7 + 13 = 24`

#### Step 2: Replace Values with Cousin Sums

- **Level 0**: The root node `8` has no cousins, so its value becomes `0`.
- **Level 1**: 
  - Node `3` has no cousins, so its value becomes `0`.
  - Node `10` has no cousins, so its value becomes `0`.
- **Level 2**: 
  - Node `1` has cousins `6` and `14`, so its value becomes `6 + 14 = 20`.
  - Node `6` has cousins `1` and `14`, so its value becomes `1 + 14 = 15`.
  - Node `14` has cousins `1` and `6`, so its value becomes `1 + 6 = 7`.
- **Level 3**: 
  - Node `4` has cousins `7` and `13`, so its value becomes `7 + 13 = 20`.
  - Node `7` has cousins `4` and `13`, so its value becomes `4 + 13 = 17`.
  - Node `13` has cousins `4` and `7`, so its value becomes `4 + 7 = 11`.

#### Final Output Tree:

```plaintext
        0
      

 / \
      0   0
     / \    \
    20  15   7
       / \   /
      20  17 11
```

---

### Time Complexity Analysis ⏳

#### Basic Solution
The time complexity for this approach is **O(N)**, where `N` is the number of nodes in the tree. This is because:
1. The first DFS pass traverses the entire tree to calculate the level sums.
2. The second DFS pass updates the node values based on the cousin sums, traversing the entire tree again.

#### Optimized Solution
This solution is already optimal in terms of time complexity. We traverse the tree twice in a depth-first manner, which ensures we visit each node in **O(N)** time.

---

### Conclusion 🎯

The **Cousins in Binary Tree II** problem offers a great opportunity to work with tree traversal techniques like DFS and understand how to manipulate tree structures based on relative node positions. By replacing node values with the sum of their cousins, we explore dynamic relationships in the tree. This is a solid exercise for anyone looking to deepen their understanding of binary trees! 🌟🌲
