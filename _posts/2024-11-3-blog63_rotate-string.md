---
layout: post  
title: "#63 🔄 796. Rotate String 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Easy
tags: [String, String Matching]
---

Imagine you have two strings, `s` and `goal`, and you want to know if you can rotate `s` some number of times so it matches `goal`. Let's dive into how to solve this with some cool insights and a clean algorithm! 🌐✨

---

## 📜 Problem Statement

Given two strings `s` and `goal`, your task is to check if `s` can be rotated to become `goal`. A rotation (or shift) moves the first character of `s` to the last position. If we perform enough rotations on `s`, we may or may not match `goal`.

### Constraints:

- `1 <= s.length, goal.length <= 100`
- Both `s` and `goal` consist of lowercase English letters.

---

## 🧩 Example Scenarios

### Example 1
**Input**:  
`s = "abcde"`  
`goal = "cdeab"`  
**Output**:  
`True`

**Explanation**:  
After rotating `s` twice, it becomes `"cdeab"`, which matches `goal`!

### Example 2
**Input**:  
`s = "abcde"`  
`goal = "abced"`  
**Output**:  
`False`

**Explanation**:  
No amount of rotation on `s` can turn it into `goal`.

---

## 🛠️ Solution Approaches

### 👶 Basic Solution (Intuitive Approach)

The most intuitive way to solve this is by rotating the string `s` manually and checking if it ever matches `goal`.

1. **Initialize** by checking if `s` and `goal` have the same length. If not, return `False`.
2. **Rotate `s`** by moving its first character to the end, and check if this equals `goal`.
3. **Repeat** until we either find a match or exhaust all possible rotations.

This approach is straightforward, but it may not be optimal for larger strings. Let’s analyze its time complexity.

#### 🕒 Time Complexity of Basic Solution

For each rotation, we compare `s` with `goal`, which takes `O(n)` time. If we perform `n` rotations, the overall time complexity becomes `O(n^2)`.

#### Python Code (Basic Solution)

```python
def rotate_string(s: str, goal: str) -> bool:
    if len(s) != len(goal):
        return False
    # Try all rotations
    for i in range(len(s)):
        # Rotate by slicing
        rotated = s[i:] + s[:i]
        if rotated == goal:
            return True
    return False
```

---

### 🚀 Optimized Solution (Concatenate Trick)

Let’s optimize our approach with a clever trick! Instead of rotating `s` multiple times, consider the **concatenation of `s` with itself**. When `s` is doubled (i.e., `s + s`), it contains all possible rotations of `s` as its substrings. For example:

- If `s = "abcde"`, then `s + s = "abcdeabcde"` contains rotations like `"bcdea"`, `"cdeab"`, etc.

With this trick, our solution simplifies to just checking if `goal` is a substring of `s + s`.

#### Steps:
1. **Check Lengths**: If `s` and `goal` don’t have the same length, return `False`.
2. **Concatenate `s` with itself**: Create a new string `doubled_s = s + s`.
3. **Check if `goal` is a substring of `doubled_s`**: If it is, then `s` can be rotated to match `goal`.

#### 🕒 Time Complexity of Optimized Solution

This approach takes `O(n)` to create `doubled_s` and `O(n)` to check if `goal` is a substring of `doubled_s`, so the overall complexity is `O(n)`.

#### Python Code (Optimized Solution)

```python
def rotate_string(s: str, goal: str) -> bool:
    if len(s) != len(goal):
        return False
    doubled_s = s + s
    return goal in doubled_s
```

---

## 🔍 Example Walkthrough

Let’s go through an example to see this solution in action.

### Walkthrough Example (Larger String)

#### Input
`s = "abcdefg"`  
`goal = "efgabcd"`

1. **Concatenate `s` with itself**:  
   `doubled_s = "abcdefgabcdefg"`

2. **Check if `goal` is in `doubled_s`**:  
   - `doubled_s` contains all rotations of `s`.
   - Since `goal = "efgabcd"` is a substring of `doubled_s`, we can confirm that `s` can be rotated to become `goal`.

3. **Return Result**:  
   Since `goal` is in `doubled_s`, we return `True`.

---

## 🧪 Edge Cases

1. **Different Lengths**: If `s` and `goal` have different lengths, return `False`.
2. **Identical Strings**: If `s` and `goal` are already the same, return `True`.
3. **Single Character Strings**: If `s` and `goal` are both one character and identical, return `True`; otherwise, return `False`.
4. **No Possible Rotation**: If `goal` is a unique combination that doesn’t appear in `doubled_s`, return `False`.

---

## 💡 Conclusion

In this problem, we explored two different approaches to determine if a string can be rotated to match another string:

- **Basic Solution**: Manually rotating and comparing, which has `O(n^2)` complexity.
- **Optimized Solution**: Using the concatenation trick, which simplifies the problem to a substring search with `O(n)` complexity.

The concatenation method is clearly the more efficient solution. This technique of concatenating a string with itself is a powerful tool for rotation-based problems.

---

### 🎉 Summary

This problem highlights the beauty of **thinking creatively** in coding—by realizing that every possible rotation of `s` is hidden within `s + s`, we unlock an optimized solution that’s both elegant and efficient. Try applying this trick to other rotation problems, and see the magic! ✨
