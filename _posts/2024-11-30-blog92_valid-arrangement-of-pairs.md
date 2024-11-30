---
layout: post  
title: "#92 2097. Valid Arrangement of Pairs 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Breadth-First Search, Graph, Heap (Priority Queue), Matrix, Shortest Path]
---

Imagine arranging puzzle pieces 🔄, where each piece has a start 🟩 and an end 🟥. The challenge is to arrange them so the end of one piece perfectly matches the start of the next. Let's dive in and untangle this challenge using Eulerian paths in graph theory! 🚀

---

## 📝 **Problem Statement**  
You are given a 2D integer array `pairs`, where each pair is represented as `[start, end]`.  

A **valid arrangement** of pairs satisfies the following condition:  
For every index $$ i $$ (where $$ 1 \leq i < \text{pairs.length} $$), the **end** of the pair at index $$ i-1 $$ equals the **start** of the pair at index $$ i $$.

### **Task**  
Return any valid arrangement of the given pairs.

### **Key Points**  
- The input guarantees that a valid arrangement exists.  
- There may be multiple valid solutions, and you can return any of them.

---

## 💡 **Examples**  

### **Example 1**  
#### Input:  
```plaintext
pairs = [[5,1],[4,5],[11,9],[9,4]]
```
#### Output:  
```plaintext
[[11,9],[9,4],[4,5],[5,1]]
```
#### Explanation:  
- The arrangement ensures the **end** of each pair equals the **start** of the next.  
  - $$ 11 \to 9 \to 4 \to 5 \to 1 $$.

---

### **Example 2**  
#### Input:  
```plaintext
pairs = [[1,3],[3,2],[2,1]]
```
#### Output:  
```plaintext
[[1,3],[3,2],[2,1]]
```
#### Explanation:  
- A valid arrangement since $$ 1 \to 3 \to 2 \to 1 $$.  
- Other valid arrangements include:  
  - $$ [3,2],[2,1],[1,3] $$  
  - $$ [2,1],[1,3],[3,2] $$.

---

### **Example 3**  
#### Input:  
```plaintext
pairs = [[1,2],[1,3],[2,1]]
```
#### Output:  
```plaintext
[[1,2],[2,1],[1,3]]
```
#### Explanation:  
- A valid arrangement exists: $$ 1 \to 2 \to 1 \to 3 $$.

---

## 🔍 **Observations and Insights**  

This problem can be modeled as a **graph problem**:  
1. Each number is a **node** 🟢.  
2. Each pair is a **directed edge** 🔄 between two nodes.  

The goal is to find an **Eulerian path**, a path that visits every edge exactly once in a directed graph.

---

Certainly! Let’s delve deeper into the **approach to solving the problem** to provide a thorough understanding of the steps involved. This will help reinforce the concepts and ensure clarity for readers.

---

## 🚀 **Approach to the Solution**

### 1️⃣ **Understanding the Problem**  

At its core, the problem requires arranging pairs such that the **end** value of one pair matches the **start** value of the next.  
- Each pair can be visualized as a **directed edge** in a graph.  
- The solution then becomes finding a sequence of edges (pairs) that forms a path through the graph.

This insight transforms the problem into finding an **Eulerian path**, a classic graph-theory challenge. But first, let’s clarify the concepts behind an Eulerian path and how it relates to the problem.

---

### 2️⃣ **Eulerian Path Basics**  

An **Eulerian path** is a path in a graph that visits every edge exactly once. For a directed graph, an Eulerian path exists if:  
1. At most one vertex (node) has an **out-degree** that exceeds its **in-degree** by 1. This is the **start node**.  
2. At most one vertex has an **in-degree** that exceeds its **out-degree** by 1. This is the **end node**.  
3. All other vertices have equal in-degree and out-degree.  

#### Key Takeaway:  
- If these conditions are satisfied, the graph contains an Eulerian path.  
- The problem guarantees a valid arrangement exists, so the above conditions are inherently met.

---

### 3️⃣ **Building the Graph**

The first step is to represent the input pairs as a directed graph:  
- Each unique number in the pairs becomes a **node**.  
- Each pair $$[start, end]$$ forms a **directed edge** from `start` to `end`.  

#### Additional Details:
- Use an adjacency list to represent the graph.  
- Track **in-degrees** and **out-degrees** of each node to identify the start and end nodes for the Eulerian path.

#### Example:
For $$ pairs = [[5,1],[4,5],[11,9],[9,4]] $$:
- Adjacency List:  
  $$
  \text{Graph: } \{11: [9], 9: [4], 4: [5], 5: [1]\}
  $$
- In-Degree:  
  $$
  \{1: 1, 4: 1, 5: 1, 9: 1\}
  $$
- Out-Degree:  
  $$
  \{11: 1, 9: 1, 4: 1, 5: 1\}
  $$

