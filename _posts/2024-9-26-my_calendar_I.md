---
layout: post  
title: "#8 729. My Calendar I 🚀 "
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Binary Search, Design, Segment Tree, Ordered Set]
---

In this post, we will tackle the problem of implementing a **simple calendar** that allows booking events without causing a **double booking**. After exploring a basic solution, we'll introduce an **optimized solution using a balanced binary search tree (BST)** to improve efficiency for large datasets. Let’s dive in!

## 📋 Problem Statement

You are implementing a program to use as your calendar. We can add a new event if adding the event will not cause a **double booking**.

A double booking happens when two events overlap, i.e., they have some common time interval. The event can be represented as a pair of integers `start` and `end` that indicates a booking on the half-open interval `[start, end)`, meaning `start` is inclusive but `end` is exclusive.

Implement the `MyCalendar` class:

- `MyCalendar()`: Initializes the calendar object.
- `boolean book(int start, int end)`: Returns `true` if the event can be added to the calendar successfully without causing a double booking. Otherwise, return `false` and do not add the event.

### 💡 Example 1:
```text
Input: ["MyCalendar", "book", "book", "book"]
       [[], [10, 20], [15, 25], [20, 30]]
Output: [null, true, false, true]

Explanation:
- myCalendar.book(10, 20) -> true, event booked.
- myCalendar.book(15, 25) -> false, overlaps with the previous event.
- myCalendar.book(20, 30) -> true, no overlap with the previous event.
```

---

## 🐢 Basic Solution

A **basic approach** is to maintain a list of booked intervals and check for overlaps every time a new event is requested. While simple, this approach has a time complexity of **O(n²)** because it checks every existing event, making it inefficient for large inputs.

### Code Implementation

```python
class MyCalendar:

    def __init__(self):
        self.calendar = []

    def book(self, start: int, end: int) -> bool:
        for s, e in self.calendar:
            if not (end <= s or start >= e):
                return False
        self.calendar.append((start, end))
        return True
```

### 🕰️ Time Complexity

- For each booking, we check for overlaps with all previously booked events, leading to **O(n)** time for each booking.
- After `n` bookings, the total time complexity is **O(n²)**.

This approach works for small input sizes but can be slow when dealing with larger datasets.

---

## ⚡ Optimized Solution: Using Balanced BST (TreeMap)

To improve efficiency, we can use a **balanced binary search tree (BST)** to store events in a sorted manner and efficiently check for overlaps. In Python, we can use the `SortedDict` from the `sortedcontainers` module, which mimics a balanced BST and supports **O(log n)** time complexity for insertion and search operations.

### Optimized Approach 💡

1. **Use a BST**: Store the events in a balanced binary search tree, sorted by their start times.
2. **Check for Overlaps Efficiently**: For each new booking, we only need to check the event that comes just before and just after the new event, as they are the only ones that could potentially overlap.

### 🚀 Code Implementation

```python
from sortedcontainers import SortedDict

class MyCalendar:

    def __init__(self):
        self.calendar = SortedDict()

    def book(self, start: int, end: int) -> bool:
        # Find the nearest event before the new event
        idx = self.calendar.bisect_right(start)
        
        # Check if the previous event overlaps with the new one
        if idx > 0 and self.calendar.peekitem(idx - 1)[1] > start:
            return False
        
        # Check if the next event overlaps with the new one
        if idx < len(self.calendar) and self.calendar.peekitem(idx)[0] < end:
            return False
        
        # No overlaps found, insert the new event
        self.calendar[start] = end
        return True
```

### Explanation:

- **Initialization**: We use `SortedDict` to store the calendar events. This data structure automatically keeps the events sorted by their start time.
- **Checking Overlaps**:
  - We first find the nearest event **before** the new event using the `bisect_right` method.
  - If the previous event ends after the new event starts, or the next event starts before the new event ends, there is an overlap.
  - If no overlap is found, we add the new event to the calendar.
  
### 📝 Example Walkthrough

Let's say we want to book events with the following times: `[10, 20]`, `[15, 25]`, and `[20, 30]`.

1. **First booking**: `[10, 20]` – No events yet, so it’s booked successfully.
2. **Second booking**: `[15, 25]` – The previous event `[10, 20]` overlaps because `15 < 20`. Hence, booking is rejected.
3. **Third booking**: `[20, 30]` – No overlap with previous or next event, so it’s booked successfully.

---

## 🕰️ Time Complexity of Optimized Solution

- **Insertion and Search**: Both operations take **O(log n)** due to the use of a balanced BST (`SortedDict`).
- **Overall Complexity**: Since each booking operation is **O(log n)**, for `n` bookings, the total time complexity is **O(n log n)**, which is much faster than the basic solution for large inputs.

---

## 📝 Conclusion

### 🐢 Basic Solution:
- **Time Complexity**: O(n²)  
  Simple but inefficient for larger datasets.

### ⚡ Optimized Solution (Using BST):
- **Time Complexity**: O(n log n)  
  By using a balanced binary search tree (BST), we can efficiently check for overlaps and insert new bookings, making this solution much more suitable for large datasets.

The optimized approach significantly reduces the complexity from **O(n²)** to **O(n log n)**, making it ideal for applications with a large number of events. When working with large-scale systems, it's important to consider such optimizations to improve performance. Happy coding! 😄
