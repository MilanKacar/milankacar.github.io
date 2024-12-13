---
layout: post  
title: "#102 2593. Find Score of an Array After Marking All Elements 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Heap (Priority Queue), Sorting, Array, Simulation, Greedy, Two Pointers, Queue, Divide and Conquer, Dynamic Programming]
---


Welcome back to another **LeetCode adventure**! Today, we're solving a fascinating problem involving marking elements in an array, updating their neighbors, and calculating the score. This problem is both an exercise in logical thinking and a great opportunity to practice working with **priority queues**. Let's dive in!

---

### Problem Statement 📈

You are given an array `nums` consisting of positive integers. Starting with `score = 0`, apply the following algorithm:

1. Choose the smallest unmarked integer in the array. If there is a tie, choose the one with the smallest index.
2. Add the value of the chosen integer to `score`.
3. Mark the chosen element and its two adjacent elements (if they exist).
4. Repeat until all elements in the array are marked.

Return the total `score` after applying this algorithm.

#### Example 1 📊

**Input:** `nums = [2,1,3,4,5,2]`  
**Output:** `7`  
**Explanation:**  
- Start with the smallest unmarked element, `1`. Mark it and its neighbors, leaving `[2,1,3,4,5,2]`.
- The next smallest unmarked element is `2`. Mark it and its left neighbor, leaving `[2,1,3,4,5,2]`.
- Finally, mark `4`, the last unmarked element.  
**Score:** `1 + 2 + 4 = 7`.

#### Example 2 📈

**Input:** `nums = [2,3,5,1,3,2]`  
**Output:** `5`  
**Explanation:**  
- Start with the smallest unmarked element, `1`. Mark it and its neighbors, leaving `[2,3,5,1,3,2]`.
- The next smallest unmarked element is `2`. Mark the leftmost one, leaving `[2,3,5,1,3,2]`.
- Finally, mark the remaining `2`.  
**Score:** `1 + 2 + 2 = 5`.

#### Constraints 🔒

- `1 ≤ nums.length ≤ 10^5`  
- `1 ≤ nums[i] ≤ 10^6`

---

### Solution Breakdown 🛠️

#### **1. Why Use a Priority Queue?**  
At each step, the smallest unmarked element must be processed. A **priority queue** (min-heap) is perfect for this task because:  
- It allows us to extract the smallest element in \(O(\log n)\) time.  
- The heap property ensures efficient insertion and deletion, maintaining the order by value.  
- It inherently sorts the elements, so we don’t need additional sorting logic.  

---

#### **2. Role of the Visited Array**  
To ensure that each element is marked only once, we maintain a `visited` array. This is crucial because:  
- Without it, we might double-count or repeatedly process elements, leading to incorrect results.  
- It provides a simple \(O(1)\) lookup for determining whether an element or its neighbors are already processed.  

---

#### **3. Algorithm Logic**  
The algorithm simulates the marking process while maintaining efficiency. Here's a more detailed breakdown of each step:  

**Step 1**: **Initialization**  
- Build the `priority_queue` with `(value, index)` pairs from the array. This ensures we can sort by value but still access the original index for marking neighbors.  
- Create a `visited` array initialized to `False` to track which elements are marked.  

**Step 2**: **Processing the Priority Queue**  
- Extract the smallest element `(value, index)` from the heap.  
- Check if the element is already marked using the `visited` array. If it is, skip it.  
- If unmarked:  
  1. Add its value to the `score`.  
  2. Mark the element and its immediate neighbors as `True` in the `visited` array.  

**Step 3**: **Neighbor Marking**  
- If the element has a left neighbor (`index - 1 >= 0`), mark it as `visited`.  
- Similarly, mark the right neighbor (`index + 1 < n`) if it exists.  

**Step 4**: **Repeat**  
- Continue until the heap is empty, meaning all elements are processed.  

---

#### **4. Step-by-Step Example with Details**  
Let’s expand the walkthrough to illustrate the logic further:  

**Input:** `nums = [4, 1, 3, 9, 2]`  

**Heap Initialization:**  
- Initial heap: `[(1, 1), (2, 4), (3, 2), (4, 0), (9, 3)]`.  

**Iteration 1:**  
- Pop `(1, 1)`.  
  - Add `1` to `score`.  
  - Mark `nums[1]` (value `1`) and its neighbors (`nums[0]` and `nums[2]`).  
  - Updated `visited`: `[True, True, True, False, False]`.  
  - Updated `score = 1`.  

**Iteration 2:**  
- Skip `(3, 2)` since `nums[2]` is already marked.  
- Pop `(2, 4)`.  
  - Add `2` to `score`.  
  - Mark `nums[4]` (value `2`) and its left neighbor (`nums[3]`).  
  - Updated `visited`: `[True, True, True, True, True]`.  
  - Updated `score = 1 + 2 = 3`.  

**Iteration 3:**  
- Remaining elements are already marked. The heap is exhausted.  

**Final Score:** `3`.  

---

