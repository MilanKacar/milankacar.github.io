---
layout: post
title: "#30 962. Maximum Width Ramp 🧠🚀"
categories: [LeetCode, Programming]
---

## 🚀 LeetCode Problem #962: Maximum Width Ramp

In this post, we’ll explore a fun and interesting problem where we need to find the **maximum width ramp** in an array. A ramp is defined as a pair of indices `(i, j)` such that `i < j` and `nums[i] <= nums[j]`. Our goal is to calculate the largest difference `j - i` where this condition holds. Let’s dive in! 🌊

### 📝 Problem Statement

A ramp in an integer array `nums` is a pair `(i, j)` such that `i < j` and `nums[i] <= nums[j]`. The width of such a ramp is defined as `j - i`. 

Given an integer array `nums`, return the maximum width of a ramp in `nums`. If no ramp exists, return `0`.

#### Constraints:
- `2 <= nums.length <= 5 * 10^4`
- `0 <= nums[i] <= 5 * 10^4`

### 🌟 Example 1:
```plaintext
Input: nums = [6,0,8,2,1,5]
Output: 4
Explanation: The maximum width ramp is achieved at (i, j) = (1, 5): nums[1] = 0 and nums[5] = 5.
```

### 🌟 Example 2:
```plaintext
Input: nums = [9,8,1,0,1,9,4,0,4,1]
Output: 7
Explanation: The maximum width ramp is achieved at (i, j) = (2, 9): nums[2] = 1 and nums[9] = 1.
```

---

### 🛠️ Basic Solution

One straightforward approach to solve this problem is by **brute force**. We can check every possible pair `(i, j)` to find the one that satisfies `nums[i] <= nums[j]` and has the maximum difference `j - i`.

#### Python Code:
```python
def maxWidthRamp(nums):
    max_width = 0
    for i in range(len(nums)):
        for j in range(i + 1, len(nums)):
            if nums[i] <= nums[j]:
                max_width = max(max_width, j - i)
    return max_width
```

### ⏳ Time Complexity of Basic Solution
- The time complexity of this solution is **O(n²)**, where `n` is the length of the array. This is because we are checking all pairs `(i, j)`, resulting in a nested loop.

---

### ⚡ Optimized Solution

To improve efficiency, we can use an approach involving **monotonic stacks**. The idea is to preprocess the array to find the smallest indices where `nums[i]` is potentially part of a ramp. Then, we can move through the array from right to left, checking for valid pairs that maximize `j - i`.

#### Python Code:
```python
def maxWidthRamp(nums):
    stack = []
    max_width = 0
    
    # Build the stack of possible ramp start indices
    for i in range(len(nums)):
        if not stack or nums[i] < nums[stack[-1]]:
            stack.append(i)
    
    # Traverse the array from the end to the beginning
    for j in reversed(range(len(nums))):
        while stack and nums[stack[-1]] <= nums[j]:
            max_width = max(max_width, j - stack.pop())
    
    return max_width
```

### ⏳ Time Complexity of Optimized Solution
- The time complexity of this optimized solution is **O(n)**. We first build the stack with one pass through the array and then check pairs with another pass, making it linear.

---

### 💡 Conclusion

In this problem, we saw two approaches to solving the **Maximum Width Ramp**. While the brute force solution gave us an intuitive understanding of the problem, it wasn’t efficient for large inputs due to its **O(n²)** time complexity. The optimized solution using a **monotonic stack** improved this to **O(n)**, making it much more suitable for large arrays.

Happy coding! 😊
