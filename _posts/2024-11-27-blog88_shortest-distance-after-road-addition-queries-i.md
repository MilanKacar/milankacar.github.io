---
layout: post  
title: "#88 🛣️🚗 3243. Shortest Distance After Road Addition Queries I 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Breadth-First Search, Graph]
---

Finding the shortest path in a graph is one of the foundational problems in computer science, especially in applications like routing, logistics, and network optimization. Today, we’ll explore an intriguing variation of the shortest path problem where the graph evolves over time, and we compute the shortest path **dynamically**! This problem is a medium-difficulty gem from LeetCode: **Shortest Distance After Road Addition Queries.** Buckle up! 🚀

---

## Problem Statement 🧾

We are given:  
- An integer `n`, representing the number of cities (`0` to `n-1`).
- Initially, there is a unidirectional road from city `i` to city `i + 1` for all `0 <= i < n - 1`.  
- A 2D integer array `queries`, where each query `[u, v]` adds a unidirectional road from city `u` to city `v`.

For each query, we need to find the length of the shortest path from city `0` to city `n-1` after processing the **first i + 1 queries**.

### Example

#### Example 1:
**Input:**  
```
n = 5
queries = [[2, 4], [0, 2], [0, 4]]
```

**Output:**  
```
[3, 2, 1]
```

**Explanation:**  
- **Initially:** The path is `0 → 1 → 2 → 3 → 4` with a total length of 4.  
- After the **first query** `[2, 4]`, the path becomes `0 → 1 → 2 → 4` with a length of 3.  
- After the **second query** `[0, 2]`, the path becomes `0 → 2 → 4` with a length of 2.  
- After the **third query** `[0, 4]`, the path becomes `0 → 4` with a length of 1.

---

#### Example 2:
**Input:**  
```
n = 4
queries = [[0, 3], [0, 2]]
```

**Output:**  
```
[1, 1]
```

**Explanation:**  
- **Initially:** The path is `0 → 1 → 2 → 3` with a total length of 3.  
- After the **first query** `[0, 3]`, the path becomes `0 → 3` with a length of 1.  
- The second query `[0, 2]` does not change the shortest path since the direct road from `0 → 3` already exists.

---

### Constraints ⛓️
- $$3 \leq n \leq 500$$
- $$1 \leq \text{queries.length} \leq 500$$
- $$0 \leq u_i < v_i < n$$
- $$1 < v_i - u_i$$
- Queries do not add repeated roads.

---

### 💡 Intuition and Approach

When tackling a problem like this, where we need to handle **dynamic changes in a graph** and compute the shortest path repeatedly, it’s essential to break the problem into smaller, manageable parts. Let’s dissect the key challenges and the thought process behind solving them:

---

### **1. Understanding the Initial Graph**

The problem begins with a simple, linear graph structure:

```
0 → 1 → 2 → ... → n-1
```

This is a directed acyclic graph (DAG), where each city `i` has a direct road to city `i+1`. The shortest path from `0` to `n-1` is initially straightforward:
- **Path:** `0 → 1 → 2 → ... → n-1`
- **Distance:** $$ n - 1 $$

---

### **2. Impact of Each Query**

Each query `[u, v]` adds a new road from city `u` to city `v`. This introduces the potential for **shortcuts** that might reduce the distance between city `0` and city `n-1`. The key task for each query is to determine if and how the newly added road improves the shortest path.

For example:
- If we add a road `[2, 4]`, the path can skip cities `3` entirely:
  - Old path: `0 → 1 → 2 → 3 → 4`
  - New path: `0 → 1 → 2 → 4`

---

### **3. Graph Representation**

To process the problem effectively, we need to represent the graph in a way that allows for dynamic updates. A good choice here is an **adjacency list**, where:
- Each city points to a list of its neighbors.  
- Initially, city `i` points to `i+1`.  
- When a query `[u, v]` is processed, we simply add `v` to the list of neighbors for city `u`.

For example, after processing the query `[2, 4]`:
```python
graph = {
    0: [1],
    1: [2],
    2: [3, 4],  # Road from 2 to 4 added
    3: [4],
    4: [5],
    ...
}
```

---

### **4. Finding the Shortest Path Dynamically**

Once we process a query and update the graph, the next step is to determine the shortest path from city `0` to city `n-1`. There are multiple ways to compute this:

- **Breadth-First Search (BFS):**  
  Since the graph is unweighted, BFS is an efficient way to find the shortest path in $$O(V + E)$$ time. It explores the graph level by level and guarantees the shortest path when the first instance of `n-1` is reached.