#### **5. Key Optimizations**  
The algorithm avoids unnecessary operations by:  
- **Using a heap**: Guarantees efficient retrieval of the smallest unmarked element.  
- **Skipping marked elements**: Ensures we only process unmarked elements, saving computation.  
- **Marking neighbors directly**: Prevents revisiting adjacent elements during subsequent iterations.  

---

#### **6. Alternative Approaches**  
If you’re exploring variations:  
1. **Sorting and Sequential Processing**  
   - Sort the array by value and process elements sequentially.  
   - This approach works but lacks the dynamic efficiency of a heap.  
   - Complexity: \(O(n \log n)\) (sorting) + \(O(n)\) (processing).  

2. **Segment Tree or Fenwick Tree**  
   - Use a tree structure to efficiently mark and query segments of the array.  
   - More complex to implement but might be beneficial for related problems with frequent range updates.  

---

#### **7. Practical Considerations**  
1. **Scalability**:  
   - The algorithm performs well even for the largest constraints (\(n = 10^5\), \(nums[i] = 10^6\)).  
2. **Edge Cases**:  
   - Single-element arrays, arrays with duplicate values, and arrays where marking neighbors overlaps are all handled seamlessly.  

---

#### Key Observations 🧠

1. We need to pick the **smallest unmarked element** at each step.
2. When an element is marked, its **neighbors** are also marked.
3. Efficiently finding the smallest unmarked element requires a **priority queue** (min-heap).

---

### Optimized Solution 🎮

We’ll use a **priority queue** to ensure that we always process the smallest unmarked element efficiently.

#### Algorithm Steps 🏃

1. **Initialization**: Create a `priority_queue` (min-heap) containing pairs of `(value, index)` for all elements in `nums`.
2. **Visited Tracker**: Use a `visited` array to track whether elements and their neighbors are marked.
3. **Processing**:
   - Pop the smallest element from the heap.
   - Add its value to the score.
   - Mark the element and its neighbors in the `visited` array.
   - Skip already marked elements in subsequent iterations.
4. **Result**: Return the total score.

#### Python Code 🐉
```python
from heapq import heapify, heappop

class Solution:
    def findScore(self, nums):
        n = len(nums)
        visited = [False] * n
        priority_queue = [(value, index) for index, value in enumerate(nums)]
        heapify(priority_queue)

        score = 0

        while priority_queue:
            value, index = heappop(priority_queue)
            if not visited[index]:
                score += value
                visited[index] = True
                if index - 1 >= 0:
                    visited[index - 1] = True
                if index + 1 < n:
                    visited[index + 1] = True

        return score
```

---

### Example Walkthrough 🔢

Let’s walk through an example in detail:

#### Input: `nums = [3, 1, 4, 1, 5, 9]`

**Step 1**:
- Initial heap: `[(1, 1), (1, 3), (3, 0), (4, 2), (5, 4), (9, 5)]`
- Choose `(1, 1)`. Add `1` to score. Mark `1` and its neighbors.
- Visited array: `[False, True, True, True, False, False]`
- Remaining heap: `[(1, 3), (3, 0), (4, 2), (5, 4), (9, 5)]`

**Step 2**:
- Skip `(1, 3)` since it’s already marked.
- Choose `(3, 0)`. Add `3` to score. Mark `3` and its neighbor.
- Visited array: `[True, True, True, True, False, False]`
- Remaining heap: `[(4, 2), (5, 4), (9, 5)]`

**Step 3**:
- Skip `(4, 2)` since it’s already marked.
- Choose `(5, 4)`. Add `5` to score. Mark `5` and its neighbor.
- Visited array: `[True, True, True, True, True, True]`
- Remaining heap: `[(9, 5)]`

**Final Step**:
- All elements are marked. Total score = `1 + 3 + 5 = 9`.

---

### Time Complexity Analysis ⌚

#### Optimized Solution

1. **Heap Operations**: Building the heap takes `O(n)`. Each pop and push operation is `O(log n)`. In the worst case, we process all `n` elements, so this part is `O(n log n)`.
2. **Marking Neighbors**: Each element is marked at most once, which is `O(n)`.

**Overall Complexity**: `O(n log n)`.

#### Space Complexity

1. **Visited Array**: `O(n)`.
2. **Priority Queue**: `O(n)`.

**Overall Complexity**: `O(n)`.

---

### Edge Cases 🚫

1. **Single Element**: If `nums = [5]`, the score is `5`.
2. **All Elements Equal**: If `nums = [2, 2, 2, 2]`, process from left to right.
3. **Large Values**: Handles up to `10^6`.
4. **Already Sorted**: Algorithm ensures correctness even with sorted arrays.

---

### Conclusion 🎉

This problem is a great mix of logical reasoning and efficient data structures. By using a **priority queue**, we ensure that we always process the smallest unmarked element efficiently. The marking logic ensures that the constraints are respected.

Whether you're preparing for interviews or sharpening your algorithmic skills, this problem offers valuable lessons in **heap operations**, **simulation**, and **adjacency handling**.

Keep solving, and see you in the next challenge! 💪

