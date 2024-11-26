---
layout: post  
title: "#87 🏆 2924. Find Champion II 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Graph]
---


Imagine a thrilling tournament 🏟️ with a unique structure—a Directed Acyclic Graph (DAG)! Each team battles it out, and your goal is to crown the **true champion** 🥇. The twist? The champion must be the **strongest** team with no other team stronger than it. If there’s any ambiguity, the title of champion is denied! Let's dive into this exciting problem!

---

### Problem Statement 📝

You are given:  
1. An integer `n` representing the **number of teams** (`0` to `n - 1`).  
2. A 2D integer array `edges` of length `m`, where `edges[i] = [u, v]` indicates a directed edge from team `u` to team `v`.  
   - A directed edge from `u` to `v` implies **team `u` is stronger** than team `v`.  

**Goal**: Determine the team that will be crowned the champion.  
- A team is a **champion** if no other team is stronger than it.  
- Return `-1` if there is no unique champion.

**Key Notes**:  
1. The graph is a **DAG** (Directed Acyclic Graph), meaning there are no cycles.  
2. A champion team should have an **in-degree of 0**.  
   - The in-degree of a node represents the number of edges directed **into** that node.  

---

### Examples 📚

#### Example 1:  
**Input**:  
```python
n = 3  
edges = [[0, 1], [1, 2]]
```

**Output**:  
```python
0
```

**Explanation**:  
- Team 1 is weaker than team 0.  
- Team 2 is weaker than team 1.  
- Team 0 is stronger than everyone, making it the **champion**! 🏆  

---

#### Example 2:  
**Input**:  
```python
n = 4  
edges = [[0, 2], [1, 3], [1, 2]]
```

**Output**:  
```python
-1
```

**Explanation**:  
- Team 2 is weaker than teams 0 and 1.  
- Team 3 is weaker than team 1.  
- Teams 0 and 1 both have an in-degree of 0, so there’s **no unique champion**.

---

### Constraints 🔒

- Firstly, let $$ 1 \leq n \leq 100 $$  
- Let $$ m $$ be the number of edges ($$ m = \text{edges.length} $$):  
  $$ 0 \leq m \leq \frac{n \times (n - 1)}{2} $$  
- Each edge $$ \text{edges}[i] $$ has exactly two vertices:  
  $$ \text{edges}[i].\text{length} = 2 $$  
- Vertex indices satisfy:  
  $$ 0 \leq \text{edges}[i][j] \leq n - 1 $$  
- No self-loops:  
  $$ \text{edges}[i][0] \neq \text{edges}[i][1] $$  
- **Input guarantees**:  
  - **No cycles**.  
  - **Transitivity**: If $$ u > v $$ and $$ v > w $$, then $$ u > w $$.  


---

### Edge Cases 🌟

1. **Single team**:  
   Input: `n = 1`, `edges = []`  
   Output: `0` (The only team is the champion.)

2. **All teams weaker than one**:  
   Input: `n = 5`, `edges = [[0, 1], [0, 2], [0, 3], [0, 4]]`  
   Output: `0` (Team `0` is the only champion.)

3. **Disconnected nodes**:  
   Input: `n = 3`, `edges = [[0, 1]]`  
   Output: `-1` (Team `2` is disconnected, leading to ambiguity.)

4. **No edges**:  
   Input: `n = 4`, `edges = []`  
   Output: `-1` (All teams have in-degree 0, creating ambiguity.)

---


### Algorithm Behind the Solution 🔍

1. **What is a DAG?**  
   A **Directed Acyclic Graph (DAG)** is a directed graph with no cycles.  
   - In our context, a DAG models the relationships of "strength" between teams.  
   - If team `u` is stronger than team `v`, there's a directed edge from `u` to `v`.  

2. **Key Properties of a DAG:**  
   - **No cycles:** A team cannot indirectly be stronger than itself through other teams.  
   - **Reachability:** If there's a path from `u` to `v`, then `u` is stronger than `v`.  
   - **Topological Sorting:** A DAG can always be arranged in a sequence where all directed edges go from earlier to later nodes.

3. **Why Does This Matter?**  
   In a DAG, teams with no incoming edges (in-degree = 0) are the "strongest," as no other team is stronger than them. These are the potential champions.

---

### Algorithm to Identify the Champion 🏆

The goal is to process the DAG and identify whether there’s a unique node (team) with in-degree `0`. This node will be the champion if it’s unique. Let’s break the process into logical steps.

---

#### Step 1: **Input Parsing and Graph Representation**

The graph is represented by the `edges` array, which contains pairs `[u, v]`:
- Each pair indicates a directed edge from `u` to `v`, meaning team `u` is stronger than team `v`.  

To efficiently process the graph:
- Use an **in-degree array** of size `n` to track the number of incoming edges for each node.  

---

#### Step 2: **Calculate In-Degrees**

For each edge `[u, v]`, increment the in-degree of `v`.  
- This is done because `v` is weaker than `u`, so we add an incoming edge to `v`.

This step involves iterating through the `edges` array and updating the in-degree array.

---

#### Step 3: **Identify Candidates**

Once the in-degree array is calculated:
- Nodes with `in-degree = 0` are potential champions.
- If there are multiple nodes with `in-degree = 0`, there’s no unique champion.

