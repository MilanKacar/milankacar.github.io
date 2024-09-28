---
layout: post
title: "#13 641. Design Circular Deque 🚀 "
categories: [LeetCode, Programming]
---

Hey there! 🎉 Today, we're diving into an exciting problem: designing a Circular Deque (double-ended queue) from scratch! The challenge requires implementing the deque and its fundamental operations like insertion, deletion, and fetching elements from the front or rear in a circular manner.

### Problem Statement 📜

We need to create a **MyCircularDeque** class that supports the following:

- **insertFront()**: Add an item at the front of the deque.
- **insertLast()**: Add an item at the rear.
- **deleteFront()**: Remove an item from the front.
- **deleteLast()**: Remove an item from the rear.
- **getFront()**: Return the front element.
- **getRear()**: Return the rear element.
- **isEmpty()**: Check if the deque is empty.
- **isFull()**: Check if the deque is full.

### Example 🤓

Here's an example of how the deque works:

```plaintext
Input
["MyCircularDeque", "insertLast", "insertLast", "insertFront", "insertFront", "getRear", "isFull", "deleteLast", "insertFront", "getFront"]
[[3], [1], [2], [3], [4], [], [], [], [4], []]

Output
[null, true, true, true, false, 2, true, true, true, 4]
```

### Solution 💡

We can solve this problem using an array to simulate the circular behavior of the deque. Let’s walk through the steps:

### 🛠️ Basic Python Solution

We use a fixed-size list to implement the circular deque. The list has a front pointer and a rear pointer. When the pointers reach the end of the list, they wrap around to the beginning, hence the "circular" behavior.

Here’s the Python code:

```python
class MyCircularDeque:

    def __init__(self, k: int):
        self.k = k
        self.deque = [-1] * k
        self.front = 0
        self.rear = -1
        self.size = 0

    def insertFront(self, value: int) -> bool:
        if self.isFull():
            return False
        self.front = (self.front - 1) % self.k
        self.deque[self.front] = value
        self.size += 1
        return True

    def insertLast(self, value: int) -> bool:
        if self.isFull():
            return False
        self.rear = (self.rear + 1) % self.k
        self.deque[self.rear] = value
        self.size += 1
        return True

    def deleteFront(self) -> bool:
        if self.isEmpty():
            return False
        self.front = (self.front + 1) % self.k
        self.size -= 1
        return True

    def deleteLast(self) -> bool:
        if self.isEmpty():
            return False
        self.rear = (self.rear - 1) % self.k
        self.size -= 1
        return True

    def getFront(self) -> int:
        if self.isEmpty():
            return -1
        return self.deque[self.front]

    def getRear(self) -> int:
        if self.isEmpty():
            return -1
        return self.deque[self.rear]

    def isEmpty(self) -> bool:
        return self.size == 0

    def isFull(self) -> bool:
        return self.size == self.k
```

### Explanation 🧑‍🏫

- **insertFront** and **insertLast** wrap around using modulo arithmetic to ensure circular behavior.
- **deleteFront** and **deleteLast** also handle the index updates in a circular manner.
- **getFront** and **getRear** return the respective elements.
- **isFull** and **isEmpty** are straightforward size checks.

### Time Complexity ⏳

The time complexity for each operation (insertion, deletion, fetching) is **O(1)** because we're using fixed-size array indexing and modulo operations, which are constant time.

### Conclusion 🎯

The basic solution to the Circular Deque problem is quite efficient, with all operations running in constant time **O(1)**. This makes it an optimal solution for scenarios where we need fast insertion, deletion, and access to both ends of the deque.

### Key Takeaways 📝

- Use modulo arithmetic to handle the circular behavior of the deque.
- All operations in the Circular Deque have constant time complexity **O(1)**.
- This problem teaches you the importance of handling circular data structures efficiently, which is critical in various real-world applications like caching and buffering.
