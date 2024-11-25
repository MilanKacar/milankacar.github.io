---
layout: post  
title: "#86 🧩 773. Sliding Puzzle 🚀🧠"
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

### 🚀 Detailed Algorithm Explanation: The Sliding Puzzle Mastermind 🧠

To solve the Sliding Puzzle efficiently, we leverage **Breadth-First Search (BFS)**, which is particularly well-suited for finding the shortest path in an unweighted graph. In the context of this problem, the graph is conceptual rather than visual—each unique board configuration is a node, and a valid move between configurations represents an edge.

Let’s break down the algorithm step by step to understand its core mechanics and why BFS is the perfect choice here:

---

#### 🔹 Step 1: **Model the Problem as a State-Space Graph**

Imagine the sliding puzzle as a network of connected states:
- **Nodes**: Each node represents a unique configuration of the board.
- **Edges**: An edge connects two nodes if you can transform one board configuration into the other by sliding the empty square (`0`) into an adjacent position.

For example:
```
[[4, 1, 2], [5, 0, 3]] → [[4, 1, 2], [0, 5, 3]] 
```
The two configurations are connected because `0` swapped positions with `5`. The goal is to traverse this graph to find the shortest path from the initial configuration to the solved state.

---

#### 🔹 Step 2: **Serialize the Board**

Working directly with 2D arrays can be cumbersome when tracking states. Instead, we **serialize** the board into a string. For example:
```
[[4, 1, 2], [5, 0, 3]] → "412503"
```
Serialization simplifies:
1. **Comparisons**: Checking whether two configurations are equal becomes straightforward.
2. **State Tracking**: Adding configurations to a `visited` set ensures we don’t revisit nodes, preventing infinite loops.

---

#### 🔹 Step 3: **Define Valid Moves for `0`**

The empty square (`0`) can move only to adjacent positions within the 2x3 grid. Since the grid is small and fixed, we can predefine the valid moves based on the current index of `0` in the serialized string:
- **Index 0** (top-left corner): Can move to `[1, 3]`
- **Index 1** (top-middle): Can move to `[0, 2, 4]`
- **Index 2** (top-right corner): Can move to `[1, 5]`
- **Index 3** (bottom-left corner): Can move to `[0, 4]`
- **Index 4** (bottom-middle): Can move to `[1, 3, 5]`
- **Index 5** (bottom-right corner): Can move to `[2, 4]`

This mapping ensures we generate only valid configurations during the traversal. For example:
```
Current: "412503"
Position of 0: Index 4
Possible Moves: [1, 3, 5]
```

---

#### 🔹 Step 4: **Initialize BFS**

**Breadth-First Search** systematically explores all possible configurations level by level:
1. Start with the initial configuration. Add it to a queue along with the step count (`queue = deque([(start, 0)])`) and mark it as visited (`visited = set([start])`).
2. While the queue is not empty:
   - Dequeue the first configuration and check if it matches the target state.
   - If not, generate all valid configurations reachable in one move by swapping `0` with adjacent tiles.
   - Add each new configuration to the queue if it hasn’t been visited.
3. If the queue is exhausted without finding the target state, return `-1`.

---

#### 🔹 Step 5: **Generate New States**

To generate new configurations:
1. Identify the position of `0` in the serialized board (`zero_index = current.index('0')`).
2. For each valid move from the `zero_index`, create a new board configuration by swapping `0` with the target position. For example:
   ```
   Current: "412503"
   Swap positions 4 and 3 → "412053"
   ```
3. Serialize the updated configuration and check if it has been visited. If not, enqueue it and mark it as visited.

---

#### 🔹 Step 6: **Early Exit on Target State**

The BFS process ensures that the first time you encounter the solved state (`"123450"`), you’ve reached it in the minimum number of moves. This is because BFS explores all configurations at depth `n` before moving to depth `n+1`.

---

### 🔍 Why BFS Works Best for This Problem

BFS guarantees that we explore all configurations that require `k` moves before configurations that require `k+1` moves. This property ensures:
1. **Shortest Path**: When we first reach the solved state, we can confidently stop the search because no shorter path exists.
2. **Efficiency**: By pruning visited states, BFS avoids redundant calculations and keeps the solution space manageable.

---

### 🛠 Expanded Algorithm in Action

Let’s revisit the example board:
```
Input: [[4, 1, 2], [5, 0, 3]]
Serialized: "412503"
Target: "123450"
```

#### **Initialization**
- Queue: `[("412503", 0)]`
- Visited: `{"412503"}`

#### **Iteration 1**
- Dequeue: `"412503", 0`
- Index of `0`: `4`
- Valid Moves: `[1, 3, 5]`

**New States Generated**:
1. Swap 4 ↔ 1 → `"402513"`  
2. Swap 4 ↔ 3 → `"412053"`  
3. Swap 4 ↔ 5 → `"412530"`

**Queue**: `[("402513", 1), ("412053", 1), ("412530", 1)]`  
**Visited**: `{"412503", "402513", "412053", "412530"}`

---

#### **Iteration 2**
- Dequeue: `"402513", 1`
- Index of `0`: `1`
- Valid Moves: `[0, 2, 4]`

**New States Generated**:
1. Swap 1 ↔ 0 → `"042513"`
2. Swap 1 ↔ 2 → `"420513"`
3. Swap 1 ↔ 4 → `"412503"` (already visited)

**Queue**: `[("412053", 1), ("412530", 1), ("042513", 2), ("420513", 2)]`  
**Visited**: Updated accordingly.

---

#### **Continuing BFS**
The process continues level by level, systematically exploring all valid configurations until the solved state (`"123450"`) is reached. BFS ensures that the solution path is optimal, with the minimum number of moves.

---

### 🔑 Key Takeaways from the Algorithm

1. **Graph Representation**: Transforming the board into a state-space graph simplifies the problem.
2. **Serialization**: Efficiently tracks visited states and avoids redundant work.
3. **BFS Guarantees Optimality**: By exploring the solution space level by level, BFS finds the shortest path to the target state.

Understanding and implementing these steps provides a robust framework to solve similar graph traversal problems. Keep this algorithm in your toolkit—it’s a game-changer! 🌟

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
