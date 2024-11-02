---
layout: post
title: "#62 🌀 2490. Circular Sentence 🧠🚀"
categories: [LeetCode, Programming]
---

In today’s post, we’re tackling a delightful problem that asks us to determine if a sentence is *circular*. Imagine each word is linked, like a chain, by matching the last letter of each word with the first letter of the next. Sounds easy, right? Well, let’s dig into the details and explore both a straightforward solution and a more optimized approach. Let’s get started! 💥

---

### 📝 Problem Statement

A *sentence* is a list of words separated by single spaces, with no leading or trailing spaces. Each word consists only of uppercase or lowercase English letters (treated as distinct characters). We say a sentence is *circular* if:
1. The last character of each word matches the first character of the next word.
2. The last character of the last word matches the first character of the first word.

Given a `sentence`, return `True` if it is circular and `False` otherwise.

### 🔍 Example Walkthrough

Let's explore a few examples to understand the requirements:

#### Example 1:
- **Input:** `"leetcode exercises sound delightful"`
- **Output:** `True`
- **Explanation:** The words in the sentence are:
  - `"leetcode"` (last character: `'e'`) links to `"exercises"` (first character: `'e'`)
  - `"exercises"` (last character: `'s'`) links to `"sound"` (first character: `'s'`)
  - `"sound"` (last character: `'d'`) links to `"delightful"` (first character: `'d'`)
  - Finally, `"delightful"` (last character: `'l'`) links back to `"leetcode"` (first character: `'l'`)
  - Since all connections are satisfied, this sentence is circular.

#### Example 2:
- **Input:** `"eetcode"`
- **Output:** `True`
- **Explanation:** With only one word `"eetcode"`, the last and first characters are the same (`'e'`). This meets the circular condition.

#### Example 3:
- **Input:** `"Leetcode is cool"`
- **Output:** `False`
- **Explanation:** Here, `"Leetcode"` (last character: `'e'`) does not match the first character of `"is"` (which is `'i'`). So the sentence is not circular.

---

### 🧠 Solution Strategy

To solve this problem, let's break it down into steps:

1. **Split the Sentence**: First, split the sentence into words using spaces as delimiters.
2. **Check Connections Between Words**: Loop through each word and check if the last character of the current word matches the first character of the next word.
3. **Final Connection Check**: After looping through all pairs of words, check if the last character of the last word matches the first character of the first word.

### 💡 Basic Solution

Let’s walk through a straightforward solution. Here, we split the sentence into words, then iterate over pairs of words to check the required conditions.

#### Python Code:

```python
def isCircularSentence(sentence: str) -> bool:
    words = sentence.split(" ")
    n = len(words)
    
    for i in range(n):
        current_word_last_char = words[i][-1]
        next_word_first_char = words[(i + 1) % n][0]
        
        if current_word_last_char != next_word_first_char:
            return False
    
    return True
```

#### 🕹️ Explanation:

1. **Split the Sentence**: We split `sentence` into a list of `words`.
2. **Loop Through Pairs**: Using a loop, we compare each word’s last character with the first character of the next word. To handle the final circular check, we use modulo indexing with `(i + 1) % n`.
3. **Return Result**: If any pair fails the check, return `False`. Otherwise, return `True`.

#### 🕒 Time Complexity

- **Time Complexity**: \(O(n)\), where \(n\) is the number of words in the sentence. We make one pass through the list of words.
- **Space Complexity**: \(O(n)\) for storing the words split from the sentence.

### 🚀 Optimized Solution

Since our basic solution already achieves \(O(n)\) time complexity, it’s efficient. However, we can streamline it by avoiding the need to explicitly handle modulo calculations, enhancing readability.

#### Python Code:

```python
def isCircularSentence(sentence: str) -> bool:
    words = sentence.split()
    
    # Check the link between last and first word as a special case
    if words[0][0] != words[-1][-1]:
        return False
    
    # Check all intermediate links between words
    for i in range(len(words) - 1):
        if words[i][-1] != words[i + 1][0]:
            return False
    
    return True
```

#### 💨 Explanation:

1. **Initial Check**: First, we check if the last character of the last word equals the first character of the first word.
2. **Loop Through Pairs**: Then, we loop through the pairs of words from start to end, checking each pair’s link.
3. **Return Result**: If all links pass, the sentence is circular.

#### 🕒 Time Complexity
- **Time Complexity**: \(O(n)\), where \(n\) is the number of words.
- **Space Complexity**: \(O(n)\) for storing the words.

### Example Walkthrough with Edge Cases

Let's walk through a detailed example with a sentence containing longer words and varied capitalization to cover edge cases.

#### Example
- **Input:** `"Algorithm magic can create excitement"`
- **Output:** `True`
- **Explanation**:
  - `"Algorithm"` (last character `'m'`) links to `"magic"` (first character `'m'`)
  - `"magic"` (last character `'c'`) links to `"can"` (first character `'c'`)
  - `"can"` (last character `'n'`) links to `"create"` (first character `'c'`)
  - `"create"` (last character `'e'`) links to `"excitement"` (first character `'e'`)
  - `"excitement"` (last character `'t'`) links back to `"Algorithm"` (first character `'A'`), but this fails since `'t'` is not equal to `'A'`.
- Since this fails, the sentence is *not circular*.

#### Edge Cases
1. **Single Word**: A sentence with a single word, like `"hello"`. The function should check if the word’s first and last characters match. If they do, return `True`.
2. **Case Sensitivity**: Ensure that uppercase and lowercase characters are treated distinctly, e.g., `"Hello oh"` should return `False`.
3. **Minimal Length**: If `sentence` is very short, like `"a a a a"`, the function should handle it without error.
4. **All Letters Different**: A sentence where each word has a different start and end character, like `"apple orange banana grape"` should return `False`.

### 📜 Conclusion

In this post, we explored how to determine if a sentence is circular by linking words based on matching characters. With our solutions, we demonstrated an efficient approach using basic checks between word pairs. Here’s a quick summary:

- **Problem**: Checking if each word's end connects to the next word's start.
- **Solution**: Use a loop to verify that the last character of each word matches the first character of the next, with a special check for the circular connection.
- **Complexity**: Achieved linear \(O(n)\) complexity.

This problem highlights how character matching and indexing can come together to solve real-world-like string manipulation problems! Keep practicing, and remember to look for patterns in character relationships – they often hold the key to solving many string-related challenges. 🧩
