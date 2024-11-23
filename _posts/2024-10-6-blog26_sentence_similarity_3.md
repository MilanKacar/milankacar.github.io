---
layout: post  
title: "#26 1813. Sentence Similarity III 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Two Pointers, String]
---

In competitive programming and real-world applications, sentence manipulation and comparison are common tasks. Whether you're working on natural language processing (NLP), chatbots, or search engines, understanding how to determine sentence similarity is crucial.

In this post, we’ll dive into the Sentence Similarity III problem, where we explore how to determine if two sentences can be made similar by inserting some words into one of them. This problem offers a fantastic opportunity to practice string manipulation and comparison techniques. It's a fun and challenging puzzle that tests your ability to work with words and spaces in an efficient way.

We will walk through the problem, analyze it with examples, and provide a clean Python solution that balances both clarity and performance. Ready to learn how two different sentences can become one? Let’s get started!

#### 🔍 Problem Statement:
You are given two strings, `sentence1` and `sentence2`, each representing a sentence composed of words separated by single spaces. The goal is to determine whether the two sentences can be made **similar** by inserting an arbitrary sentence (possibly empty) into one of them. 

The inserted sentence must be separated from the existing words by spaces. In simpler terms, two sentences are considered similar if, by adding words to one of them in a specific position, we can make both sentences identical.

For example:
- `sentence1 = "Hello Jane"` and `sentence2 = "Hello my name is Jane"` are **similar** because you can insert `"my name is"` between `"Hello"` and `"Jane"` in `sentence1` to match `sentence2`.
- However, `"Frog cool"` and `"Frogs are cool"` are **not similar**, as the inserted sentence `"s are"` does not have spaces separating it from the words in `sentence1`.

#### 🧩 Problem Breakdown:
The task boils down to checking whether one sentence can be turned into the other by inserting some words either at the beginning, the middle, or the end of the other sentence, while maintaining proper space separation. 

We must focus on **prefixes** (words that match from the beginning) and **suffixes** (words that match from the end) between the two sentences. If the middle part of one sentence can be ignored (and therefore inserted into the other), then the sentences are similar.

#### 💡 Example Walkthrough:

**Example 1:**
```plaintext
Input: sentence1 = "My name is Haley", sentence2 = "My Haley"
Output: true
```
Explanation: 
- The words `"My"` and `"Haley"` appear at the beginning and end of both sentences, respectively. 
- By inserting the middle part of `sentence1`, which is `"name is"`, between `"My"` and `"Haley"` in `sentence2`, the two sentences become identical.

**Example 2:**
```plaintext
Input: sentence1 = "of", sentence2 = "A lot of words"
Output: false
```
Explanation: 
- There's no way to insert any part of `sentence1` into `sentence2` (or vice versa) to make them identical. The mismatch is too large.

**Example 3:**
```plaintext
Input: sentence1 = "Eating right now", sentence2 = "Eating"
Output: true
```
Explanation:
- In this case, the sentences match from the start, and the only difference is that `sentence1` has extra words, `"right now"`, at the end.
- By inserting `"right now"` at the end of `sentence2`, we can make them identical.

#### 🛠️ Solution:
To solve this problem, we can focus on finding the **longest common prefix** and the **longest common suffix** between the two sentences. The middle portion, if there is any, should be ignored because it can be inserted into the other sentence without affecting the overall structure.

### Steps:
1. **Split both sentences** into lists of words.
2. **Compare words from the start** of both lists to find the longest common prefix.
3. **Compare words from the end** of both lists to find the longest common suffix.
4. If the combined length of the prefix and suffix covers the entire shorter sentence, the sentences are considered similar.

#### ⚡ Optimized Solution (Python):
The following Python code efficiently implements the solution by comparing both prefixes and suffixes of the two sentences:

```python
def areSentencesSimilar(sentence1: str, sentence2: str) -> bool:
    s1_words = sentence1.split()  # Split sentence1 into words
    s2_words = sentence2.split()  # Split sentence2 into words
    
    # Step 1: Find the longest common prefix
    i = 0
    while i < len(s1_words) and i < len(s2_words) and s1_words[i] == s2_words[i]:
        i += 1  # Move forward as long as words match at the start
    
    # Step 2: Find the longest common suffix
    j = 0
    while j < len(s1_words) - i and j < len(s2_words) - i and s1_words[-1 - j] == s2_words[-1 - j]:
        j += 1  # Move backward as long as words match at the end
    
    # Step 3: Check if i + j covers the entire shorter sentence
    return i + j == min(len(s1_words), len(s2_words))
```

#### 🧑‍💻 Code Explanation:
- `sentence1.split()` and `sentence2.split()` convert the sentences into lists of words for easier comparison.
- We initialize two indices, `i` (for the prefix) and `j` (for the suffix). 
- The prefix matching loop (`i`) moves from the beginning of both word lists, comparing the words at the same index, until a mismatch is found.
- The suffix matching loop (`j`) compares words from the end of both word lists, moving backward.
- Finally, we check if the combined length of the prefix and suffix (`i + j`) covers all words in the shorter sentence. If it does, the sentences are similar; otherwise, they're not.

#### ⏳ Time Complexity:
- The time complexity of this solution is **O(n)**, where `n` is the number of words in the shorter sentence. 
- We iterate over the words from the start and the end only once, making it efficient even for larger inputs.

#### 📝 Edge Cases to Consider:
- **Identical sentences**: Sentences like `"Hello World"` and `"Hello World"` are always similar.
- **One word vs. multiple words**: When one sentence contains a single word, like `"of"` and `"A lot of words"`, it's important to handle these mismatches early.
- **No common words**: If the two sentences share no common words, such as `"apple pie"` and `"banana split"`, the solution should return `false` right away.

#### 🚀 Conclusion:
This solution efficiently handles the problem of sentence similarity by focusing on the longest common prefix and suffix. The key insight is that we can ignore the middle portion of the sentences, as any insertion in the middle doesn’t affect the overall similarity.

With an optimal time complexity of **O(n)**, this approach ensures that even the largest sentences can be compared quickly. This problem serves as a great example of using string manipulation and array comparisons to solve real-world issues, such as text processing and sentence similarity in natural language processing tasks.
