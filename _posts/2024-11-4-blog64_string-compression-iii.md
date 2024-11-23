---
layout: post  
title: "#64 🔍 3163. String Compression III 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [String]
---

When working with large strings, especially those filled with repeating characters, **compression** can save valuable space. In **LeetCode problem #3163: String Compression III**, we’re asked to create a compressed version of a string while following a specific rule: we can capture **only up to 9 consecutive characters of the same type** at a time. This twist challenges us to think carefully about how to **segment the string** efficiently without missing any repetitions.

Whether you're just starting with string manipulation or looking to brush up on your algorithmic skills, this problem is a great one to dissect. Let’s jump into it and solve it step-by-step! 🚀

---

## Problem Statement 📝

Given a string `word`, we need to compress it using the following algorithm:

1. Initialize an empty string `comp`.
2. While `word` has characters left:
   - Identify the longest prefix of `word` that consists of the same character `c` but does not exceed **9 consecutive occurrences**.
   - Remove this prefix from `word`.
   - Append the **length of this prefix** followed by `c` to `comp`.
3. Once we’ve processed the entire string, `comp` holds the compressed version of `word`.

### Examples 📖

**Example 1**:
- **Input**: `word = "abcde"`
- **Output**: `"1a1b1c1d1e"`
  
**Explanation**: Here, each character appears only once, so we append `"1"` followed by each character to get `"1a1b1c1d1e"`.

**Example 2**:
- **Input**: `word = "aaaaaaaaaaaaaabb"`
- **Output**: `"9a5a2b"`

**Explanation**: 
- We start with `"aaaaaaaaa"` (9 `a`s), which we encode as `"9a"`.
- Then we capture `"aaaaa"` (5 `a`s) as `"5a"`.
- Finally, `"bb"` is encoded as `"2b"`.

### Constraints
- **String length**: `1 <= word.length <= 200,000`
- `word` contains only lowercase English letters.

---

## Initial Thoughts 🤔

The problem is all about **grouping characters** into clusters of **up to 9 occurrences**. This limit on the number of characters in each group forces us to consider only the **first 9 repetitions** of each character at a time. Here’s the plan:

1. **Traverse `word` character by character**, counting consecutive occurrences until we reach either a new character or the limit of 9 repetitions.
2. After each count, **add the count and character to `comp`** and proceed with the next group of the same character if more instances remain.

---

## Edge Cases 🧩

Considering edge cases helps us ensure our solution is robust:
1. **Single Character String**: `word = "a"`. Since only one character exists, the output should be `"1a"`.
2. **String of Unique Characters**: `word = "abcdefg"`. With each character unique, each will have only one occurrence: `"1a1b1c1d1e1f1g"`.
3. **All Characters Repeat but are Fewer Than 9**: `word = "aaa"`. The output here should be `"3a"`.
4. **Very Long String of a Single Character**: `word = "aaaaaaaaaaaaaaaa"`. With 16 `a`s, this tests the segmentation rule. Expected output: `"9a7a"`, as we capture 9 and then the remaining 7.
5. **Mixed Repeats and Singles**: `word = "aaaabbbccddeeeeeeeeee"`. Here, the pattern and limit constraints will apply to different groups.

---

## Approach 1: Basic Solution 🛠️

### Strategy 📋
In this approach, we’ll keep a pointer to mark the start of each new group. For each character in the string:
- **Count consecutive occurrences** of the character until either the character changes or we reach the max of 9.
- **Append the count and character to the result** and move to the next character group.

Using this approach, we can process each group in constant time.

### Complexity Analysis 🕰️
- **Time Complexity**: **O(n)** - We pass through each character in `word` once.
- **Space Complexity**: **O(n)** - Storing the compressed string in `comp`.

### Basic Solution Code in Python 🐍

```python
def compress_string(word: str) -> str:
    comp = ""
    i = 0
    while i < len(word):
        char = word[i]
        count = 0
        # Count up to 9 occurrences of the current character
        while i < len(word) and word[i] == char and count < 9:
            i += 1
            count += 1
        # Append the count and character to the result
        comp += str(count) + char
    return comp
```

---

## Optimized Solution 🏅

To make our approach even more efficient, we’ll use a **list to build `comp`** rather than concatenating strings. This is because concatenating strings repeatedly is inefficient in Python due to creating new strings each time. 

### Optimized Solution Code in Python 🐍

```python
def compress_string(word: str) -> str:
    comp = []
    i = 0
    while i < len(word):
        char = word[i]
        count = 0
        # Count up to 9 occurrences of the current character
        while i < len(word) and word[i] == char and count < 9:
            i += 1
            count += 1
        comp.append(f"{count}{char}")
    return "".join(comp)
```

### Complexity
This approach shares the same complexity as the first:
- **Time Complexity**: **O(n)**
- **Space Complexity**: **O(n)**

---

## Example Walkthrough 🔍

Let’s look at a more detailed example to see the solution in action.

### Example 1: `word = "aaaaaaaaaaaaaabb"`

1. **Initialize `comp` as an empty list**.
2. **Count the first 9 `a`s**:
   - We count up to the limit, so `count = 9` and `comp = ["9a"]`.
3. **Move to the next group of `a`s**:
   - We count the remaining 5 `a`s: `count = 5` and `comp = ["9a", "5a"]`.
4. **Move to `b`s**:
   - Count 2 `b`s: `comp = ["9a", "5a", "2b"]`.

### Example 2: `word = "aaaaabbbbccccc"`

Here’s how it breaks down:
1. First group of 5 `a`s: `comp = ["5a"]`.
2. Then 4 `b`s: `comp = ["5a", "4b"]`.
3. Finally, 5 `c`s: `comp = ["5a", "4b", "5c"]`.

---

## Edge Case Walkthrough 🔎

Let’s walk through a few tricky cases to ensure our solution is handling edge cases correctly.

### Case 1: Single Character String
- **Input**: `word = "a"`
- **Output**: `comp = ["1a"]`, resulting in `"1a"`

### Case 2: Completely Unique Characters
- **Input**: `word = "abcdefg"`
- **Output**: `comp = ["1a", "1b", "1c", "1d", "1e", "1f", "1g"]`, which translates to `"1a1b1c1d1e1f1g"`

### Case 3: String of 16 Identical Characters
- **Input**: `word = "aaaaaaaaaaaaaaaa"`
- **Output**: `comp = ["9a", "7a"]`, so we get `"9a7a"`

---

## Conclusion 🎉

We’ve solved **String Compression III** by taking advantage of character repetition patterns and efficiently building compressed groups. Here’s what we learned:

1. **Constraints can guide our solution**: Limiting to 9 repetitions per group let us decide to segment each character group into smaller, manageable clusters.
2. **Building with lists is efficient**: By using a list to construct our final string, we reduced unnecessary operations, keeping our solution optimal.
3. **Edge cases are essential**: Single characters, unique characters, and large repetitions helped verify the robustness of our solution.

### Takeaways 📚
- Leveraging the problem’s constraints allows us to find optimal solutions.
- Efficient string operations can make a big difference in performance, especially for large inputs.
- Carefully handling edge cases leads to a more reliable solution.

With this knowledge, you’re now ready to tackle similar problems involving character manipulation and compression. Happy coding! 😄
