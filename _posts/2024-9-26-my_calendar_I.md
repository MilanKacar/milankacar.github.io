---
layout: post
title: " #729 My Calendar I 🚀 "
categories: LeetCode Programming
---

In this post, we will tackle the problem of implementing a **simple calendar** that allows booking events without causing a **double booking**. We'll explore the solution and discuss the time complexity involved. Let's dive into it!

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

## 🐢 Solution

A **simple approach** is to maintain a list of booked intervals and check for overlaps every time a new event is requested. If no overlap is found, the new event is added.

### Approach 💡

1. **Initialize an empty list** to store the events.
2. **Check for Overlaps**: For each new booking request, iterate through the existing events and check if it overlaps with any of them.
3. **Add the Event**: If no overlap is found, add the event to the calendar.

### 🧑‍💻 Code Implementation

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

### Explanation:

- **Initialization**: We create an empty list `self.calendar` to hold all booked intervals.
- **Checking Overlaps**: For each new booking request, we iterate through the already booked intervals and check if the new event overlaps with any existing one. This is done by checking if the new event ends before the start of an existing event or starts after the end of an existing event. If neither of these conditions holds, an overlap occurs, and the booking request is rejected.
- **Booking**: If no overlap is found, we append the new event to the list of booked events.

---

## 🕰️ Time Complexity

- **For each booking**, we need to check for overlaps with all previously booked events. Since there are `n` bookings, and we compare the new event against all of them, the **time complexity** for each booking is **O(n)**.
  
- Therefore, the total time complexity for making `n` bookings is **O(n²)**. This can be inefficient as the number of bookings increases, especially for large inputs. But in this case this is not the problem since the day has only 24 hours and our granularity is `HH:MM`.

---

## 📝 Conclusion

The **simple solution** discussed here is clear and easy to implement. While it has a **time complexity of O(n²)**, it works well for small to moderate inputs. For larger datasets, you might consider optimizing the solution by using data structures like balanced binary search trees (BST) to reduce the complexity to **O(log n)** for each booking.

This problem showcases how even simple logic can become inefficient as input sizes grow, highlighting the importance of time complexity considerations when designing algorithms. Happy coding! 😄
