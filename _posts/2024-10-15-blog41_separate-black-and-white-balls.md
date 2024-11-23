---
layout: post  
title: "#41 2938. ⚫⚪ Separate Black and White Balls: Efficient Grouping with Minimal Swaps 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Two Pointers, String, Greedy]
---

In this problem, you're tasked with grouping black ⚫ and white ⚪ balls represented as a binary string. The goal is to move all the black balls to the right side and all the white balls to the left side with the minimum number of adjacent swaps.

### 📜 Problem Statement
You are given a 0-indexed binary string `s` of length `n`, where:
- `'1'` (⚫) represents a black ball.
- `'0'` (⚪) represents a white ball.

In each step, you can choose two adjacent balls and swap them. The objective is to group all the black balls (⚫) to the right and all the white balls (⚪) to the left with the **minimum number of swaps**.

#### Constraints:
- `1 <= n == s.length <= 10^5`
- `s[i]` is either `'0'` or `'1'`.

### 🌟 Example:
**Example 1**:  
Input: `s = "101"`  
Output: `1`  
Explanation: You can swap `s[0]` with `s[1]` to get `"011"`. It requires 1 step.

**Example 2**:  
Input: `s = "100"`  
Output: `2`  
Explanation:
1. Swap the first and second balls (`"100"` -> `"010"`).
2. Swap the second and third balls (`"010"` -> `"001"`).

**Example 3**:  
Input: `s = "0111"`  
Output: `0`  
Explanation: The black balls are already grouped together on the right.

### 🔍 Approach & Intuition

The goal is to group all the black balls to the right while minimizing the number of adjacent swaps. A key insight is to count the number of white balls (⚪) a black ball (⚫) has to pass over in order to be grouped correctly.

Instead of moving balls explicitly, we use a counting approach:
1. Traverse the string from right to left.
2. Maintain a count of white balls (⚪) encountered.
3. Every time you encounter a black ball (⚫), add the number of white balls (⚪) encountered so far to the total number of swaps.
   
This method gives the exact number of swaps needed without actually performing the swaps.

### 💻 Python Code:

```python
class Solution:
    def minimumSteps(self, s: str) -> int:
        counter = 0  # To count white balls (0's)
        steps = 0    # To count the minimum number of swaps
        start = -1   # Start from the right end of the string
        
        # Traverse the string from right to left
        for _ in range(len(s)):
            if s[start] == '0':  # Encounter a white ball (⚪)
                counter += 1
            else:  # Encounter a black ball (⚫)
                steps += counter  # Add the number of white balls passed over
            start -= 1  # Move left
        
        return steps
```

### 🕹️ Example Walkthrough

Let's walk through the example `s = "11000111"` step by step:

1. **Initial string**: `s = "11000111"`
   - Start from the right end (`start = -1`) with `counter = 0` and `steps = 0`.

2. **Step 1**:
   - Current character is `'1'` (⚫). No swaps required, but this black ball will need to pass over white balls later.
   - `counter = 0`, `steps = 0`.

3. **Step 2**:
   - Current character is `'1'` (⚫). No swaps required yet.
   - `counter = 0`, `steps = 0`.

4. **Step 3**:
   - Current character is `'1'` (⚫). No swaps required yet.
   - `counter = 0`, `steps = 0`.

5. **Step 4**:
   - Current character is `'0'` (⚪). Increase the white ball counter.
   - `counter = 1`, `steps = 0`.

6. **Step 5**:
   - Current character is `'0'` (⚪). Increase the white ball counter.
   - `counter = 2`, `steps = 0`.

7. **Step 6**:
   - Current character is `'0'` (⚪). Increase the white ball counter.
   - `counter = 3`, `steps = 0`.

8. **Step 7**:
   - Current character is `'1'` (⚫). There are 3 white balls (⚪) to pass over, so we add `counter = 3` to `steps`.
   - `counter = 3`, `steps = 3`.

9. **Step 8**:
   - Current character is `'1'` (⚫). There are still 3 white balls (⚪) to pass over, so we add `counter = 3` to `steps`.
   - Final `counter = 3`, `steps = 6`.

**Final Result**: The minimum number of swaps required to group all black balls to the right and all white balls to the left is `6`.

### ⏱️ Time Complexity

- **Time Complexity**: The solution iterates through the string once, so the time complexity is **O(n)**, where `n` is the length of the string.
- **Space Complexity**: We use only a few extra variables (`counter`, `steps`, `start`), so the space complexity is **O(1)**.

### 🔚 Conclusion

This problem demonstrates how a counting-based approach can effectively minimize swaps by keeping track of how many white balls (⚪) each black ball (⚫) needs to pass. By processing the string from right to left, we ensure an efficient solution with a time complexity of O(n). 🚀
