---
layout: post
title: "#48 1593. Split a String Into the Max Number of Unique Substrings 🧠🚀"
categories: [LeetCode, Programming]
---


In this problem, we are tasked with maximizing the number of **unique substrings** into which we can split a given string. The key is to ensure that no substring appears more than once. We can explore different ways to split the string, but we must always guarantee that the resulting substrings are distinct. Let’s walk through this challenge with plenty of examples and explore the approach that will help us crack this problem efficiently using **backtracking**!

---

### 📄 **Problem Statement**

Given a string `s`, return the maximum number of unique substrings that the string can be split into. The substrings must be **non-empty** and their concatenation must form the original string. A substring is any contiguous sequence of characters within the string, but they must all be **unique** in the final split.

---

### 🧪 **Example 1**

- **Input:** `s = "ababccc"`
- **Output:** `5`
- **Explanation:** One valid way to split the string is `["a", "b", "ab", "c", "cc"]`. If we try to split it like `["a", "b", "a", "b", "c", "cc"]`, it’s **invalid** because both "a" and "b" are repeated.

---

### 🧪 **Example 2**

- **Input:** `s = "aba"`
- **Output:** `2`
- **Explanation:** A possible way to split is `["a", "ba"]`.

---

### 🧪 **Example 3**

- **Input:** `s = "aa"`
- **Output:** `1`
- **Explanation:** It is impossible to split the string into more than one unique substring.

---

### 📝 **Constraints**:
- `1 <= s.length <= 16`
- The string `s` contains only lowercase English letters.

---

### 🔍 **Breaking Down the Problem**
The problem revolves around ensuring that every substring in our final result is unique. Given that the length of the string can be at most 16, brute-force approaches are acceptable here. A **backtracking** solution works well because we can explore all potential ways to split the string and keep track of unique substrings.

### **Key Observations**:
1. The string can be split in multiple ways.
2. We can generate substrings starting from the first character and then try all possible splits.
3. We need to track the substrings we’ve already used to avoid duplicates.
4. Backtracking allows us to explore all valid splits and backtrack when necessary.

---

### 🛠️ **Backtracking Solution**

Backtracking is a natural fit for this type of problem because we need to try out different options and discard those that don’t meet the criteria. Let’s break down the steps:

1. **Track Unique Substrings**: Use a set to track the substrings we've used.
2. **Split the String**: For each index in the string, split it and check if the current substring is unique.
3. **Recursion and Backtracking**: Recursively check the next part of the string, keeping track of the number of unique substrings. If a split leads to a valid solution, we update our result.
4. **Maximize the Count**: The goal is to maximize the count of unique substrings at each recursion level.

---

### ⏳ **Time Complexity Analysis**

The time complexity of this solution is **O(2^n)**, where `n` is the length of the string. This is because, at each step, we can either choose to include a substring or not, leading to exponential branching.

- **Space Complexity**: We use extra space for the set that tracks unique substrings, and the recursion stack also consumes space, leading to a space complexity of **O(n)**.

---

### 🧑‍💻 **Python Code**:

```python
def maxUniqueSplit(s: str) -> int:
    def backtrack(start, seen):
        if start == len(s):
            return 0
        max_splits = 0
        for end in range(start + 1, len(s) + 1):
            substring = s[start:end]
            if substring not in seen:
                seen.add(substring)
                max_splits = max(max_splits, 1 + backtrack(end, seen))
                seen.remove(substring)  # Backtrack by removing the substring
        return max_splits

    return backtrack(0, set())
```

---

### 🔎 **Walkthrough of the Solution**

Let’s walk through the **backtracking** approach with an example for clarity. We'll use `s = "ababccc"` and follow the recursive steps to see how the algorithm works.

#### Step 1: Initialize

We begin with the string `"ababccc"`. We will recursively attempt to split the string starting from the first character, and use a **set** to track the substrings we have already used.

#### Step 2: Try Splitting

1. Start at index `0` and split `"a"`. Add `"a"` to the set and move to the next character.
2. Split `"b"` at index `1` and add it to the set.
3. Continue splitting until we reach the string `"abab"` at index `4`.
4. Add `"c"` and `"cc"` at indices `5` and `7`.

By the end, we’ve split the string into `["a", "b", "ab", "c", "cc"]`, yielding a total of **5 unique substrings**.

---

### 🛑 **Backtracking to Explore Other Paths**

The power of backtracking is that it explores **all possible ways** to split the string, discarding invalid splits. For example:

- If we split it as `["a", "b", "a", "b", "c", "cc"]`, this would be invalid because `"a"` and `"b"` appear twice.
- The algorithm will backtrack to previous steps and try different splits until it finds the valid maximum split.

---

### 🔍 **Step-by-Step Breakdown with More Details**

Let’s work through a larger example: `s = "abcdefgabc"`

#### Step 1: Start with `"a"`:
We begin by adding the first letter `"a"` to the set of unique substrings.

#### Step 2: Continue splitting:
- We add `"b"`, `"c"`, `"d"`, `"e"`, and `"f"` sequentially, ensuring that they are all unique.
- As we proceed, `"g"` is added, followed by `"abc"`, the second occurrence of the substring `"abc"`.

At this point, the algorithm notices that `"abc"` has already been used and backtracks, trying different splits until it finds the maximum number of unique substrings.

By recursively checking each substring, the backtracking approach ensures that we get the largest possible number of unique substrings for the given input.

---

### 🔚 **Conclusion**

The backtracking approach we used is the best solution for this problem given the constraints (string length ≤ 16). It explores every possible way to split the string and ensures that we maximize the number of unique substrings.

#### **Key Insights**:
1. **Backtracking** is ideal for exploring all possible combinations when uniqueness or constraint satisfaction is involved.
2. The solution operates within an acceptable time complexity of **O(2^n)** given the small input size.
3. By breaking down the string and ensuring that each substring is unique, we guarantee the correct solution.
