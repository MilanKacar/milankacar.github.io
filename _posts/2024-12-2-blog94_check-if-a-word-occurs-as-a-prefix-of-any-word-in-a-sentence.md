---
layout: post  
title: "#93 📝 1455. Check If a Word Occurs As a Prefix of Any Word in a Sentence 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Easy
tags: [Two Pointers, String, String Matching]
---

Given a sentence and a search word, determine if the search word appears as a prefix in any word of the sentence. If it does, return the 1-indexed position of the first word that matches. Otherwise, return `-1`.  

---

## 🧐 **Problem Statement**  
We are tasked with finding if a given `searchWord` is a prefix of any word in a `sentence`. If it is, we return the **1-indexed position** of the first word that matches. If no word matches, return `-1`.  
 
A **prefix** is defined as a leading contiguous substring of a string. For example, "burg" is a prefix of "burger" because "burger" starts with "burg".  

### 🔍 **Input and Output:**  

#### **Input:**  
- `sentence` (string): A sentence containing words separated by single spaces.  
- `searchWord` (string): A word we want to check as a prefix.  

#### **Output:**  
- Return the 1-indexed position of the word where `searchWord` is a prefix.  
- If `searchWord` is not a prefix of any word, return `-1`.  

---

## ✨ **Examples**  

### **Example 1:**  
**Input:**  
```python
sentence = "i love eating burger"  
searchWord = "burg"
```  
**Output:**  
```python
4
```  
**Explanation:**  
The word "burger" is the 4th word in the sentence, and "burg" is a prefix of it.  

### **Example 2:**  
**Input:**  
```python
sentence = "this problem is an easy problem"  
searchWord = "pro"
```  
**Output:**  
```python
2
```  
**Explanation:**  
The word "problem" appears twice in the sentence (at positions 2 and 6). We return the minimal index, which is `2`.  

### **Example 3:**  
**Input:**  
```python
sentence = "i am tired"  
searchWord = "you"
```  
**Output:**  
```python
-1
```  
**Explanation:**  
The word "you" is not a prefix of any word in the sentence, so the result is `-1`.  

---

## 💡 **Edge Cases**  
1. **Case with no match:**  
   - Input: `sentence = "hello world"`, `searchWord = "x"`.  
   - Output: `-1`.  
   - Explanation: None of the words in the sentence start with "x".  

2. **Case with empty sentence:**  
   - Input: `sentence = ""`, `searchWord = "test"`.  
   - Output: `-1`.  
   - Explanation: An empty sentence has no words to check.  

3. **Case where all words match:**  
   - Input: `sentence = "abc abc abc"`, `searchWord = "a"`.  
   - Output: `1`.  
   - Explanation: All words start with "a", but the first occurrence is at position `1`.  

4. **Case where searchWord equals the full word:**  
   - Input: `sentence = "apple banana", searchWord = "apple"`.  
   - Output: `1`.  
   - Explanation: The `searchWord` matches the first word entirely, so it is still a prefix.  

---

## 🛠️ **Solution**  

### Approach  
1. **Split the Sentence:**  
   Break the `sentence` into words using the `split()` function.  

2. **Check for Prefix:**  
   Iterate through the list of words, checking if the `searchWord` is a prefix of any word using Python's `startswith()` method.  

3. **Return the Index:**  
   If a match is found, return the 1-indexed position (index + 1). If no match is found, return `-1`.  

---

### 🔹 **Python Code (Single Solution)**  

```python
class Solution:
    def isPrefixOfWord(self, sentence: str, searchWord: str) -> int:
        # Split the sentence into words
        words = sentence.split()
        
        # Iterate through words and check for the prefix
        for i, word in enumerate(words):
            if word.startswith(searchWord):
                return i + 1  # 1-indexed position
        
        # If no match, return -1
        return -1
```

---

### **Time Complexity**  
- **Splitting the sentence:** `O(N)` where `N` is the length of the `sentence`.  
- **Iterating through words:** `O(W)` where `W` is the number of words in the `sentence`.  
- **Checking prefix for each word:** `O(L)` where `L` is the length of the longest word.  

Thus, the overall time complexity is **`O(N + W * L)`**.  

### **Space Complexity**  
- Splitting the sentence creates a list of words, so the space complexity is **`O(W)`**, where `W` is the number of words.  

---

## 🔍 **Example Walkthrough**  

### **Example Input:**  
```python
sentence = "coding is fun and fantastic"
searchWord = "fan"
```  

#### Step-by-Step Execution:  
1. **Split the sentence:**  
   ```python
   words = ["coding", "is", "fun", "and", "fantastic"]
   ```  

2. **Iterate through words:**  
   - `i = 0`, `word = "coding"` → `"fan"` is not a prefix.  
   - `i = 1`, `word = "is"` → `"fan"` is not a prefix.  
   - `i = 2`, `word = "fun"` → `"fan"` is not a prefix.  
   - `i = 3`, `word = "and"` → `"fan"` is not a prefix.  
   - `i = 4`, `word = "fantastic"` → `"fan"` **is a prefix!**  

3. **Return Index:**  
   The word "fantastic" is at position `5` (1-indexed).  

**Output:**  
```python
5
```  

---

## ✍️ **Conclusion**  
This problem is straightforward with the use of string operations like `startswith()`. By splitting the sentence into words and checking each for a prefix match, we efficiently find the result.  

### **Key Takeaways:**  
- Use Python's built-in string manipulation functions for clean and concise solutions.  
- Remember to account for edge cases like no matches, an empty sentence, or multiple matches.  

With this approach, you can solve the problem in optimal time while maintaining clarity in your code. 🚀  
