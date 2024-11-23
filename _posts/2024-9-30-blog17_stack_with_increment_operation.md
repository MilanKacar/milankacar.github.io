---
layout: post  
title: "#17 1381. Design a Stack With Increment Operation 🚀"  
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Stack, Design]
---

Hey 😄 Today, we'll be solving an interesting stack problem: designing a stack that supports **increment operations** in addition to the usual `push` and `pop`. Ready? Let’s dive in!

### Problem Statement 📝

We need to implement a **CustomStack** class with the following features:

1. **CustomStack(int maxSize)**: Initializes the stack with a maximum size.
2. **void push(int x)**: Adds `x` to the top of the stack if the stack is not full.
3. **int pop()**: Removes and returns the top element of the stack. Returns `-1` if the stack is empty.
4. **void inc(int k, int val)**: Increments the bottom `k` elements by `val`. If the stack has fewer than `k` elements, increment all elements.

### Example 🧐

```plaintext
Input
["CustomStack", "push", "push", "pop", "push", "push", "push", "increment", "increment", "pop", "pop", "pop", "pop"]
[[3], [1], [2], [], [2], [3], [4], [5, 100], [2, 100], [], [], [], []]

Output
[null, null, null, 2, null, null, null, null, null, 103, 202, 201, -1]
```

- **Step-by-step**: 
  - `push(1)`: Stack becomes `[1]`.
  - `push(2)`: Stack becomes `[1, 2]`.
  - `pop()`: Returns `2`, stack becomes `[1]`.
  - `push(2)`: Stack becomes `[1, 2]`.
  - `push(3)`: Stack becomes `[1, 2, 3]`.
  - `push(4)`: Stack remains `[1, 2, 3]` (since maxSize is `3`).
  - `increment(5, 100)`: Stack becomes `[101, 102, 103]` (all elements incremented by 100).
  - `increment(2, 100)`: Stack becomes `[201, 202, 103]`.
  - `pop()`: Returns `103`, stack becomes `[201, 202]`.
  - `pop()`: Returns `202`, stack becomes `[201]`.
  - `pop()`: Returns `201`, stack becomes `[]`.
  - `pop()`: Returns `-1` (stack is empty).

### 🛠️ Basic Python Solution

Let’s start by implementing a **simple solution** using a list. For the `push`, `pop`, and `increment` operations, we will perform actions directly on the stack list.

```python
class CustomStack:
    def __init__(self, maxSize: int):
        self.stack = []
        self.maxSize = maxSize

    def push(self, x: int) -> None:
        if len(self.stack) < self.maxSize:
            self.stack.append(x)

    def pop(self) -> int:
        if self.stack:
            return self.stack.pop()
        return -1

    def increment(self, k: int, val: int) -> None:
        limit = min(k, len(self.stack))
        for i in range(limit):
            self.stack[i] += val
```

### Explanation 🧑‍🏫

- **push(x)**: We check if the current stack size is less than `maxSize` and append `x` if it’s not full.
- **pop()**: If the stack is non-empty, we remove and return the top element. If it’s empty, return `-1`.
- **increment(k, val)**: We loop over the bottom `k` elements (or all elements if `k > len(stack)`) and add `val` to each of them.

### Time Complexity ⏳

- **Push/Pop**: Both operations take **O(1)** time since we either append or pop an element from the stack.
- **Increment**: This operation can take **O(k)** in the worst case when `k` is close to the size of the stack, as we iterate over the bottom `k` elements.

### 🏎️ Optimized Python Solution

The basic solution works, but we can **optimize the increment operation** to make it faster. Instead of incrementing every element during each call to `increment`, we can use a lazy increment approach. We maintain an extra list to record pending increments.

```python
class CustomStack:
    def __init__(self, maxSize: int):
        self.stack = []
        self.incrementArr = [0] * maxSize
        self.maxSize = maxSize

    def push(self, x: int) -> None:
        if len(self.stack) < self.maxSize:
            self.stack.append(x)

    def pop(self) -> int:
        if not self.stack:
            return -1
        idx = len(self.stack) - 1
        res = self.stack.pop() + self.incrementArr[idx]
        if idx > 0:
            self.incrementArr[idx - 1] += self.incrementArr[idx]
        self.incrementArr[idx] = 0
        return res

    def increment(self, k: int, val: int) -> None:
        limit = min(k, len(self.stack))
        if limit > 0:
            self.incrementArr[limit - 1] += val
```

### Explanation of the Optimized Solution 🚀

- **Lazy Increment**: We keep a separate `incrementArr` array to track pending increments for each element. Instead of incrementing the elements directly, we add the increment values lazily. The increment is only applied when an element is popped.
- **pop()**: When popping an element, we check its pending increment (from `incrementArr`) and adjust the next element’s increment.
  
### Time Complexity ⏱️

- **Push/Pop**: Still takes **O(1)** time since appending or popping elements, and updating the lazy increment array happens in constant time.
- **Increment**: Now takes **O(1)** time because we only modify the increment array instead of iterating through the stack elements.

### Conclusion 🎯

The optimized solution improves the **increment** operation from **O(k)** to **O(1)** using a lazy increment technique. All operations are now performed in constant time, making the solution efficient and scalable for larger inputs.

- **Basic Solution**: Works with **O(k)** time for the `increment` function.
- **Optimized Solution**: Improves the `increment` function to **O(1)** using lazy updates.

That’s it for this problem! Feel free to try both approaches and see how they perform in practice. Happy coding! 💻🎉
