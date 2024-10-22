---
layout: post
title: "#49 🔍🌳 1106. Parsing A Boolean Expression 🧠🚀"
categories: [LeetCode, Programming]
---

In this post, we'll tackle an exciting problem: finding the **Kth largest level sum** in a binary tree. 🌲 Each level of a binary tree contains nodes with specific values, and our goal is to compute the sum of these nodes for every level, then pick out the Kth largest sum. Whether you're into **BFS**, **DFS**, or both, you'll love diving deep into this one! 😎

---

### 📝 Problem Statement
You are given the root of a binary tree and a positive integer `k`. The **level sum** is defined as the sum of values of all nodes at the same level in the tree. The task is to find the **Kth largest level sum**. If there are fewer levels than `k`, return `-1`.

Two nodes are considered on the same level if they have the same distance from the root node.

#### Input:
- The root of a binary tree where the number of nodes is between `2 <= n <= 105`.
- Each node has a value between `1 <= Node.val <= 106`.
- A positive integer `k` where `1 <= k <= n`.

#### Output:
- Return the **Kth largest level sum** of the tree. If the tree contains fewer than `k` levels, return `-1`.

---

### 💡 Example 1:

**Input:**
```plaintext
root = [5, 8, 9, 2, 1, 3, 7, 4, 6], k = 2
```

**Output:**
```plaintext
13
```

**Explanation:**
The level sums are:
- Level 1: 5.
- Level 2: 8 + 9 = 17.
- Level 3: 2 + 1 + 3 + 7 = 13.
- Level 4: 4 + 6 = 10.

The **2nd largest level sum** is `13`.

### 💡 Example 2:

**Input:**
```plaintext
root = [1, 2, null, 3], k = 1
```

**Output:**
```plaintext
3
```

**Explanation:**
The only level sums are:
- Level 1: 1.
- Level 2: 2.
- Level 3: 3.

The **largest level sum** is `3`.

---

### 💻 Basic Solution — BFS Approach 🎒

To solve this problem, we need to compute the sum of all nodes at each level of the binary tree and then extract the **Kth largest sum**. A simple way to traverse a tree level-by-level is through **Breadth-First Search (BFS)**.

**Steps:**
1. Perform a **BFS** to traverse the tree level by level. As we traverse, calculate the sum of the nodes at each level.
2. Store all the sums in a list or priority queue (heap).
3. Sort the sums in **descending order** and return the Kth largest sum. If there are fewer than `k` levels, return `-1`.

#### 🧑‍💻 Python Code:
```python
from collections import deque

def kthLargestLevelSum(root, k):
    if not root:
        return -1
    
    # Use deque for BFS traversal
    queue = deque([root])
    level_sums = []
    
    # BFS to traverse each level
    while queue:
        level_sum = 0
        level_size = len(queue)
        
        # Sum the values of nodes at the current level
        for _ in range(level_size):
            node = queue.popleft()
            level_sum += node.val
            
            # Add the children of the current node to the queue
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        level_sums.append(level_sum)
    
    # Sort the sums in descending order and return the Kth largest sum
    level_sums.sort(reverse=True)
    
    return level_sums[k-1] if k <= len(level_sums) else -1
```

### ⏱️ Time Complexity of Basic Solution:
- **Time Complexity**: `O(n log n)` where `n` is the number of nodes. The BFS traversal takes `O(n)` time, and sorting the list of level sums takes `O(n log n)`.
- **Space Complexity**: `O(n)` for storing the sums of each level.

---

### ⚡ Optimized Solution — Max Heap Approach 🛠️

To avoid the costly sorting step, we can optimize this solution by using a **max heap**. The idea is to maintain the `k` largest level sums during the BFS traversal.

**Steps:**
1. Instead of collecting all level sums and sorting them, maintain a **heap** to store only the `k` largest sums.
2. Perform a BFS traversal, and for each level, if the heap contains fewer than `k` sums, simply add the current level sum.
3. Once the heap contains `k` sums, compare the current level sum to the smallest element in the heap. If the current sum is larger, remove the smallest element from the heap and insert the current sum.
4. If there are fewer than `k` levels, return `-1`.

#### 🧑‍💻 Python Code:
```python
import heapq
from collections import deque

def kthLargestLevelSum(root, k):
    if not root:
        return -1
    
    queue = deque([root])
    min_heap = []
    
    while queue:
        level_sum = 0
        level_size = len(queue)
        
        for _ in range(level_size):
            node = queue.popleft()
            level_sum += node.val
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        # Maintain a heap of the k largest sums
        heapq.heappush(min_heap, level_sum)
        
        if len(min_heap) > k:
            heapq.heappop(min_heap)
    
    return min_heap[0] if len(min_heap) == k else -1
```

### ⏱️ Time Complexity of Optimized Solution:
- **Time Complexity**: `O(n log k)` because we maintain a heap of size `k`. Inserting and deleting from the heap takes `O(log k)`, and we perform these operations `n` times for each level sum.
- **Space Complexity**: `O(k)` for the heap, which stores the top `k` largest sums.

---

### 🔍 Example Walkthrough with Higher Numbers 💪

Let’s walk through an example with larger numbers to see how both the basic and optimized solutions work.

#### Example Input:
```plaintext
root = [20, 15, 30, 10, 5, 25, 35, 1, 12], k = 3
```

This binary tree looks like this:

```
         20
       /    \
     15     30
    /  \   /   \
   10  5  25   35
  /  \
 1   12
```

Here are the sums of the levels:
- Level 1: `20`
- Level 2: `15 + 30 = 45`
- Level 3: `10 + 5 + 25 + 35 = 75`
- Level 4: `1 + 12 = 13`

The level sums are `[20, 45, 75, 13]`. The **3rd largest sum** is `20`.

#### Output:
```plaintext
20
```

---

### ✨ Conclusion

In this post, we explored how to solve the problem of finding the **Kth largest level sum** in a binary tree. We discussed two approaches:
- **Basic Solution** using **BFS** and sorting the level sums.
- **Optimized Solution** using a **max heap** to track only the largest sums.

We showed that the optimized approach is more efficient for large trees when `k` is small, as it avoids the need to sort all level sums. This solution gives a time complexity of `O(n log k)` rather than `O(n log n)`.

Now, you have the tools to tackle this problem in an interview or on your own coding journey! Happy coding! 😄🌲
