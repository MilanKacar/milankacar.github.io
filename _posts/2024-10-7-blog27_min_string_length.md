---
layout: post  
title: "#27 2696. Minimum String Length After Removing Substrings 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Easy
tags: [String, Stack, Simulation]
---

Given a string consisting of only uppercase English letters, we need to repeatedly remove the substrings "AB" and "CD" until no more of them exist. Our task is to find the minimum possible length of the string after all these operations.

---

### Problem Statement 📋
You are given a string `s` containing only uppercase English letters. You can perform an operation to remove any occurrence of the substrings "AB" or "CD". The string concatenates after each removal, and new "AB" or "CD" substrings may appear.

You need to return the minimum length of the resulting string.

#### Example 1:
- **Input**: `"ABFCACDB"`
- **Output**: `2`
- **Explanation**:  
  - Remove "AB", resulting in `"FCACDB"`.  
  - Remove "CD", resulting in `"FCAB"`.  
  - Remove "AB", resulting in `"FC"`.  
  The resulting length is 2.

#### Example 2:
- **Input**: `"ACBBD"`
- **Output**: `5`
- **Explanation**:  
  No "AB" or "CD" substrings can be removed, so the string length remains 5.

---

### Solution 💡

We can solve this problem using a **greedy approach** by continuously scanning the string and removing the first occurrence of either "AB" or "CD" until no more are found. Let's break it down step by step.

---

### Basic Solution 🛠️

In the basic approach, we keep removing substrings "AB" and "CD" by traversing the string repeatedly until no more removals can be performed.

#### Steps:
1. Traverse the string to find "AB" or "CD".
2. Remove the found substring.
3. Repeat until no more "AB" or "CD" can be removed.
4. Return the length of the final string.

#### Python Code 🐍
```python
def minLength(s: str) -> int:
    while "AB" in s or "CD" in s:
        s = s.replace("AB", "")
        s = s.replace("CD", "")
    return len(s)
```

---

### Optimized Solution 🚀

While the above solution works, we can further optimize by scanning the string only once and using a **stack** to handle the removals dynamically. The idea is to push characters to the stack and check if the top of the stack forms "AB" or "CD" with the current character. If so, pop the stack; otherwise, push the character.

#### Steps:
1. Initialize an empty stack.
2. Iterate through each character in the string:
   - If the top of the stack combined with the current character forms "AB" or "CD", pop the stack.
   - Otherwise, push the current character to the stack.
3. The remaining characters in the stack form the final string.
4. Return the stack's length.

#### Python Code 🐍
```python
def minLength(s: str) -> int:
    stack = []
    for char in s:
        if stack and ((stack[-1] == 'A' and char == 'B') or (stack[-1] == 'C' and char == 'D')):
            stack.pop()
        else:
            stack.append(char)
    return len(stack)
```

### Time Complexity ⏱️
Both the basic and optimized solutions have the same time complexity, but the optimized solution performs fewer unnecessary operations:

- **Time Complexity**: O(n), where `n` is the length of the string. We either iterate through the string several times (basic approach) or only once using a stack (optimized approach).

---

### Recursive Solution 🔄

We can also solve this problem recursively by reducing the string each time we encounter "AB" or "CD." The idea is to look for these substrings, remove them, and call the function recursively on the updated string until no more removals are possible.

#### Steps:
1. Base case: If no "AB" or "CD" exists in the string, return the length of the string.
2. Recursive step: Find the first occurrence of "AB" or "CD" and remove it, then recursively solve for the new string.
3. Continue this process until we cannot find any more "AB" or "CD".

#### Python Code 🐍
```python
def minLength(s: str) -> int:
    # Base case: if no "AB" or "CD" is found, return the string's length
    if "AB" not in s and "CD" not in s:
        return len(s)
    
    # Recursively remove the first occurrence of "AB" and "CD"
    if "AB" in s:
        s = s.replace("AB", "", 1)
    if "CD" in s:
        s = s.replace("CD", "", 1)
    
    # Recursively call the function again
    return minLength(s)
```

### Time Complexity ⏱️

- **Time Complexity**: In the worst case, we remove one "AB" or "CD" per recursive call, and each call scans through the string. So, the time complexity would be **O(n^2)**, where `n` is the length of the string.
- **Space Complexity**: O(n), due to recursion stack space.

---

### Conclusion 📝

We explored several solutions to minimize the string length:
- **Basic solution** with repeated string traversal.
- **Stack-based solution** for a single-pass approach.
- **Recursive solution**, which simplifies the problem by removing one occurrence of "AB" or "CD" and calling the function again.

While recursion works well conceptually, the stack-based approach remains more efficient due to its **O(n)** time complexity, making it the optimal choice in larger cases.