---

### 4️⃣ **Finding the Start Node**

After constructing the graph, determine the **start node** for the Eulerian path:  
- Iterate over all nodes in the graph.  
- A node where **out-degree > in-degree** by 1 becomes the start node.  

If no such node exists, the graph is an **Eulerian circuit**, and any node can serve as the start.

#### Example:
For $$ pairs = [[5,1],[4,5],[11,9],[9,4]] $$:  
- All nodes have balanced degrees, so the graph forms an Eulerian circuit. We can start from any node.  

---

### 5️⃣ **Constructing the Path (Hierholzer’s Algorithm)**

With the start node identified, use **Hierholzer’s Algorithm** to find the Eulerian path:  
- Start from the identified node.  
- Perform a Depth-First Search (DFS), traversing edges one by one and removing them as you go.  
- Backtrack when you reach a dead end and append nodes to the path.  

#### Why Hierholzer’s Algorithm?  
- It efficiently constructs an Eulerian path by ensuring every edge is visited exactly once.  
- The algorithm runs in $$ O(E) $$, where $$ E $$ is the number of edges.  

---

### 6️⃣ **Reversing the Path**

Since Hierholzer’s Algorithm appends nodes in reverse order (from dead ends to the starting node):  
- Reverse the path at the end to get the correct sequence.  

---

### 7️⃣ **Transforming the Path into Pairs**

The final step is to convert the list of nodes into pairs.  
- Iterate through consecutive nodes in the path and form pairs:  
  $$
  \text{Result: } [[\text{path}[i], \text{path}[i+1]] \, \forall i \, \text{in } (0, \text{len(path)}-1)]
  $$

---

### 🔍 **Detailed Example Walkthrough**

Let’s revisit an example to apply these steps.

#### Input:
```plaintext
pairs = [[11,9],[9,4],[4,5],[5,1]]
```

---

#### Step 1: Build the Graph
- Adjacency List:  
  $$
  \text{Graph: } \{11: [9], 9: [4], 4: [5], 5: [1]\}
  $$
- In-Degree:  
  $$
  \{1: 1, 4: 1, 5: 1, 9: 1\}
  $$
- Out-Degree:  
  $$
  \{11: 1, 9: 1, 4: 1, 5: 1\}
  $$

---

#### Step 2: Identify Start Node  
- All nodes have balanced degrees, so start from $$ 11 $$.

---

#### Step 3: Hierholzer’s Algorithm  
1. Stack: $$[11]$$  
2. Visit $$ 9 $$: Stack becomes $$[11, 9]$$.  
3. Visit $$ 4 $$: Stack becomes $$[11, 9, 4]$$.  
4. Visit $$ 5 $$: Stack becomes $$[11, 9, 4, 5]$$.  
5. Visit $$ 1 $$: Stack becomes $$[11, 9, 4, 5, 1]$$.  
6. Backtrack: Path becomes $$[1, 5, 4, 9, 11]$$.

---

#### Step 4: Reverse the Path  
$$
\text{Path: } [11, 9, 4, 5, 1]
$$

---

#### Step 5: Convert to Pairs  
$$
\text{Result: } [[11, 9], [9, 4], [4, 5], [5, 1]]
$$

---

### 🔑 **Key Insights for Optimization**

1. **Adjacency List Efficiency**:  
   - Use a deque for adjacency lists to ensure $$ O(1) $$ complexity for popping edges during traversal.  

2. **Degree Calculation**:  
   - Use a single loop to compute in-degrees and out-degrees while building the graph.  

3. **Hierholzer's Algorithm**:  
   - Efficiently processes all edges in linear time.

---

## 🛠️ **Solution**  

### 🔹 Basic Solution  
#### **Approach**  
The basic solution focuses on:  
1. Building a graph.  
2. Performing a depth-first traversal to reconstruct the valid arrangement.

---

### **Python Code**  

### 🛠️ **Python Implementation in Detail**

We will now dive deep into the Python implementation of the solution, covering all steps thoroughly. Each part of the implementation will be explained with its purpose and how it connects to the overall logic of finding a valid arrangement of pairs.

---

### **Step 1: Building the Graph**

The input pairs represent edges in a directed graph. Using a **defaultdict** from Python's `collections` module, we can efficiently store adjacency lists and keep track of node degrees.  
- **Graph Representation:** Each node is a key in the dictionary, and its value is a deque (double-ended queue) that stores its outgoing edges (end nodes).
- **In-Degree and Out-Degree:** These dictionaries store the count of incoming and outgoing edges for each node, which will help us determine the starting node for the Eulerian path.

