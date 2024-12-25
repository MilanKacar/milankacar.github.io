---
layout: post  
title: "#106 🎄 515. Find Largest Value in Each Tree Row 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Tree, Depth-First Search, Breadth-First Search, Binary Tree]
---

### 🌟 Find the Largest Value in Each Tree Row 🌟

Binary trees are fascinating data structures, and problems like this one help us understand their traversal techniques while solving practical challenges. Let’s dive into this exciting problem and explore how to find the largest value in each row of a binary tree! 🚀

---

### 📝 Problem Statement

Given the root of a binary tree, return an array containing the largest value in each row of the tree (0-indexed).

---

### 💡 Examples

#### Example 1:

```plaintext
Input: root = [1,3,2,5,3,null,9]
Output: [1,3,9]
```

**Explanation:**

- Row 0: `[1]` → Largest value = 1  
- Row 1: `[3, 2]` → Largest value = 3  
- Row 2: `[5, 3, 9]` → Largest value = 9  

Final output: `[1, 3, 9]`

---

#### Example 2:

```plaintext
Input: root = [1,2,3]
Output: [1,3]
```

**Explanation:**

- Row 0: `[1]` → Largest value = 1  
- Row 1: `[2, 3]` → Largest value = 3  

Final output: `[1, 3]`

---

#### Example 3 (Edge Case):

```plaintext
Input: root = []
Output: []
```

**Explanation:**  
If the tree is empty, there are no rows, so the result is an empty list.

---

#### Constraints

- The number of nodes in the tree is in the range `[0, 10^4]`.  
- Each node’s value is within the range `[-2^31, 2^31 - 1]`.

---

### 🌟 Intuition Behind the Solution

The goal is to find the largest value in each row (level) of a binary tree. Understanding the structure of a binary tree and how to traverse it effectively is key to solving this problem.

#### 💡 Key Insights:
1. **Tree Levels:**  
   A binary tree consists of levels or rows, where each level contains nodes connected to their parent nodes in the previous level. To find the largest value in each row, we must process nodes level by level.

2. **Traversal Method:**  
   Traversal techniques for trees include **Depth-First Search (DFS)** and **Breadth-First Search (BFS)**. For this problem:
   - BFS is ideal because it naturally processes nodes level by level.  
   - DFS is also possible by tracking the depth of recursion, but it may require additional logic to maintain the maximum values for each level.

3. **Finding the Maximum:**  
   While processing a level, maintain a variable to track the maximum value seen so far. After completing the level, append this maximum to the result list.

4. **Queue for BFS:**  
   A queue is perfect for BFS because it allows for orderly processing of nodes level by level. Each node’s children are added to the queue, ensuring that nodes are processed in the correct order.

---

### 🌟 Edge Cases to Consider

1. **Empty Tree:**  
   Ensure the code handles an input where `root = None`.

2. **Single Node Tree:**  
   Verify that a tree with only one node returns its value as the largest in the only row.

3. **All Negative Values:**  
   Confirm the code works when all node values are negative, as the maximum would still need to be correctly identified.

4. **Unbalanced Tree:**  
   Test cases where the tree is unbalanced (e.g., all nodes are on the left or right).

---

### 🔍 Step-by-Step Intuition with BFS

Let’s break the process into clear steps:

1. **Initialize:**  
   - Start with the root node in a queue.  
   - If the root is `None`, return an empty list as there are no rows to process.  

2. **Level-by-Level Traversal:**  
   - Use a loop to process nodes level by level.  
   - For each level, determine the number of nodes (`len(queue)`) to process, ensuring all nodes in the level are handled before moving to the next level.

3. **Track Maximum Value:**  
   - During traversal of a level, update a variable (`mx`) to store the maximum value of the current level.  
   - After processing all nodes in the level, append `mx` to the result list.

4. **Handle Children:**  
   - For each node, if it has left or right children, add them to the queue to be processed in the subsequent levels.

5. **Return Result:**  
   - After processing all levels, return the result list containing the largest value for each row.

---

