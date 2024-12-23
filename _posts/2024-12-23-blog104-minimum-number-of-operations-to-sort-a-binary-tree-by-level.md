---
layout: post  
title: "#104 🌳 2471. Minimum Number of Operations to Sort a Binary Tree by Level 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Tree, Breadth-First Search, Binary Tree]
---

Sorting a binary tree by level values can be tricky, but with a few clever swaps, it's achievable! Let's dive into an exciting problem where we aim to minimize the number of operations required to make the values at each level sorted in increasing order. Along the way, we’ll explore a neat mix of tree traversal and array sorting techniques! 

---

#### Problem Statement 🌐

You are given the root of a binary tree with **unique values**. In one operation, you can **swap values of any two nodes** at the same level.

The goal is to **sort the values at each level in strictly increasing order** using the **minimum number of operations**. Return the minimum number of operations needed.

The number of edges between the node and the root defines each node's level.

---

#### Examples 🌟

**Example 1**:

```
Input: root = [1,4,3,7,6,8,5,null,null,null,null,9,null,10]
Output: 3

Explanation:
- Swap 4 and 3. The 2nd level becomes [3,4].
- Swap 7 and 5. The 3rd level becomes [5,6,8,7].
- Swap 8 and 7. The 3rd level becomes [5,6,7,8].

We used 3 operations, so the output is 3.
```

**Example 2**:

```
Input: root = [1,3,2,7,6,5,4]
Output: 3

Explanation:
- Swap 3 and 2. The 2nd level becomes [2,3].
- Swap 7 and 4. The 3rd level becomes [4,6,5,7].
- Swap 6 and 5. The 3rd level becomes [4,5,6,7].

We used 3 operations, so the output is 3.
```

**Example 3**:

```
Input: root = [1,2,3,4,5,6]
Output: 0

Explanation: Each level is already sorted, so no swaps are required.
```

---

#### Approach Overview 🎩

#### 1. Group Values by Levels 🌱
The first step involves traversing the binary tree level by level to group node values. This is done using a **Breadth-First Search (BFS)**. Here's how:

- Use a queue to traverse the tree, starting from the root.
- For each level, store the values of all nodes in a list.
- Repeat this process until all levels are processed.

**Why BFS?**  
BFS ensures that nodes are visited level by level, making it the ideal choice for grouping values by levels in a tree.

**Example Walkthrough**  
For the tree `root = [1, 4, 3, 7, 6, 8, 5]`:

- Level 0: `[1]`
- Level 1: `[4, 3]`
- Level 2: `[7, 6, 8, 5]`

---

#### 2. Sort Each Level's Values 🌟
For each level, the goal is to sort the values in **increasing order**. However, instead of directly swapping elements, we focus on identifying **how many swaps are required** to transform the array into its sorted form.

---

#### 3. Minimum Swaps to Sort an Array 🔄
To calculate the minimum swaps needed:

1. Create a mapping of the **value to its index** in the sorted array.
   - This tells us where each element should ideally be.
2. Use a **visited array** to track which elements have already been placed correctly.
3. Iterate through the array:
   - If an element is already in its correct position or has been visited, skip it.
   - Otherwise, calculate the size of the **cycle** it forms.
4. Each cycle of size $$k$$ requires $$(k - 1)$$ swaps to resolve.

**Why Focus on Cycles?**  
Cycles represent the minimal set of swaps needed to move elements into their correct positions without unnecessary operations.

---

#### Example: Minimum Swaps for `[7, 6, 8, 5]`
- **Sorted Array**: `[5, 6, 7, 8]`
- **Mapping**: `{5: 0, 6: 1, 7: 2, 8: 3}`
- Cycles:
  - $$7 → 5 → 7$$: Cycle of size 2 → 1 swap.
  - $$8 → 7 → 8$$: Cycle of size 2 → 1 swap.
- **Total Swaps**: $$1 + 1 = 2$$.

---

#### 4. Sum Up Swaps 🌐
After calculating the swaps required for each level, sum them up to get the total number of operations.


---

#### Basic Solution 🎨

##### Steps:
1. Perform a BFS to collect node values grouped by their levels.
2. Sort each level’s array and count the number of swaps required.
3. Return the total swap count.

##### Python Code:

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def minimumOperations(self, root: Optional[TreeNode]) -> int:
        from collections import deque

        def find_min_swaps_to_sort(arr):
            # Create a map of value -> index for sorted array
            sorted_indices = {val: idx for idx, val in enumerate(sorted(arr))}
            visited = [False] * len(arr)
            swaps = 0

            for i in range(len(arr)):
                if visited[i] or sorted_indices[arr[i]] == i:
                    continue

                cycle_size = 0
                x = i

                while not visited[x]:
                    visited[x] = True
                    x = sorted_indices[arr[x]]
                    cycle_size += 1

                swaps += (cycle_size - 1)

            return swaps

        queue = deque([root])
        total_swaps = 0

        while queue:
            level = []
            for _ in range(len(queue)):
                node = queue.popleft()
                level.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            total_swaps += find_min_swaps_to_sort(level)

        return total_swaps
```

##### Time Complexity 🕰:
- **BFS Traversal**: $$O(n)$$, where $$n$$ is the total number of nodes.
- **Sorting Each Level**: $$O(k \log k)$$ per level, where $$k$$ is the number of nodes at that level.
- Total Complexity: $$O(n + L \cdot k \log k)$$, where $$L$$ is the number of levels.

##### Space Complexity 🕰:
- $$O(n)$$ for the queue and level arrays.

---

#### Optimized Solution 🔄
The basic solution already incorporates key optimizations, such as BFS for level grouping and cycle detection for minimum swaps. Hence, there isn't a significant improvement for time complexity beyond the current approach.

---

#### Example Walkthrough 🔍

Let’s go through **Example 1** in detail:

**Input**: `root = [1,4,3,7,6,8,5,null,null,null,null,9,null,10]`

1. **Level 0**: `[1]`
   - Already sorted. No swaps required.

2. **Level 1**: `[4, 3]`
   - Sorted array: `[3, 4]`
   - Cycles:
     - $$4 → 3 → 4$$: 1 swap.
   - Swaps required: 1.

3. **Level 2**: `[7, 6, 8, 5]`
   - Sorted array: `[5, 6, 7, 8]`
   - Cycles:
     - $$7 → 5 → 7$$: 1 swap.
     - $$8 → 7 → 8$$: 1 swap.
   - Swaps required: 2.

**Total Swaps**: $$1 + 2 = 3$$

---

#### Edge Cases 🔎
1. **Single Node**: 
   - Input: `[1]`
   - Output: `0` (no swaps required).

2. **Already Sorted Tree**:
   - Input: `[1, 2, 3, 4, 5]`
   - Output: `0` (no swaps required).

3. **Complete Binary Tree with Random Values**:
   - Larger inputs to test efficiency.

---

#### Conclusion 📝
In this problem, we combined **tree traversal** and **array sorting** techniques to achieve the minimum swaps for level-wise sorting. By focusing on cycles in permutations, we reduced unnecessary operations and ensured efficiency. 

This problem emphasizes the importance of breaking down complex operations into smaller, independent tasks—a valuable skill for any software engineer! 🚀

