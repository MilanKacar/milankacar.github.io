---
layout: post  
title: "#100 📆 2054. Two Best Non-Overlapping Events 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Binary Search, Dynamic Programming, Sorting, Heap (Priority Queue)]
---

When juggling multiple opportunities, the trick lies in choosing the ones that offer the highest rewards while avoiding overlaps. This problem is a classic example of finding an optimal solution under constraints, where we aim to maximize the sum of values by attending at most two non-overlapping events. 💡 Let's unravel it step by step and dive into the magic of efficient algorithms! 🚀  

---

## 💼 Problem Statement  

You are given a **0-indexed** 2D integer array `events` where:  
- `events[i] = [startTimeᵢ, endTimeᵢ, valueᵢ]`,  
- `startTimeᵢ` is the start time of the i-th event,  
- `endTimeᵢ` is the end time of the i-th event,  
- `valueᵢ` is the value obtained from attending the i-th event.  

### Goal 🎯  
Select **at most two non-overlapping events** such that the sum of their values is maximized.  

### Key Constraints:  
1. Start and end times are inclusive, meaning you cannot attend two events where one starts as the other ends. Specifically:
   - If an event ends at time `t`, the next event must start at or after `t + 1`.
2. Return the maximum sum of values from two non-overlapping events.  

---

### ✨ Example 1:  
#### Input:  
```python
events = [[1,3,2],[4,5,2],[2,4,3]]
```  
#### Output:  
```python
4
```  
#### Explanation:  
Choose events `[1,3,2]` and `[4,5,2]` for a total value of `2 + 2 = 4`.  

---

### ✨ Example 2:  
#### Input:  
```python
events = [[1,3,2],[4,5,2],[1,5,5]]
```  
#### Output:  
```python
5
```  
#### Explanation:  
Choose the single event `[1,5,5]` since attending any other event would cause overlap.  

---

### ✨ Example 3:  
#### Input:  
```python
events = [[1,5,3],[1,5,1],[6,6,5]]
```  
#### Output:  
```python
8
```  
#### Explanation:  
Choose events `[1,5,3]` and `[6,6,5]` for a total value of `3 + 5 = 8`.  

---

## 🧩 Edge Cases to Consider  

1. **Small Inputs**  
   - Only two events that overlap:  
     ```python
     events = [[1,2,3],[2,3,5]]
     Output: 5
     ```  
   - Only one event:  
     ```python
     events = [[1,5,10]]
     Output: 10
     ```  

2. **All Events Overlap**  
   - Example:  
     ```python
     events = [[1,5,2],[2,6,3],[3,7,4]]
     Output: 4
     ```  
     In this case, you can pick only one event with the highest value.  

3. **Disjoint Events**  
   - Example:  
     ```python
     events = [[1,2,10],[3,4,20],[5,6,30]]
     Output: 50
     ```  
     Here, you can pick the two highest-value events as they don’t overlap.  

4. **Sparse Time Range**  
   - Events that are widely spaced apart:  
     ```python
     events = [[1,2,1],[1000,1001,2],[2000,2001,3]]
     Output: 5
     ```  

---

## 🔍 Key Insights  

1. **Sorting for Simplicity**  
   Sorting events by their end times ensures that we process events in a timeline order. This makes it easier to compare overlapping conditions.  

2. **Binary Search for Efficiency**  
   When checking for the highest value of a previously attended event that doesn’t overlap with the current event, a binary search helps find the best match quickly.  

3. **Dynamic Programming for Optimization**  
   By maintaining a rolling record of maximum event values, we avoid recomputation and make the solution efficient.  

---

## 🛠️ Optimized Solution  

## 📚 Detailed Solution Approach  

The goal is to maximize the sum of values from **at most two non-overlapping events**. The key challenges here are:  

1. Efficiently checking which events can be combined without overlapping.  
2. Tracking the highest values of possible event combinations as we iterate through the list.  

Let’s break down the solution step by step.  

---

### 🔑 Step 1: Sort Events by End Time  

Sorting events by their **end times** simplifies the problem. After sorting:  
- Any previously processed event is guaranteed to end before the current event.  
- This ensures that we only check whether the current event overlaps with earlier events and not vice versa.  