- **Dijkstra’s Algorithm:**  
  If the graph had weights (or if we want a more flexible approach), Dijkstra’s algorithm would be a better choice. It uses a priority queue to explore nodes with the smallest cumulative distance first. Although it’s overkill for an unweighted graph, it provides a scalable framework for more complex variations.

---

### **5. Key Observations for Optimization**

Efficient solutions stem from identifying redundancies and patterns:
- **Only Update When Necessary:**  
  Not all queries change the shortest path. For example:
  - If a direct road `[0, n-1]` is added, it immediately becomes the shortest path.  
  - A road `[u, v]` where $$v < n-1$$ may not improve the shortest path to `n-1`.

- **Reuse Information:**  
  Instead of recomputing the entire shortest path from scratch after every query, we can incrementally update the distances for affected nodes. This makes algorithms like BFS or Dijkstra’s more efficient.

---

### **6. Challenges in Dynamic Graphs**

Dynamic graph problems require handling:
1. **Incremental Updates:**  
   Each query modifies the graph. This means we must update our understanding of the shortest path efficiently.
   
2. **Avoiding Full Recomputations:**  
   For large graphs with many queries, recomputing the shortest path from scratch for each query can be prohibitively slow. Instead, focus on **local updates** or algorithms like **incremental BFS**.

3. **Cycle Prevention:**  
   Although the problem guarantees no repeated roads, it’s crucial to ensure that shortcuts don’t create incorrect paths (e.g., skipping intermediate cities incorrectly).

---

### **7. Special Case Scenarios**

To further solidify the intuition, consider some edge cases:
- **Direct Connection:**  
  A query like `[0, n-1]` immediately renders all other paths irrelevant, as it creates the shortest possible route.

- **Redundant Roads:**  
  Queries like `[u, v]` that add roads bypassing cities already on the shortest path may not change the result. For instance:
  - Query `[1, 4]` when the shortest path is already `0 → 1 → 4`.

- **Disconnected Segments:**  
  A road that doesn’t contribute to reaching `n-1` (e.g., `[2, 3]` when the shortest path is already `0 → 1 → 5 → 6`).

---

### **8. Strategy for Implementation**

With the above insights, the approach boils down to:
1. **Graph Representation:** Use an adjacency list to efficiently store roads.
2. **Incremental Updates:** For each query, update the graph structure and run a shortest-path algorithm.
3. **Shortest Path Calculation:** Use BFS for simplicity or Dijkstra’s for scalability.

By understanding the structure and dynamics of the problem, we ensure that our solution is not only correct but also efficient! 🚀
---

## 📝 Solution

### **Basic Solution: Breadth-First Search (BFS)** 🌳

BFS is a simple yet effective algorithm for finding the shortest path in an unweighted graph. Here's the plan:

1. Start from city `0` and explore all connected cities level by level.
2. Keep track of the shortest path to each city.
3. For each query, add the new road to the graph and run BFS again.

#### Implementation in Python 🐍
```python
from collections import deque

def shortestPathQueries(n, queries):
    # Initialize the adjacency list for the graph
    graph = {i: [] for i in range(n)}
    for i in range(n - 1):
        graph[i].append(i + 1)  # Initial roads
    
    answers = []
    
    # Process each query
    for u, v in queries:
        graph[u].append(v)  # Add new road
        
        # Run BFS from city 0 to find shortest path to city n-1
        queue = deque([0])
        distances = [-1] * n
        distances[0] = 0
        
        while queue:
            current = queue.popleft()
            for neighbor in graph[current]:
                if distances[neighbor] == -1:
                    distances[neighbor] = distances[current] + 1
                    queue.append(neighbor)
        
        # Shortest path to city n-1
        answers.append(distances[n - 1])
    
    return answers
```

---

### Time Complexity ⏳

1. **Graph Construction:** Each query adds a new edge, which takes $$O(1)$$.
2. **BFS Execution:** BFS takes $$O(V + E)$$, where $$V$$ is the number of cities, and $$E$$ is the number of edges.
   - In the worst case, $$E = n - 1 + q$$, where $$q$$ is the number of queries.
3. **Total Complexity:** For $$q$$ queries, the total complexity is $$O(q \cdot (V + E))$$.  

### Space Complexity 🧠
The adjacency list and distance array take $$O(V + E)$$.

---

### **Optimized Solution: Dijkstra’s Algorithm** 🚀

For graphs with weighted edges, Dijkstra's Algorithm is a powerful method for finding the shortest path. In this problem, even though the graph is unweighted, we can adapt Dijkstra’s approach to maintain efficiency.

---

