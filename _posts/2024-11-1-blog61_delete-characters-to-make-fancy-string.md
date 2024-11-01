---
layout: post
title: "#60 1957. Delete Characters to Make Fancy String 🧠🚀"
categories: [LeetCode, Programming]
---


Imagine a long string with repeating characters that looks a bit cluttered. For instance, "aaabaaaa" has groups of consecutive characters that make it challenging to read. Our mission is to refine this string to make it look fancy! In a fancy string, no three consecutive characters should be the same. 🪄 By removing the minimum number of characters, we'll transform the input string to meet this condition. Let’s dive into the details of how to approach this problem and implement an efficient solution.

---

### 📜 Problem Statement
A **fancy string** is a string where no three consecutive characters are equal.

Given a string `s`, delete the minimum possible number of characters from `s` to make it fancy.

Return the final string after deletion. The answer will always be unique.

#### Constraints:
- \(1 \leq s.\text{length} \leq 10^5\)
- `s` consists only of lowercase English letters.

---

### 🔍 Examples

#### Example 1:
- **Input:** `s = "leeetcode"`
- **Output:** `"leetcode"`
- **Explanation:** Remove an extra 'e' from the initial sequence of 'e's to prevent three consecutive 'e's. The final result is "leetcode".

#### Example 2:
- **Input:** `s = "aaabaaaa"`
- **Output:** `"aabaa"`
- **Explanation:** 
  - Remove an 'a' from the first group of 'a's to make `"aabaaaa"`.
  - Then, remove two 'a's from the second group of 'a's, resulting in `"aabaa"`.

#### Example 3:
- **Input:** `s = "aab"`
- **Output:** `"aab"`
- **Explanation:** Since there are no three consecutive characters in "aab", the string remains unchanged.

---

### 🚩 Edge Cases
1. **Short Strings**: For strings with fewer than three characters, the output is the same as the input since no three consecutive characters exist.
2. **All Identical Characters**: If the string consists of a single repeated character, like `"aaaaaa"`, we should reduce it to two occurrences of that character, `"aa"`.
3. **No Repeating Characters**: For a string without any consecutive repeating characters, like `"abc"`, the string remains unchanged.
4. **Strings with Intermittent Repeats**: For strings with intermittent repeats, like `"abbba"`, we must delete any third consecutive character within groups.

---

### 🧩 Solution Approach

#### Strategy
To efficiently solve this problem, we can employ a **greedy approach**. We’ll construct the output string by iterating through the input string and only adding characters that do not result in three consecutive duplicates.

### 💡 Optimized Solution
1. **Initialize an Empty Result List**: We'll use a list (`result`) to store our final answer character by character.
2. **Iterate Through the Input String**: For each character in `s`, check if appending it to `result` would form three consecutive duplicates.
3. **Add Character Conditionally**:
   - If adding the character does not lead to three consecutive identical characters, add it to `result`.
   - Otherwise, skip this character.
4. **Return the Final String**: Join the characters in `result` to form the final answer.

#### Time Complexity Analysis
This solution has a **time complexity of \(O(n)\)** since we only iterate through the string once and perform constant-time checks for each character.

---

### 🧑‍💻 Python Code Implementation

```python
def make_fancy_string(s: str) -> str:
    # Step 1: Initialize an empty result list to build the fancy string
    result = []
    
    # Step 2: Iterate through each character in the input string `s`
    for char in s:
        # Step 3: Check if adding the current character `char` would result in
        # three consecutive identical characters
        if len(result) >= 2 and result[-1] == char and result[-2] == char:
            continue  # Skip this character if it would create three in a row
        
        # Step 4: Otherwise, append the character to the result list
        result.append(char)
    
    # Step 5: Join the list into a string and return the final result
    return "".join(result)
```

---

### 🔍 Example Walkthrough

Let's go through the code with a detailed example to see how it operates in practice:

#### Input Example: `s = "aaabaaaa"`
- **Step 1**: Initialize an empty `result = []`.
  
- **Step 2**: Begin iterating through each character:
  - **First `a`**: `result = ['a']`
  - **Second `a`**: `result = ['a', 'a']`
  - **Third `a`**: Skip (to avoid three consecutive 'a's)
  - **`b`**: `result = ['a', 'a', 'b']`
  - **Fourth `a`**: `result = ['a', 'a', 'b', 'a']`
  - **Fifth `a`**: `result = ['a', 'a', 'b', 'a', 'a']`
  - **Sixth `a`**: Skip (to avoid three consecutive 'a's)
  - **Seventh `a`**: Skip (to avoid three consecutive 'a's)

- **Step 3**: Join the `result` list: `''.join(['a', 'a', 'b', 'a', 'a']) = "aabaa"`
  
- **Output**: `"aabaa"`

This step-by-step addition ensures that we avoid any three consecutive characters.

---

### 🧐 Edge Case Walkthroughs

1. **Case: Short String**  
   - **Input**: `"a"`
   - **Explanation**: The string length is less than 3, so it meets the criteria by default.
   - **Output**: `"a"`

2. **Case: All Identical Characters**  
   - **Input**: `"aaaaa"`
   - **Explanation**: Reduce the string to two characters to avoid three in a row.
   - **Output**: `"aa"`

3. **Case: Mixed Groups**  
   - **Input**: `"aabbaaa"`
   - **Explanation**: First "aab" and "ba" are acceptable. We need to delete one of the 'a's in the last group.
   - **Output**: `"aabbaa"`

---

### 📝 Conclusion

By iterating through the string and conditionally adding characters to our result, we efficiently avoid creating three consecutive identical characters. This method ensures a unique solution with minimal deletions. This problem is an excellent example of using a **greedy algorithm** to achieve optimal results with minimal complexity.