For example, if the input events are:  
```python
events = [[1, 3, 5], [2, 5, 7], [4, 6, 9]]
```  
After sorting by end time (`events[i][1]`):  
```python
sorted_events = [[1, 3, 5], [2, 5, 7], [4, 6, 9]]
```  

Sorting takes **O(n log n)** time, where `n` is the number of events.  

---

### 🔑 Step 2: Use Binary Search to Find Compatible Events  

For a given event, we need to find the **latest event that does not overlap** with it.  
- Specifically, we want an event whose **end time** is less than the **start time** of the current event.  
- To do this efficiently, we use **binary search** on a list of previously processed event end times.  

#### Why Binary Search?  
Binary search reduces the complexity of finding compatible events to **O(log n)** per query.  
- Instead of scanning through all prior events to check for compatibility, binary search allows us to directly jump to the relevant candidate.  

We use the Python `bisect_right` function, which returns the position where the current event's start time would fit in the list of previously processed event end times.  

---

### 🔑 Step 3: Maintain Rolling Maximum Values  

To keep track of the maximum value of previously processed events, we maintain two lists:  
1. `max_weights`: Stores the maximum values observed so far.  
   - At each step, this list tells us the best value we can achieve by attending only one event up to the current point.  
2. `max_weight_ends`: Stores the corresponding end times for the values in `max_weights`.  
   - This allows us to check compatibility efficiently using binary search.  

#### Updating `max_weights` and `max_weight_ends`:  
- After processing each event, we compare its value with the current maximum in `max_weights`.  
- If the new event has a higher value, we append it to `max_weights` along with its end time to `max_weight_ends`.  

Example:  
For events:  
```python
events = [[1, 3, 5], [4, 6, 10], [7, 9, 15]]
```  
1. Start with:  
   ```python
   max_weights = [0]  # Rolling maximum values
   max_weight_ends = [-1]  # Corresponding end times
   ```  
2. After processing `[1, 3, 5]`:  
   ```python
   max_weights = [0, 5]
   max_weight_ends = [-1, 3]
   ```  
3. After processing `[4, 6, 10]`:  
   ```python
   max_weights = [0, 5, 10]
   max_weight_ends = [-1, 3, 6]
   ```  
4. After processing `[7, 9, 15]`:  
   ```python
   max_weights = [0, 5, 10, 15]
   max_weight_ends = [-1, 3, 6, 9]
   ```  

---

### 🔑 Step 4: Calculate the Maximum Sum  

For each event, calculate the **potential maximum value** by combining:  
1. The value of the current event.  
2. The maximum value of a compatible previous event (found using binary search).  

If the current event’s start time is `start` and value is `value`:  
- Use `bisect_right(max_weight_ends, start - 1)` to find the latest compatible event.  
- Add its value from `max_weights`.  
- Update the global maximum (`max_two`) if this combination is better.  

---

### 🔑 Example Walkthrough  

Let’s process a more detailed example:  

#### Input:  
```python
events = [[1, 5, 3], [6, 10, 10], [11, 15, 20], [16, 20, 30]]
```  

#### Step-by-Step Execution:  

1. **Sort Events by End Time:**  
   ```python
   sorted_events = [[1, 5, 3], [6, 10, 10], [11, 15, 20], [16, 20, 30]]
   ```  

2. **Initialize Trackers:**  
   ```python
   max_weights = [0]  # Initial maximum value
   max_weight_ends = [-1]  # Initial end times
   max_two = 0  # Final result
   ```  

