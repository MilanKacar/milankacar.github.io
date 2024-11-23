---
layout: post  
title: " #5 2416. Sum of Prefix Scores of Strings 🚀 " 
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, String, Trie, Counting]
---


In this post, we will tackle the problem of finding the **sum of prefix scores of strings**. We'll explore both a **brute-force approach** and an **optimized solution** using a HashMap to solve this problem efficiently. Let's dive into it!

## 📋 Problem Statement

You are given an array of strings `words` consisting of non-empty strings. We define the **score** of a string `word` as the number of strings `words[i]` such that `word` is a **prefix** of `words[i]`.

For example, if `words = ["a", "ab", "abc", "cab"]`, then the score of `"ab"` is `2`, since `"ab"` is a prefix of both `"ab"` and `"abc"`.

Your goal is to return an array `answer` of size `n` where `answer[i]` is the **sum of scores of every non-empty prefix** of `words[i]`.

### 💡 Example 1:
```text
Input: words = ["abc", "ab", "bc", "b"]  
Output: [5, 4, 3, 2]

Explanation:
- "abc" has 3 prefixes: "a", "ab", "abc".
  Score = 2 (for "a") + 2 (for "ab") + 1 (for "abc") = 5
- "ab" has 2 prefixes: "a", "ab".
  Score = 2 (for "a") + 2 (for "ab") = 4
- "bc" has 2 prefixes: "b", "bc".
  Score = 2 (for "b") + 1 (for "bc") = 3
- "b" has 1 prefix: "b".
  Score = 2 (for "b") = 2
```

### 💡 Example 2:
```text
Input: words = ["abcd"]
Output: [4]

Explanation:
- "abcd" has 4 prefixes: "a", "ab", "abc", "abcd".
  Each prefix has 1 string, so total = 1 + 1 + 1 + 1 = 4.
```

---

## 🐢 Brute-Force Solution

The **brute-force approach** generates every prefix for each string and checks how many strings in the array start with that prefix. It's simple but inefficient.

### Approach 💡

1. **Generate Prefixes**: For each word, generate all possible non-empty prefixes.
2. **Count Matches**: For each prefix, count how many strings in the array start with it.
3. **Sum the Scores**: Add up the counts of each prefix to compute the total score for the word.

### 🧑‍💻 Brute-Force Code Implementation

```python
def sumPrefixScores(words):
    n = len(words)
    answer = []

    for word in words:
        total_score = 0
        # Iterate over all possible prefixes of 'word'
        for i in range(1, len(word) + 1):
            prefix = word[:i]
            count = 0
            # Count how many words start with this prefix
            for other in words:
                if other.startswith(prefix):
                    count += 1
            total_score += count
        answer.append(total_score)

    return answer
```

### 🕰️ Time Complexity of Brute-Force:

- **Prefix Generation**: For each word, generating all prefixes takes **O(k)** time, where `k` is the length of the word.
- **Prefix Matching**: For each prefix, checking all words takes **O(n)** time.
  
Thus, the overall time complexity is **O(n * k²)**, where `n` is the number of words and `k` is the average length of the words. This can be quite slow for large inputs.

---

## ⚡ Optimized Solution: Using a HashMap

We can significantly improve the performance by counting prefix occurrences using a **HashMap** (dictionary). This avoids rechecking all words for each prefix.

### 💡 Optimized Approach:

1. **Count Prefix Occurrences**: For each word, generate all its prefixes and store how many times each prefix appears across all words.
2. **Calculate Scores**: For each word, sum the counts of all its prefixes to compute the total prefix score.

### Steps:
- Use a HashMap to count how many times each prefix appears across all the words.
- For each word, sum the occurrences of its prefixes from the HashMap.

### 🚀 Optimized Code Implementation

```python
def sumPrefixScores(words):
    prefix_count = {}

    # Step 1: Count frequencies of all prefixes
    for word in words:
        for i in range(1, len(word) + 1):
            prefix = word[:i]
            prefix_count[prefix] = prefix_count.get(prefix, 0) + 1

    # Step 2: Calculate the score for each word based on prefix counts
    answer = []
    for word in words:
        score = 0
        for i in range(1, len(word) + 1):
            prefix = word[:i]
            score += prefix_count[prefix]
        answer.append(score)

    return answer
```

### 📝 Example Walkthrough

Let's take `words = ["abc", "ab", "bc", "b"]`. After the first step (counting prefix occurrences), the **HashMap** will look like this:

```python
{
  "a": 2,  # ("abc", "ab")
  "ab": 2, # ("abc", "ab")
  "abc": 1, # ("abc")
  "b": 2,  # ("bc", "b")
  "bc": 1  # ("bc")
}
```

Now, for each word, we calculate the total score by summing the counts of its prefixes:

- `"abc"`: score = `prefix_count["a"] + prefix_count["ab"] + prefix_count["abc"] = 2 + 2 + 1 = 5`
- `"ab"`: score = `prefix_count["a"] + prefix_count["ab"] = 2 + 2 = 4`
- `"bc"`: score = `prefix_count["b"] + prefix_count["bc"] = 2 + 1 = 3`
- `"b"`: score = `prefix_count["b"] = 2`

### ⏳ Time Complexity of Optimized Solution:

- **Counting Prefixes**: O(n * k), where `n` is the number of words and `k` is the average length of the words.
- **Calculating Scores**: O(n * k), as we sum the scores for each word's prefixes.

The overall time complexity is **O(n * k)**, which is much more efficient compared to the brute-force approach.

---

## 📝 Conclusion

### 🐢 Brute-Force Solution:
- **Time Complexity**: O(n * k²)  
  Simple but inefficient. For large datasets, it takes too long to compute prefix scores.

### ⚡ Optimized Solution (Using HashMap):
- **Time Complexity**: O(n * k)  
  Efficient and scalable, this solution optimizes the process by counting prefix occurrences in a single pass using a HashMap.

When dealing with large datasets, the optimized solution is highly recommended. The HashMap efficiently reduces redundant calculations, providing a much faster solution for this problem. Happy coding! 😄



