---
layout: post
title: "#31 1942. The Number of the Smallest Unoccupied Chair 🪑 🧠🚀"
categories: [LeetCode, Programming]
---

Ever wondered how to find a seat at a party, especially when it’s a race for the smallest unoccupied chair? This fun problem dives into seating friends at a party and ensuring we track who sits where — and more importantly, where *your* friend sits! Let’s solve it step by step.

---

### Problem Statement 📝

We are given `n` friends attending a party, and they sit on the smallest unoccupied chair when they arrive. The arrival and leaving times of each friend are provided in a 2D array called `times`, and all arrival times are distinct. The task is to determine which chair the `targetFriend` will sit on.

**Input**: 

- `times[i] = [arrival_i, leaving_i]`: arrival and leaving times of the i-th friend.
- `targetFriend`: the friend we want to track.

**Output**: The number of the chair that the `targetFriend` sits on.

---

### Example 🧑‍🤝‍🧑

#### Example 1:
```
Input: times = [[1,4],[2,3],[4,6]], targetFriend = 1
Output: 1
```
**Explanation**: 
- Friend 0 arrives at time 1 and sits on chair 0.
- Friend 1 arrives at time 2 and sits on chair 1.
- Friend 1 leaves at time 3, so chair 1 becomes empty.
- Friend 0 leaves at time 4, so chair 0 becomes empty.
- Friend 2 arrives at time 4 and sits on chair 0.

Since friend 1 sat on chair 1, we return **1**.

#### Example 2:
```
Input: times = [[3,10],[1,5],[2,6]], targetFriend = 0
Output: 2
```

---

### Naive Solution 🔧

The simplest approach involves simulating the process step by step. We sort the friends by their arrival times, keep track of available chairs, and assign each friend the smallest available chair. As soon as a friend leaves, their chair becomes available again.

Steps:
1. **Sort** the friends by their arrival times.
2. For each friend:
   - Find the smallest available chair.
   - Assign it to the arriving friend.
   - Mark the chair as available when they leave.

#### Python Code 🐍
```python
import heapq

def smallestChair(times, targetFriend):
    n = len(times)
    
    # Add indices to times and sort by arrival times
    indexed_times = sorted(enumerate(times), key=lambda x: x[1][0])
    
    available_chairs = []
    heapq.heapify(available_chairs)
    occupied = []
    
    for i in range(n):
        heapq.heappush(available_chairs, i)
    
    events = []
    
    # Insert both arrival and departure events
    for idx, (arr, leave) in indexed_times:
        events.append((arr, 1, idx))
        events.append((leave, -1, idx))
    
    events.sort()
    
    chair_assignment = {}
    
    for time, event, friend in events:
        if event == 1:  # Arrival event
            chair = heapq.heappop(available_chairs)
            chair_assignment[friend] = chair
            if friend == targetFriend:
                return chair
        else:  # Departure event
            heapq.heappush(available_chairs, chair_assignment[friend])
```

#### Time Complexity ⏳
- Sorting friends by their arrival times takes **O(n log n)**.
- Each chair assignment or release operation takes **O(log n)** due to the heap structure.

Thus, the overall time complexity is **O(n log n)**.

---

### Optimized Solution 🚀

The naive solution is already optimized since we use heaps to manage available chairs efficiently. The use of heaps allows us to always grab the smallest unoccupied chair and return it back when a friend leaves.

---

### Example Walkthrough 🚶‍♂️

Let’s walk through an example to better understand the process:

For `times = [[1,4], [2,3], [4,6]]` and `targetFriend = 1`:

1. **Initial state**: Chairs are [0, 1, 2, ...] (infinite).
2. **At time 1**: Friend 0 arrives and sits on chair 0.
3. **At time 2**: Friend 1 arrives and sits on chair 1 (the next available chair).
4. **At time 3**: Friend 1 leaves, and chair 1 becomes available.
5. **At time 4**: Friend 2 arrives and takes chair 0 (smallest available).
6. Since Friend 1 sat on chair 1, the answer is **1**.

---

### Conclusion 🏁

This problem demonstrates a fun application of heaps to efficiently manage available resources (in this case, chairs). The solution can scale well for larger inputs, and the heap operations allow us to always return the smallest available option at any time.