This can be achieved with a simple list comprehension:
```python
candidates = [team for team in range(n) if in_degree[team] == 0]
```

---

#### Step 4: **Determine the Result**

Finally, check the number of candidates:
- If there’s exactly **one candidate**, return it as the champion.
- Otherwise, return `-1`.

---

### Expanded Algorithm (Pseudo-Code)

Here’s a more detailed breakdown of the algorithm in pseudo-code format:

1. **Initialize Variables:**  
   - Create an `in_degree` array of size `n`, initialized to `0`.

2. **Process Edges:**  
   - For each edge `[u, v]` in `edges`:  
     - Increment `in_degree[v]` by `1`.  

3. **Identify Candidates:**  
   - Collect all nodes with `in_degree = 0` into a list `candidates`.  

4. **Check Uniqueness:**  
   - If `candidates` has exactly one element, return it as the champion.  
   - Otherwise, return `-1`.



---

### Why Does This Algorithm Work? 🧠

The correctness of this algorithm relies on key properties of DAGs and in-degrees:

1. **DAGs Always Have at Least One Node with `in-degree = 0`:**  
   - By definition, DAGs cannot have all nodes with incoming edges, as this would create cycles.  

2. **In-Degree Represents "Strength Hierarchy":**  
   - Teams with `in-degree = 0` are not weaker than any other team, making them potential champions.

3. **Checking Uniqueness Ensures a Single Champion:**  
   - If multiple nodes have `in-degree = 0`, they are equally strong, and no unique champion exists.

---

### Example: Algorithm in Action 🚀

Let’s walk through the algorithm with a complex example:  

**Input:**  
```python
n = 5
edges = [[0, 1], [0, 2], [1, 3], [1, 4], [2, 4]]
```

#### Step 1: Initialize In-Degree Array  
```python
in_degree = [0, 0, 0, 0, 0]
```

#### Step 2: Process Edges  
For each edge, update the in-degree array:  
1. Edge `[0, 1]`:  
   ```python
   in_degree = [0, 1, 0, 0, 0]
   ```
2. Edge `[0, 2]`:  
   ```python
   in_degree = [0, 1, 1, 0, 0]
   ```
3. Edge `[1, 3]`:  
   ```python
   in_degree = [0, 1, 1, 1, 0]
   ```
4. Edge `[1, 4]`:  
   ```python
   in_degree = [0, 1, 1, 1, 1]
   ```
5. Edge `[2, 4]`:  
   ```python
   in_degree = [0, 1, 1, 1, 2]
   ```

#### Step 3: Identify Candidates  
Nodes with `in-degree = 0`:  
```python
candidates = [0]
```

#### Step 4: Determine Result  
There is exactly one candidate (`0`), so the champion is:  
```python
Output: 0
```

---

### Python Implementation 🐍

```python
def find_champion(n, edges):
    # Step 1: Initialize in-degree array
    in_degree = [0] * n

    # Step 2: Populate in-degree array
    for u, v in edges:
        in_degree[v] += 1

    # Step 3: Identify candidates
    candidates = [team for team in range(n) if in_degree[team] == 0]

    # Step 4: Return the result
    return candidates[0] if len(candidates) == 1 else -1
```

---

### Example Walkthrough: Large Input 🔍

#### Input:
```python
n = 8
edges = [[0, 1], [1, 2], [2, 3], [4, 5], [5, 6], [6, 7]]
```

#### Step-by-Step:
1. **Initialize**:  
   ```python
   in_degree = [0, 0, 0, 0, 0, 0, 0, 0]
   ```

2. **Update in-degrees**:  
   Processing edges results in:  
   ```python
   in_degree = [0, 1, 1, 1, 0, 1, 1, 1]
   ```

3. **Find candidates**:  
   ```python
   candidates = [0, 4]
   ```

4. **Output**:  
   Since there are multiple candidates (`0` and `4`), return `-1`.  

---

### Edge Cases 🔍

1. **Single Node**:  
   Input:  
   ```python
   n = 1
   edges = []
   ```  
   Output:  
   ```python
   0  # The only team is the champion
   ```

2. **Disconnected Graph**:  
   Input:  
   ```python
   n = 4
   edges = []
   ```  
   Output:  
   ```python
   -1  # All teams have in-degree 0, causing ambiguity
   ```

---

### Time and Space Complexity Analysis 🕒

1. **Time Complexity:**  
   - Calculating in-degrees involves processing all `m` edges, which takes $$O(m)$$.  
   - Identifying candidates involves iterating through `n` nodes, which takes $$O(n)$$.  
   - Overall time complexity:  
     $$
     O(n + m)
     $$  
     This is efficient for large inputs, as $$m$$ (number of edges) is bounded by $$n(n-1)/2$$.  

2. **Space Complexity:**  
   - We use an `in_degree` array of size $$O(n)$$.  
   - The graph itself is not explicitly stored; only the edges are processed.  
   - Overall space complexity:  
     $$
     O(n)
     $$  


---

### Conclusion 🌟

This problem is a fantastic exercise in understanding graph properties and leveraging the unique characteristics of DAGs. It showcases the power of **in-degree analysis** and efficient traversals. Always remember to consider edge cases, especially for disconnected nodes or ambiguous scenarios. 🏆