#### Code:
```python
from collections import defaultdict, deque

def validArrangement(pairs):
    # Step 1: Build graph and calculate degrees
    graph = defaultdict(deque)
    in_degree = defaultdict(int)
    out_degree = defaultdict(int)
    
    for start, end in pairs:
        graph[start].append(end)  # Add edge to the graph
        out_degree[start] += 1    # Increment out-degree for the start node
        in_degree[end] += 1       # Increment in-degree for the end node
```

#### Explanation:
- **`graph[start].append(end)`**: Appends the end node to the adjacency list of the start node.  
- **`out_degree[start]`** and **`in_degree[end]`**: Update the respective degrees for each edge.

---

### **Step 2: Finding the Start Node**

To find the starting node of the Eulerian path:
1. **Eulerian Path Conditions:** A node with `out-degree > in-degree` is a natural candidate for the starting node.
2. If no such node exists, any node from the graph can be chosen as the starting point.

#### Code:
```python
    # Step 2: Find the starting node for Eulerian path
    start = pairs[0][0]  # Default start node
    for node in graph:
        if out_degree[node] > in_degree[node]:
            start = node
            break
```

#### Explanation:
- We default to the first node in the input pairs (`pairs[0][0]`), but we check for a node with an excess outgoing degree.

---

### **Step 3: Hierholzer's Algorithm**

To construct the Eulerian path:
1. Use a **stack** to keep track of the current traversal path.
2. Use a **while loop** to iteratively explore the graph, visiting nodes and removing edges from the adjacency list (graph).
3. Backtrack when no further edges can be visited from the current node, adding nodes to the path in reverse order.

#### Code:
```python
    # Step 3: Hierholzer's Algorithm for Eulerian path
    path = []  # This will store the final path
    stack = [start]  # Start from the determined node

    while stack:
        # Traverse until we find a node with no outgoing edges
        while graph[stack[-1]]:
            next_node = graph[stack[-1]].popleft()  # Remove the next edge
            stack.append(next_node)  # Add the next node to the stack
        # Backtrack and add the node to the path
        path.append(stack.pop())
```

#### Explanation:
- **`stack[-1]`**: The last node in the stack is the current node being explored.
- **`popleft()`**: Efficiently removes the next edge from the deque.
- **`stack.pop()`**: Backtracks by removing the current node from the stack and appending it to the final path.

---

### **Step 4: Reversing and Converting to Pairs**

After constructing the path in reverse order:
1. Reverse the path to get the correct order.
2. Transform the path into the required output format, a list of pairs.

#### Code:
```python
    # Reverse the path to get the correct order
    path.reverse()

    # Convert path into pairs
    result = [[path[i], path[i + 1]] for i in range(len(path) - 1)]
    return result
```

#### Explanation:
- **`path.reverse()`**: The path is built in reverse order during traversal; reversing it gives the correct sequence.
- **`[[path[i], path[i + 1]] ...]`**: Converts the node sequence into edge pairs.

---

### **Full Python Code**
Here is the complete code, with all the steps integrated:

```python
from collections import defaultdict, deque

def validArrangement(pairs):
    # Step 1: Build graph and calculate degrees
    graph = defaultdict(deque)
    in_degree = defaultdict(int)
    out_degree = defaultdict(int)
    
    for start, end in pairs:
        graph[start].append(end)  # Add edge to the graph
        out_degree[start] += 1    # Increment out-degree for the start node
        in_degree[end] += 1       # Increment in-degree for the end node

    # Step 2: Find the starting node for Eulerian path
    start = pairs[0][0]  # Default start node
    for node in graph:
        if out_degree[node] > in_degree[node]:
            start = node
            break

    # Step 3: Hierholzer's Algorithm for Eulerian path
    path = []  # This will store the final path
    stack = [start]  # Start from the determined node

    while stack:
        # Traverse until we find a node with no outgoing edges
        while graph[stack[-1]]:
            next_node = graph[stack[-1]].popleft()  # Remove the next edge
            stack.append(next_node)  # Add the next node to the stack
        # Backtrack and add the node to the path
        path.append(stack.pop())

    # Reverse the path to get the correct order
    path.reverse()

    # Convert path into pairs
    result = [[path[i], path[i + 1]] for i in range(len(path) - 1)]
    return result
```

---

### **Edge Cases in Implementation**

1. **Single Pair:**  
   Input: `[[1, 2]]`  
   Output: `[[1, 2]]`  
   Explanation: The single pair forms a valid arrangement.

2. **Disconnected Graph Components:**  
   Input: `[[1, 2], [2, 3], [4, 5]]`  
   Explanation: The problem guarantees that the input always forms a valid arrangement, so disconnected graphs should not occur.

3. **Circular Path:**  
   Input: `[[1, 2], [2, 3], [3, 1]]`  
   Output: Any valid permutation of the circular arrangement, e.g., `[[1, 2], [2, 3], [3, 1]]`.

---