### 🛠️ BFS Solution with Full Explanation

Here’s the BFS implementation with detailed comments for better understanding:

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

from typing import Optional, List
import collections

class Solution:
    def largestValues(self, root: Optional[TreeNode]) -> List[int]:
        # If the tree is empty, return an empty list
        if not root:
            return []

        # Initialize an empty list to store the largest values
        ans = []

        # Use a queue to perform BFS
        q = collections.deque([root])

        # Process each level of the tree
        while q:
            # Initialize the maximum value for the current level
            mx = -float('inf')

            # Process all nodes in the current level
            for _ in range(len(q)):
                node = q.popleft()
                # Update the maximum value with the current node's value
                mx = max(mx, node.val)

                # Add children to the queue for the next level
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)

            # Append the maximum value of the current level to the result
            ans.append(mx)

        # Return the list of largest values
        return ans
```

---

### 🔑 Why BFS Works Well for This Problem

- **Row-by-Row Processing:**  
  BFS ensures that we process all nodes at a given depth (or row) before moving to the next depth. This aligns perfectly with the problem's requirement to find the largest value at each row.

- **Queue for Order:**  
  The queue maintains the order of nodes as we traverse. By adding children of a node to the queue, we ensure nodes are processed in the correct sequence for their respective rows.

---

### 🌀 Alternate Approach: DFS

While BFS is straightforward, a **Depth-First Search (DFS)** can also solve this problem. DFS involves recursively traversing the tree and keeping track of the depth (or row) of each node. At each depth, we update the maximum value seen so far.

#### DFS Intuition:
- Use recursion to traverse the tree.
- Pass the current depth as a parameter during recursion.
- Maintain a list (`ans`) where the index corresponds to the depth of the tree.
- If the current depth is already in the list, update it with the maximum value. Otherwise, append the value.

#### DFS Code:

```python
class Solution:
    def largestValues(self, root: Optional[TreeNode]) -> List[int]:
        def dfs(node, depth):
            if not node:
                return
            # Expand the list if this depth hasn't been encountered yet
            if depth == len(ans):
                ans.append(node.val)
            else:
                # Update the maximum value for this depth
                ans[depth] = max(ans[depth], node.val)
            # Recursively traverse left and right children
            dfs(node.left, depth + 1)
            dfs(node.right, depth + 1)

        # Initialize the list to store largest values
        ans = []
        dfs(root, 0)
        return ans
```


---

### 🌟 Visual Walkthrough of BFS with Large Values

Let’s visualize BFS traversal with a tree:

```plaintext
        50
       /  \
     20    90
    / \     \
   10  30   100
```

1. **Initialization:**  
   Queue: `[50]`  
   Result: `[]`

2. **Level 0:**  
   Queue: `[20, 90]` → Largest: `50`  
   Result: `[50]`

3. **Level 1:**  
   Queue: `[10, 30, 100]` → Largest: `90`  
   Result: `[50, 90]`

4. **Level 2:**  
   Queue: `[]` → Largest: `100`  
   Result: `[50, 90, 100]`

---

### 🌟 DFS Approach

#### Steps:
1. **Recursive Function:**
   - Define a recursive helper function that processes each node, taking the node, its depth, and the result list (`ans`) as parameters.

2. **Track Depth:**  
   - Use the depth of the recursion to determine the current level. This depth maps directly to the index in the result list.

3. **Expand Result List:**  
   - If the current depth exceeds the length of the result list, it means you've encountered a new level. Add the node's value as the initial maximum for this level.

4. **Update Maximum:**  
   - If the level already exists in the result list, compare the node's value with the current maximum for that level and update it if needed.

5. **Recur for Children:**  
   - Recursively call the helper function for the left and right children, incrementing the depth.

---

### 🛠️ Python Code for DFS

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

from typing import Optional, List

class Solution:
    def largestValues(self, root: Optional[TreeNode]) -> List[int]:
        # Helper function for DFS traversal
        def dfs(node, depth):
            if not node:
                return
            
            # If we encounter a new level, expand the result list
            if depth == len(ans):
                ans.append(node.val)
            else:
                # Update the maximum value for this level
                ans[depth] = max(ans[depth], node.val)
            
            # Recursively process left and right children
            dfs(node.left, depth + 1)
            dfs(node.right, depth + 1)
        
        # Result list to store the largest values
        ans = []
        # Start DFS from the root at depth 0
        dfs(root, 0)
        return ans
```