3. **Process Events:**  

   - **Event [1, 5, 3]:**  
     - No compatible previous event (`bisect_right([-1], 0) - 1 = -1`).  
     - `max_two = max(0, 3 + 0) = 3`.  
     - Update trackers:  
       ```python
       max_weights = [0, 3]
       max_weight_ends = [-1, 5]
       ```  

   - **Event [6, 10, 10]:**  
     - Compatible event: `[1, 5, 3]` (`bisect_right([5], 5) - 1 = 0`).  
     - `max_two = max(3, 10 + 3) = 13`.  
     - Update trackers:  
       ```python
       max_weights = [0, 3, 10]
       max_weight_ends = [-1, 5, 10]
       ```  

   - **Event [11, 15, 20]:**  
     - Compatible event: `[6, 10, 10]` (`bisect_right([10], 10) - 1 = 1`).  
     - `max_two = max(13, 20 + 10) = 30`.  
     - Update trackers:  
       ```python
       max_weights = [0, 3, 10, 20]
       max_weight_ends = [-1, 5, 10, 15]
       ```  

   - **Event [16, 20, 30]:**  
     - Compatible event: `[11, 15, 20]` (`bisect_right([15], 15) - 1 = 2`).  
     - `max_two = max(30, 30 + 20) = 50`.  
     - Update trackers:  
       ```python
       max_weights = [0, 3, 10, 20, 30]
       max_weight_ends = [-1, 5, 10, 15, 20]
       ```  

4. **Final Result:**  
   ```python
   max_two = 50
   ```  

---

### 🐍 Python Code  

```python
from bisect import bisect_right

class Solution:
    def maxTwoEvents(self, events: List[List[int]]) -> int:
        # Step 1: Pre-sort events by their ending times
        events.sort(key=lambda x: x[1])
        
        # Step 2: Initialize trackers for maximum values and ends
        max_weights = [0]  # Rolling max weights
        max_weight_ends = [-1]  # Rolling end times
        
        max_two = 0  # Final result
        
        # Step 3: Iterate over each event
        for start, end, value in events:
            # Find the max-weight event ending before the current event's start time
            index = bisect_right(max_weight_ends, start - 1) - 1
            
            # Update max_two with the best sum of values
            if value + max_weights[index] > max_two:
                max_two = value + max_weights[index]
            
            # Update rolling max values
            if value > max_weights[-1]:
                max_weights.append(value)
                max_weight_ends.append(end)
        
        return max_two
```

---

### 🔍 Example Walkthrough (With Higher Numbers)  

#### Input:  
```python
events = [[1, 5, 10], [6, 10, 15], [11, 15, 20], [16, 20, 25]]
```  

#### Sorted Events:  
```python
[[1, 5, 10], [6, 10, 15], [11, 15, 20], [16, 20, 25]]
```  

#### Step-by-Step Execution:  
1. **First Event:** `[1,5,10]`  
   - No previous non-overlapping event.  
   - `max_two = max(0, 10) = 10`.  
   - Update `max_weights = [0, 10]`, `max_weight_ends = [-1, 5]`.  

2. **Second Event:** `[6,10,15]`  
   - Previous compatible event: `[1,5,10]`.  
   - `max_two = max(10, 15 + 10) = 25`.  
   - Update `max_weights = [0, 10, 15]`, `max_weight_ends = [-1, 5, 10]`.  

3. **Third Event:** `[11,15,20]`  
   - Previous compatible event: `[6,10,15]`.  
   - `max_two = max(25, 20 + 15) = 35`.  
   - Update `max_weights = [0, 10, 15, 20]`, `max_weight_ends = [-1, 5, 10, 15]`.  

4. **Fourth Event:** `[16,20,25]`  
   - Previous compatible event: `[11,15,20]`.  
   - `max_two = max(35, 25 + 20) = 45`.  
   - Update `max_weights = [0, 10, 15, 20, 25]`, `max_weight_ends = [-1, 5, 10, 15, 20]`.  

#### Output:  
```python
45
```  

---

## ⏱️ Time Complexity  

### Sorting:  
Sorting the events takes **O(n log n)**, where `n` is the number of events.  

### Binary Search:  
For each event, finding the best compatible event takes **O(log n)** using binary search.  

### Overall:  
**O(n log n)**, which is efficient for the given constraints.  

---

## 🔚 Conclusion  

This problem elegantly combines sorting, binary search, and dynamic programming for an efficient solution. 🧠 By leveraging a clear structure and precomputed results, we ensure that we can handle large inputs efficiently.  

Now, you're ready to tackle event optimization problems with confidence! 💪 Keep exploring, keep optimizing, and happy coding! 🚀