### ⏱ **Time Complexity**  
- **Graph Construction**: $$ O(E) $$, where $$ E $$ is the number of edges (pairs).  
- **Eulerian Path Construction**: $$ O(E) $$.  
- **Overall**: $$ O(E) $$.  

### 🔧 **Space Complexity**  
- Adjacency list storage: $$ O(V + E) $$, where $$ V $$ is the number of unique nodes.  

---

## 🔎 **Example Walkthrough**  

Let’s walk through a more detailed example with a larger graph and higher numbers to better illustrate the solution process.

---

### Example Input:  
```plaintext
pairs = [[21, 34], [34, 45], [45, 56], [56, 67], [67, 21], [78, 89], [89, 78]]
```

---

### **Step 1: Graph Construction**  
We begin by representing the input pairs as a directed graph, using an adjacency list for efficient traversal.

#### Adjacency List:
```plaintext
Graph: 
{
    21: [34], 
    34: [45], 
    45: [56], 
    56: [67], 
    67: [21], 
    78: [89], 
    89: [78]
}
```

#### In-Degree and Out-Degree:
```plaintext
In-Degree:  {34: 1, 45: 1, 56: 1, 67: 1, 21: 1, 89: 1, 78: 1}
Out-Degree: {21: 1, 34: 1, 45: 1, 56: 1, 67: 1, 78: 1, 89: 1}
```

#### Analysis:
- All nodes have balanced in-degrees and out-degrees.  
- The graph contains two **Eulerian circuits** (connected components):  
  - Circuit 1: $$ 21 \to 34 \to 45 \to 56 \to 67 \to 21 $$  
  - Circuit 2: $$ 78 \to 89 \to 78 $$  

We can start from any node in one of the circuits.

---

### **Step 2: Find Start Node**  
Since all nodes have balanced degrees, any node can serve as the start. Let’s pick $$ 21 $$.

---

### **Step 3: Hierholzer’s Algorithm**

Perform Depth-First Search (DFS) to traverse all edges and build the Eulerian path.

#### **Circuit 1 Traversal**:
1. Start at $$ 21 $$: Stack = $$[21]$$.  
2. Visit $$ 34 $$: Stack = $$[21, 34]$$.  
3. Visit $$ 45 $$: Stack = $$[21, 34, 45]$$.  
4. Visit $$ 56 $$: Stack = $$[21, 34, 45, 56]$$.  
5. Visit $$ 67 $$: Stack = $$[21, 34, 45, 56, 67]$$.  
6. Visit $$ 21 $$: Stack = $$[21, 34, 45, 56, 67, 21]$$.  

Circuit completed: $$ [21, 34, 45, 56, 67, 21] $$.

#### **Circuit 2 Traversal**:
1. Start at $$ 78 $$: Stack = $$[78]$$.  
2. Visit $$ 89 $$: Stack = $$[78, 89]$$.  
3. Visit $$ 78 $$: Stack = $$[78, 89, 78]$$.  

Circuit completed: $$ [78, 89, 78] $$.

---

### **Step 4: Combine Circuits**

Since the circuits are disconnected, the result is the union of the two paths. Combine the circuits into the final result.

Final Eulerian Path:  
```plaintext
Path: [21, 34, 45, 56, 67, 21] + [78, 89, 78]
```

---

### **Step 5: Convert to Pairs**

Transform the Eulerian path into the output format of pairs:
```plaintext
Result: 
[[21, 34], [34, 45], [45, 56], [56, 67], [67, 21], [78, 89], [89, 78]]
```

---

### 🔑 **Edge Cases**  
1. **Single Pair:**  
   Input: `pairs = [[1, 2]]`  
   Output: `[[1, 2]]`  
   Explanation: The only pair forms a trivial valid arrangement.

2. **Multiple Disconnected Components:**  
   Input: `pairs = [[1, 2], [2, 1], [3, 4], [4, 3]]`  
   Output: `[[1, 2], [2, 1], [3, 4], [4, 3]]`  
   Explanation: The graph contains two independent circuits, which are output as separate sequences.

3. **Non-Trivial Start Node:**  
   Input: `pairs = [[5, 1], [1, 3], [3, 5]]`  
   Output: `[[5, 1], [1, 3], [3, 5]]` or any valid permutation of the circuit.  

---

### 🏁 **Conclusion**  
This enhanced example demonstrates how Hierholzer’s algorithm systematically builds the Eulerian path in complex cases. It highlights the flexibility of the solution to handle disconnected components, balanced graphs, and diverse scenarios efficiently.

---

## 📈 **Conclusion**  
This problem beautifully merges graph theory 🎓 with algorithmic problem-solving! By recognizing the structure as a graph and leveraging Eulerian paths, we solve the problem efficiently 🚀. Eulerian path problems like this are highly applicable in network routing 🌐, DNA sequencing 🧬, and more. Keep practicing and unravel the power of graph theory! 🌟
