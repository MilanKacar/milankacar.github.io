---
layout: post
title: " #5 2416. Sum of Prefix Scores of Strings 🚀 "
categories: LeetCode Programming
---

Certainly! Here's the updated blog post with the time complexities added at the end.

---

We are given an array of strings `words`, and for each string in the array, we need to calculate the sum of the **scores of every non-empty prefix** of that string. The **score** of a prefix is defined as the number of strings in the array that start with that prefix.

**For example**:  
Given `words = ["abc", "ab", "bc", "b"]`, we want to calculate an array `answer` where each element corresponds to the sum of the scores of the prefixes of the respective word.

---

### Example 🎯

**Input**:  
```python
words = ["abc", "ab", "bc", "b"]
```

**Output**:  
```python
answer = [5, 4, 3, 2]
```

### Explanation:  
Let's break down each string and calculate the total score for each of its prefixes:

- **"abc"** has prefixes `"a"`, `"ab"`, `"abc"`.  
  - There are 2 strings starting with `"a"`: `"abc"`, `"ab"`.
  - There are 2 strings starting with `"ab"`: `"abc"`, `"ab"`.
  - There is 1 string starting with `"abc"`: `"abc"`.
  - Total: `2 + 2 + 1 = 5`

- **"ab"** has prefixes `"a"`, `"ab"`.  
  - There are 2 strings starting with `"a"`: `"abc"`, `"ab"`.
  - There are 2 strings starting with `"ab"`: `"abc"`, `"ab"`.
  - Total: `2 + 2 = 4`

- **"bc"** has prefixes `"b"`, `"bc"`.  
  - There are 2 strings starting with `"b"`: `"bc"`, `"b"`.
  - There is 1 string starting with `"bc"`: `"bc"`.
  - Total: `2 + 1 = 3`

- **"b"** has prefix `"b"`.  
  - There are 2 strings starting with `"b"`: `"bc"`, `"b"`.
  - Total: `2`

---

## 🛠 Brute-Force Solution

### Approach 💡

We can solve this problem in a brute-force manner by iterating through each word and calculating the score for every possible prefix. For each prefix, we check how many strings in the array have that prefix.

### Steps:

1. Loop through each word in `words`.
2. For each word, generate all possible non-empty prefixes.
3. For each prefix, count how many strings in the array start with that prefix.
4. Add up these counts to get the total score for the word.

### Code Implementation:

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

### Time Complexity ⏳

- **Generating prefixes**: O(k) for each word, where `k` is the length of the word.
- **Checking for matches**: O(n) for each prefix, where `n` is the number of words.

Thus, the overall time complexity is **O(n * k²)**, where `n` is the number of words and `k` is the average length of the words.

This approach works but is **inefficient** for larger inputs, as it requires checking every word for every prefix.

---

## 🚀 Optimal Solution

To optimize the solution, we can use a **Trie** (Prefix Tree) data structure, which allows us to efficiently count the occurrences of prefixes while inserting words.

### Why Trie? 🌳

A **Trie** is perfect for this problem because:
- It stores strings in a hierarchical structure based on their prefixes.
- It allows us to efficiently count how many words share the same prefix while inserting each word into the Trie.

### Steps:

1. **Build the Trie**: Insert each word into the Trie. While inserting a word, for each character, keep track of how many times that prefix has been encountered.
2. **Calculate Prefix Scores**: For each word, traverse through its prefixes in the Trie and sum up the counts.

### Code Implementation:

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.count = 0

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
            node.count += 1
    
    def getPrefixScore(self, word):
        node = self.root
        total_score = 0
        for ch in word:
            if ch in node.children:
                node = node.children[ch]
                total_score += node.count
        return total_score

def sumPrefixScores(words):
    trie = Trie()
    
    # Step 1: Insert all words into the Trie
    for word in words:
        trie.insert(word)
    
    # Step 2: Calculate the score for each word
    answer = []
    for word in words:
        score = trie.getPrefixScore(word)
        answer.append(score)
    
    return answer
```

### Time Complexity ⏳

- **Inserting a word**: O(k) for each word, where `k` is the length of the word.
- **Querying a word's score**: O(k) for each word.

Thus, the overall time complexity is **O(n * k)**, which is much more efficient than the brute-force approach.

---

### Example Walkthrough 🎯

For `words = ["abc", "ab", "bc", "b"]`, the Trie is built as follows:

1. Insert `"abc"`, the Trie looks like:
   ```
   root -> a -> b -> c
   ```

2. Insert `"ab"`, the Trie looks like:
   ```
   root -> a -> b -> c
                   -> (end of "ab")
   ```

3. Insert `"bc"`, the Trie looks like:
   ```
   root -> a -> b -> c
        -> b -> c
   ```

4. Insert `"b"`, the Trie looks like:
   ```
   root -> a -> b -> c
        -> b -> c
           -> (end of "b")
   ```

By counting the prefixes in the Trie, we can efficiently calculate the sum of prefix scores for each word!

---

## Time Complexity Comparison ⏳

### Brute-Force Approach:
- **Time Complexity**: O(n * k²)  
  Where `n` is the number of words and `k` is the average length of the words.
  - Generating each prefix takes O(k) time.
  - For each prefix, we check all words to see if they start with that prefix, taking O(n) time.
  - Total: O(n * k²).

### Optimal Trie-Based Approach:
- **Time Complexity**: O(n * k)  
  Where `n` is the number of words and `k` is the average length of the words.
  - Inserting each word into the Trie takes O(k) time.
  - Querying the prefix score of each word also takes O(k) time.
  - Total: O(n * k).

