---
layout: post  
title: "#29 921. Minimum Add to Make Parentheses Valid 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [String, Stack, Greedy]
---

Have you ever encountered a string of parentheses that looks a bit off? The challenge here is to make sure every opening parenthesis `(` has a matching closing parenthesis `)`. We're tasked with calculating the minimum number of insertions needed to make the string valid. 🚀

---

### 📋 Problem Statement:
A parentheses string is valid if:
1. It's an empty string, or
2. It can be written as `AB` (A concatenated with B), where both A and B are valid strings, or
3. It can be written as `(A)`, where A is a valid string.

Given a parentheses string `s`, return the minimum number of insertions required to make it valid. You can insert parentheses at any position.

#### Example 1:
- **Input**: `s = "())"`
- **Output**: `1`

#### Example 2:
- **Input**: `s = "((("`
- **Output**: `3`

---

### 🔍 Basic Solution:

We need to scan through the string and track the number of unmatched parentheses. Two things can happen:
1. **Opening parenthesis `(`**: We need to keep track of it and wait for a matching closing parenthesis `)`.
2. **Closing parenthesis `)`**: If there’s no unmatched opening parenthesis, we need to insert one. Otherwise, we match it with an opening parenthesis.

Let’s count how many unmatched parentheses we encounter and use that count to return the number of insertions needed.

```python
def minAddToMakeValid(s: str) -> int:
    open_count = 0  # Count of unmatched '('
    close_needed = 0  # Count of ')' needed

    for char in s:
        if char == '(':
            open_count += 1
        elif char == ')':
            if open_count > 0:
                open_count -= 1  # Match with an open '('
            else:
                close_needed += 1  # No matching '('

    # Total moves: unmatched '(' + unmatched ')'
    return open_count + close_needed
```

#### ⏱ Time Complexity:
- **Time Complexity**: `O(n)` where `n` is the length of the string. We loop through the string once.
- **Space Complexity**: `O(1)` as we use only a few extra variables for counting.

---

### 🛠️ Optimized Solution:

The optimized solution is essentially the same as the basic solution, since we are already solving the problem in a single pass over the string with constant space. There’s no further optimization possible in terms of time complexity.

---

### 🔚 Conclusion:
In this problem, both the basic and optimized solutions have the same time complexity `O(n)`, as we process each character of the string only once. The key idea is to count the number of unmatched opening and closing parentheses and compute the required insertions based on that.

This problem helps us solidify the concept of stack-based problems, even though we solved it here with simple counting. 🌟