(continued)

### Optimized Solution Implementation 🚀

Using Dijkstra's algorithm ensures we efficiently compute the shortest path by dynamically prioritizing the nearest nodes. Here's how it works for our problem:

#### Implementation in Python 🐍
```python
import heapq

def shortestPathQueriesOptimized(n, queries):
    # Initialize the adjacency list for the graph
    graph = {i: [] for i in range(n)}
    for i in range(n - 1):
        graph[i].append((i + 1, 1))  # Initial roads with weight 1
    
    answers = []
    
    # Process each query
    for u, v in queries:
        graph[u].append((v, 1))  # Add the new road with weight 1
        
        # Run Dijkstra's algorithm to find shortest path from city 0 to city n-1
        min_heap = [(0, 0)]  # (distance, city)
        distances = {i: float('inf') for i in range(n)}
        distances[0] = 0
        
        while min_heap:
            curr_distance, curr_city = heapq.heappop(min_heap)
            
            if curr_distance > distances[curr_city]:
                continue
            
            for neighbor, weight in graph[curr_city]:
                distance = curr_distance + weight
                if distance < distances[neighbor]:
                    distances[neighbor] = distance
                    heapq.heappush(min_heap, (distance, neighbor))
        
        # Shortest path to city n-1
        answers.append(distances[n - 1])
    
    return answers
```

---

### Time Complexity ⏳

1. **Graph Update:** Adding a new edge for each query takes $$O(1)$$.  
2. **Dijkstra's Execution:** Each query runs Dijkstra's algorithm, which is $$O((V + E) \cdot \log(V))$$.  
   - $$V = n$$, the number of cities.  
   - $$E = n - 1 + q$$, the total number of edges after processing all queries.  
3. **Total Complexity:** For $$q$$ queries, the complexity is $$O(q \cdot (n + q) \cdot \log(n))$$.

### Space Complexity 🧠

The adjacency list and priority queue take $$O(V + E)$$, which simplifies to $$O(n + q)$$.

---

## 🔍 Example Walkthrough: Using Higher Numbers 🎯

### Input:
```
n = 7
queries = [[2, 6], [1, 5], [0, 4], [0, 6]]
```

---

### Initial Graph:  
```  
0 → 1 → 2 → 3 → 4 → 5 → 6  
```

---

### Query 1: Add Road [2, 6]  

#### Graph Update:
```  
0 → 1 → 2 → 3 → 4 → 5 → 6  
          ↘︎  
           6  
```

#### Shortest Path:
Using BFS/Dijkstra's:  
Path = `0 → 1 → 2 → 6`  
**Length = 3**

---

### Query 2: Add Road [1, 5]  

#### Graph Update:
```  
0 → 1 → 2 → 3 → 4 → 5 → 6  
      ↘︎      ↘︎  
       5       6  
```

#### Shortest Path:
Path = `0 → 1 → 5 → 6`  
**Length = 3**

---

### Query 3: Add Road [0, 4]  

#### Graph Update:
```  
0 → 1 → 2 → 3 → 4 → 5 → 6  
 ↘︎      ↘︎      ↘︎  
  4       5       6  
```

#### Shortest Path:
Path = `0 → 4 → 5 → 6`  
**Length = 3**

---

### Query 4: Add Road [0, 6]  

#### Graph Update:
```  
0 → 1 → 2 → 3 → 4 → 5 → 6  
 ↘︎      ↘︎      ↘︎  
  6       5       6  
```

#### Shortest Path:
Path = `0 → 6`  
**Length = 1**

---

### Final Output:
```
[3, 3, 3, 1]
```

---

## 🌟 Edge Cases to Consider

1. **Small Graphs:**  
   - $$n = 3$$ (smallest valid graph).  
   - Queries that don’t change the path, e.g., adding a road from `0 → 2` when the path already goes through `0 → 1 → 2`.  

2. **Disconnected Graphs:**  
   - Adding a road that doesn’t connect to `n-1` immediately.  

3. **Redundant Queries:**  
   - Adding a road that creates a cycle or duplicates an existing path.  

---

## 🏁 Conclusion

In this post, we tackled the dynamic shortest path problem using both **BFS** and **Dijkstra’s Algorithm**. While BFS works well for small graphs, Dijkstra's algorithm provides an optimized solution for larger graphs with many queries.  

### Key Takeaways:  
- **Dynamic Graph Updates** require efficient algorithms to avoid recomputation.  
- **Edge Cases** should be considered to ensure correctness.  
- Real-world problems like routing systems can benefit from these graph-based solutions.  

Happy coding! 💻✨
