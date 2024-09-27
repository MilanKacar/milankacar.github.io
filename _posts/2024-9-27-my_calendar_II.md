---
layout: post
title: "#8 731. My Calendar II 🚀 "
categories: LeetCode Programming
---

In this post, we will tackle the problem of implementing a **calendar** that allows booking events without causing a **triple booking**. After exploring a basic solution, we'll introduce an **optimized solution using a sweep line algorithm** to improve efficiency for large datasets. Let’s dive in!

## 📋 Problem Statement

You are implementing a program to use as your calendar. We can add a new event if adding the event will not cause a **triple booking**.

A triple booking happens when three events overlap, i.e., they have some common time interval. The event can be represented as a pair of integers `start` and `end` that indicates a booking on the half-open interval `[start, end)`, meaning `start` is inclusive but `end` is exclusive.

Implement the `MyCalendarTwo` class:

- `MyCalendarTwo()`: Initializes the calendar object.
- `boolean book(int start, int end)`: Returns `true` if the event can be added to the calendar successfully without causing a triple booking. Otherwise, return `false` and do not add the event.

### 💡 Example 1:
```text
Input: ["MyCalendarTwo", "book", "book", "book", "book", "book", "book"]
       [[], [10, 20], [50, 60], [10, 40], [5, 15], [5, 10], [25, 55]]
Output: [null, true, true, true, false, true, true]

Explanation:
- myCalendarTwo.book(10, 20) -> true, event booked.
- myCalendarTwo.book(50, 60) -> true, event booked.
- myCalendarTwo.book(10, 40) -> true, double booking allowed.
- myCalendarTwo.book(5, 15) -> false, triple booking not allowed.
- myCalendarTwo.book(5, 10) -> true, no overlap with existing double bookings.
- myCalendarTwo.book(25, 55) -> true, no triple booking occurs.
```

---

## 🐢 Basic Solution

A **basic approach** is to maintain two lists: one for **single bookings** and another for **double bookings**. Each time we try to book an event, we check if it would overlap with any existing **double-booked** intervals. If not, we update the list of **double bookings** with any overlaps from **single bookings**.

### Code Implementation

```python
class MyCalendarTwo:
    def __init__(self):
        self.single_bookings = []
        self.double_bookings = []

    def book(self, start, end):
        # Check for triple booking
        for s, e in self.double_bookings:
            if max(start, s) < min(end, e):  # Overlap detected with double booking
                return False
        
        # Add new double bookings based on overlaps with single bookings
        for s, e in self.single_bookings:
            if max(start, s) < min(end, e):
                self.double_bookings.append((max(start, s), min(end, e)))

        # Add the event as a single booking
        self.single_bookings.append((start, end))
        return True
```

### 🕰️ Time Complexity

- Each booking requires checking overlap with both single and double bookings, leading to **O(n²)** time complexity after `n` bookings.

This approach works for small inputs but becomes inefficient as the number of events grows.

---

## ⚡ Optimized Solution: Using a Sweep Line Algorithm

To improve efficiency, we can use a **sweep line algorithm**. The idea is to increment and decrement counters at the start and end of each event. By tracking how many events are overlapping at any moment, we can detect triple bookings in **O(n log n)** time.

### Optimized Approach 💡

1. **Track Events Using Counters**: For each new booking, we increment the counter at the start and decrement at the end.
2. **Detect Triple Bookings**: As we sweep through the sorted events, we ensure that no moment has three overlapping events.

### 🚀 Code Implementation

```python
from sortedcontainers import SortedDict

class MyCalendarTwo:
    def __init__(self):
        self.events = SortedDict()

    def book(self, start, end):
        # Increment at the start and decrement at the end
        self.events[start] = self.events.get(start, 0) + 1
        self.events[end] = self.events.get(end, 0) - 1
        
        # Sweep through the events and check overlaps
        active_events = 0
        for count in self.events.values():
            active_events += count
            if active_events >= 3:
                # Revert changes if triple booking detected
                self.events[start] -= 1
                self.events[end] += 1
                if self.events[start] == 0:
                    del self.events[start]
                if self.events[end] == 0:
                    del self.events[end]
                return False
        return True
```

### Explanation:

- **Increment/Decrement**: When booking an event `[start, end)`, we increment at `start` and decrement at `end`.
- **Sweep Line**: We then iterate through the sorted time points and check if the number of overlapping events exceeds two.
- **Efficiency**: By only checking overlaps through sweeping, we reduce the complexity of each booking to **O(log n)**.

---

## 🕰️ Time Complexity of Optimized Solution

- **Insertion and Search**: Both operations take **O(log n)** due to the sorted data structure (`SortedDict`).
- **Overall Complexity**: Since each booking operation is **O(log n)**, for `n` bookings, the total time complexity is **O(n log n)**.

---

## 📝 Conclusion

### 🐢 Basic Solution:
- **Time Complexity**: O(n²)  
  Simple but inefficient for larger datasets.

### ⚡ Optimized Solution (Using Sweep Line):
- **Time Complexity**: O(n log n)  
  By tracking events using a sweep line algorithm, we can efficiently detect overlaps, making this solution much more suitable for large datasets.

The optimized solution significantly reduces the complexity, making it ideal for handling a large number of bookings. When working with performance-critical applications, it’s crucial to choose the right approach to ensure scalability. Happy coding! 😄
