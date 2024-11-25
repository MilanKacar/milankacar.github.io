---
layout: post  
title: "#85 🧩 773. Sliding Puzzle 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Breadth-First Search, Matrix]
---

Welcome to a comprehensive deep dive into **LeetCode Problem #773: Sliding Puzzle**. This problem combines elements of graph traversal, logical deduction, and algorithmic problem-solving. It's a must-know challenge for any aspiring software engineer or coding enthusiast looking to level up their BFS skills. Buckle up as we unravel every detail of this exciting problem, with examples, code walkthroughs, and advanced insights to help you ace it! 💪

---

### 🌟 Problem Overview: Sliding Into the Challenge

The Sliding Puzzle is a **2x3 grid-based game**, where numbered tiles (1-5) and an empty square (0) need to be rearranged into a predefined solved configuration. Moves are made by sliding the empty square (`0`) to an adjacent position, swapping it with a numbered tile. 

The **solved state** of the puzzle is defined as:

```
[[1, 2, 3],
 [4, 5, 0]]
```

Your task is to determine the **minimum number of moves** required to reach this configuration. If the board cannot be solved (due to an unsolvable arrangement), return `-1`. Let’s break it down into smaller, digestible parts! 🍴

---

### 🎯 Key Concepts to Understand

1. **State-Space Graph**  
   Imagine every possible board configuration as a **node** in a graph, with edges connecting configurations that can be achieved by a single move. Your goal is to navigate this graph from the initial configuration to the solved state, using the shortest path.  

2. **Breadth-First Search (BFS)**  
   BFS is ideal for finding the shortest path in an unweighted graph. By exploring all possible states level by level, BFS ensures you find the minimum number of moves required to solve the puzzle.

3. **Serialization**  
   To efficiently represent the board and track visited states, the 2D grid is converted into a 1D string, such as `"412503"`. This simplifies state management and comparisons during traversal.

---

### 📝 Problem Statement (Expanded)

You are given a 2x3 grid where:
- The tiles are numbered from `1` to `5`.
- An empty square is represented as `0`.  
Your task is to determine the **minimum number of moves** required to transform the initial configuration into the solved state:

```
[[1, 2, 3],
 [4, 5, 0]]
```

---

### 🔍 How BFS Works in This Problem

The Sliding Puzzle is modeled as a **graph traversal problem**, where:
- **Nodes** = Unique board configurations.
- **Edges** = Valid moves of `0` (the empty square).

Here’s how BFS systematically explores the state space:
1. Start with the initial configuration.
2. Add it to a queue and mark it as visited.
3. Iteratively dequeue the first element, generate all valid configurations reachable in one move, and enqueue them.
4. Stop when the solved configuration is reached. If the queue is exhausted without finding it, return `-1`.

---

### 🚀 Step-by-Step Solution

#### 1️⃣ Serialization of the Board  
Represent the board as a **string** for easier manipulation. For example:  
```
[[4, 1, 2], [5, 0, 3]] → "412503"
```
This format allows for quick comparisons, swaps, and state tracking.

#### 2️⃣ Define Valid Moves  
Since the grid is fixed in size (2x3), the possible moves for `0` are predefined based on its position:  
```
Index 0: Moves to [1, 3]  
Index 1: Moves to [0, 2, 4]  
Index 2: Moves to [1, 5]  
Index 3: Moves to [0, 4]  
Index 4: Moves to [1, 3, 5]  
Index 5: Moves to [2, 4]
```
These mappings ensure efficient generation of new states.

#### 3️⃣ Breadth-First Search  
Using a queue, explore all possible configurations reachable from the initial state. Each level in the BFS tree corresponds to a single move.

---

### 🛠 Python Code (Detailed)

Here’s the Python implementation:

```python
from collections import deque

def slidingPuzzle(board):
    # Serialize the board into a string
    start = ''.join(str(num) for row in board for num in row)
    target = "123450"
    
    # Define valid moves for each index of '0'
    moves = {
        0: [1, 3], 1: [0, 2, 4], 2: [1, 5],
        3: [0, 4], 4: [1, 3, 5], 5: [2, 4]
    }
    
    # BFS setup
    queue = deque([(start, 0)])  # (current configuration, steps)
    visited = set([start])
    
    while queue:
        current, steps = queue.popleft()
        
        # Check if the target configuration is reached
        if current == target:
            return steps
        
        # Find the index of '0' (empty square)
        zero_index = current.index('0')
        
        # Generate all possible moves from the current configuration
        for move in moves[zero_index]:
            # Swap '0' with the adjacent tile
            new_board = list(current)
            new_board[zero_index], new_board[move] = new_board[move], new_board[zero_index]
            new_state = ''.join(new_board)
            
            # Add the new configuration to the queue if not visited
            if new_state not in visited:
                visited.add(new_state)
                queue.append((new_state, steps + 1))
    
    # If the target configuration is unreachable
    return -1
```

---

### 🔎 Example Walkthrough: `[[4, 1, 2], [5, 0, 3]]`

#### **Step 1: Initial State**
- Serialized board: `"412503"`  
- Moves taken: `0`  

#### **Step 2: Explore Moves**
Index of `0`: `4`  
Valid moves: `[1, 3, 5]`  

- Move `0` to index `1`: `"402513"`  
- Move `0` to index `3`: `"412053"`  
- Move `0` to index `5`: `"412530"`  

**Queue:**  
`[("402513", 1), ("412053", 1), ("412530", 1)]`  

---

#### **Step 3: Process First Element (`402513`)**
Index of `0`: `1`  
Valid moves: `[0, 2, 4]`  

- Move `0` to index `0`: `"042513"`  
- Move `0` to index `2`: `"420513"`  
- Move `0` to index `4`: `"412503"`  

**Queue:**  
`[("412053", 1), ("412530", 1), ("042513", 2), ("420513", 2)]`  

---

#### **Step 4: Continue Until Solved**
BFS guarantees the shortest solution. After processing all configurations level by level, the algorithm will eventually find the solved state: `"123450"`.  

**Final Moves Count:** `5`  

---

### 🚦 Complexity Analysis

- **Time Complexity:** `O(V + E)`  
  There are `6! = 720` possible configurations (nodes), with each having up to `4` neighbors (edges). Thus, the complexity is **O(720 × 4)**.

- **Space Complexity:** `O(V)`  
  Both the `visited` set and BFS queue require space proportional to the number of nodes.

---

### 🌟 Edge Cases

1. **Already Solved:**  
   Input: `[[1, 2, 3], [4, 5, 0]]`  
   Output: `0`  

2. **Impossible Configuration:**  
   Input: `[[1, 2, 3], [5, 4, 0]]`  
   Output: `-1`  

3. **Max Moves:**  
   Input: `[[4, 1, 2], [5, 0, 3]]`  
   Output: `5`  

---

### 🎉 Conclusion

The Sliding Puzzle is a classic problem that tests your ability to model and solve real-world scenarios using graph traversal techniques. By mastering BFS and its applications, you unlock the ability to tackle a wide range of problems efficiently. Keep practicing and happy coding! 🌟