---

### 🌟 Intuition for DFS Solution

1. **Recursion Depth Equals Tree Level:**  
   The depth of the recursion corresponds to the level of the binary tree. By using the depth as the index, we map each node to its appropriate level in the result list.

2. **Dynamic List Expansion:**  
   The result list expands dynamically as we encounter new levels during traversal. If the depth matches the size of the list, it means this is the first node in a new level.

3. **Maximum Value Update:**  
   As recursion proceeds, the function compares the node’s value to the current maximum value for its level and updates it accordingly.

4. **Left-to-Right Traversal Order:**  
   DFS naturally processes left children before right children. This order does not affect the correctness of the solution because we are only concerned with finding the maximum value in each level.

---

### 🌟 Example Walkthrough

Consider the binary tree:

```plaintext
        10
       /  \
      20   30
     /    /  \
    40   50   60
```

#### Initial State:
- `ans = []`  
- Start DFS at root (value `10`) with depth `0`.

#### Recursive Steps:
1. **Root Node (`10` at depth `0`):**  
   - New level → Append `10` to `ans`.  
   - `ans = [10]`

2. **Left Child (`20` at depth `1`):**  
   - New level → Append `20` to `ans`.  
   - `ans = [10, 20]`

3. **Left Child (`40` at depth `2`):**  
   - New level → Append `40` to `ans`.  
   - `ans = [10, 20, 40]`

4. **Right Child of Root (`30` at depth `1`):**  
   - Update → Max(`20`, `30`) → `30`.  
   - `ans = [10, 30, 40]`

5. **Left Child (`50` at depth `2`):**  
   - Update → Max(`40`, `50`) → `50`.  
   - `ans = [10, 30, 50]`

6. **Right Child (`60` at depth `2`):**  
   - Update → Max(`50`, `60`) → `60`.  
   - `ans = [10, 30, 60]`

---

### ⏳ Time Complexity

- **Traversal:**  
  Each node is visited once, making the time complexity $$O(N)$$, where $$N$$ is the number of nodes in the tree.

- **Update Operations:**  
  Updating the result list for each node is $$O(1)$$.

- **Overall Complexity:**  
  $$O(N)$$, as traversal dominates.

---

### 🧠 Comparing BFS and DFS

| Feature                 | BFS                                   | DFS                                   |
|-------------------------|---------------------------------------|---------------------------------------|
| **Traversal Style**      | Level-by-level                       | Depth-first                          |
| **Data Structure**       | Queue                                | Recursion or Stack                   |
| **Use Case**             | Natural for level-order problems     | Flexible for problems requiring depth |
| **Space Complexity**     | $$O(W)$$, where $$W$$ is max width   | $$O(H)$$, where $$H$$ is max depth   |
| **Edge Cases**           | Handles large trees level by level   | Handles deep trees with recursion     |

---




### 🌟 When to Choose DFS Over BFS

- **Memory Constraints:**  
  DFS may use less memory than BFS for wide trees because BFS requires storing all nodes at a level in the queue.

- **Implementation Preference:**  
  DFS is more elegant and concise for recursive solutions. If you’re comfortable with recursion, DFS can simplify your code.

- **Specific Requirements:**  
  Some problems, like this one, can be solved equally well with either method. If you're already exploring nodes depth-first for some other purpose, DFS might be a natural choice.

---

### 🚀 Conclusion

This problem is a fantastic example of applying BFS or DFS techniques to solve real-world challenges with binary trees. Both approaches work efficiently, but BFS is generally more intuitive for level-based operations.  

Through examples, edge cases, and visual walkthroughs, we’ve covered everything you need to tackle this problem confidently! 😊
