---
layout: post
title: "#37 2406. Divide Intervals Into Minimum Number of Groups 🧠🚀"
categories: [LeetCode, Programming]
---

In this problem, we are tasked with dividing a set of intervals into groups such that no two intervals in the same group overlap. Each group should be able to hold non-overlapping intervals, and our objective is to minimize the number of groups required. This problem is about efficiently managing the overlap of intervals and finding the minimum number of distinct groups to separate them.

Understanding how to approach this problem can be useful in various real-world scenarios, such as scheduling tasks, organizing events, or managing resource allocation where different intervals represent tasks that need exclusive time slots.

### 📝 Problem Statement
You are given a 2D integer array `intervals`, where each element `intervals[i] = [lefti, righti]` represents an inclusive interval `[lefti, righti]`. The challenge is to divide the intervals into one or more groups so that no intervals within the same group overlap.

Two intervals overlap if they share at least one number. For example, intervals `[1, 5]` and `[5, 8]` overlap because they intersect at `5`.

Return the **minimum number of groups** you need to form such that no two intervals in the same group overlap.

#### Example 1:
**Input**: `intervals = [[5,10],[6,8],[1,5],[2,3],[1,10]]`  
**Output**: `3`  
**Explanation**:  
We can divide the intervals into the following groups:
- Group 1: `[1, 5], [6, 8]` - These intervals do not overlap.
- Group 2: `[2, 3], [5, 10]` - There is no overlap within this group.
- Group 3: `[1, 10]` - This interval overlaps with all others, so it must be placed in its own group.

It can be proven that it's impossible to divide the intervals into fewer than 3 groups.

#### Example 2:
**Input**: `intervals = [[1,3],[5,6],[8,10],[11,13]]`  
**Output**: `1`  
**Explanation**: None of the intervals overlap with each other, so all of them can be grouped together.

### 🛠️ Solution

To solve this problem, we can break it down into several key steps:

1. **Event-based approach**: 
   - Convert each interval into two events: one for the start and one for the end. This way, we can process intervals dynamically and efficiently track when they begin and end.
   - For example, interval `[1, 5]` would create two events: `(1, start)` and `(6, end)`, where we treat the end as exclusive (`right + 1`).
   
2. **Sort events**: 
   - We sort all the events by time. If two events happen at the same time, we handle start events before end events. This ensures that we count overlapping intervals correctly when intervals share a common boundary.
   
3. **Tracking overlapping intervals**: 
   - As we process the sorted events, we maintain a running count of the number of overlapping intervals. The maximum number of overlapping intervals at any point tells us how many groups we need.

### 💡 Key Insight:
The key insight here is that the minimum number of groups required is equal to the maximum number of overlapping intervals at any given time. If multiple intervals overlap at a single point in time, they all need to be in separate groups.

### ⏳ Time Complexity (for both basic and optimized solutions)
Since we need to sort the events and then process them, the time complexity is dominated by the sorting step. Therefore, the time complexity is **O(n log n)**, where `n` is the number of intervals. This is efficient enough for the problem's constraint of up to 100,000 intervals.

### 🧑‍💻 Python Code

Here is the Python code implementing the optimized solution.

#### Optimized Solution

```python
def minGroups(intervals):
    events = []
    
    # Convert intervals to events: (start, +1) for starting an interval, (end + 1, -1) for ending it
    for left, right in intervals:
        events.append((left, 1))  # 1 means start of interval
        events.append((right + 1, -1))  # -1 means end of interval (exclusive)
    
    # Sort events: by time first, if times are equal, starts (+1) come before ends (-1)
    events.sort()
    
    groups, current_overlap = 0, 0
    
    # Process events and track the number of overlapping intervals
    for time, event_type in events:
        current_overlap += event_type
        groups = max(groups, current_overlap)
    
    return groups
```

### 🔍 Example Walkthrough

Let’s walk through the first example step by step:

**Input**: `intervals = [[5,10],[6,8],[1,5],[2,3],[1,10]]`

1. **Step 1: Convert intervals to events**  
   We convert each interval to start and end events:
   - `[5, 10]` → `(5, +1)` and `(11, -1)`
   - `[6, 8]` → `(6, +1)` and `(9, -1)`
   - `[1, 5]` → `(1, +1)` and `(6, -1)`
   - `[2, 3]` → `(2, +1)` and `(4, -1)`
   - `[1, 10]` → `(1, +1)` and `(11, -1)`

2. **Step 2: Sort the events**  
   After sorting, we get the following list of events:  
   `[(1, 1), (1, 1), (2, 1), (3, -1), (4, -1), (5, 1), (6, -1), (6, 1), (8, -1), (9, -1), (10, -1), (11, -1)]`

3. **Step 3: Process the events**  
   We now process these events to find the maximum overlap:
   - At time `1`, two intervals start, so the overlap becomes 2.
   - At time `2`, another interval starts, increasing the overlap to 3 (maximum).
   - At time `3` and `4`, some intervals end, reducing the overlap.
   - Finally, the maximum overlap at any time is 3, meaning we need 3 groups.

**Output**: The minimum number of groups required is `3`.

### 🔚 Conclusion

In this problem, we used an event-based approach to efficiently track the number of overlapping intervals. By converting each interval into start and end events and sorting them, we were able to calculate the maximum overlap at any point in time. This value directly tells us how many groups are necessary.

The solution runs in **O(n log n)**, which is optimal given the need to sort the intervals, and it handles up to the problem's constraint of 100,000 intervals efficiently.

This approach is widely applicable in various scheduling and resource allocation problems, making it a valuable technique to understand.
