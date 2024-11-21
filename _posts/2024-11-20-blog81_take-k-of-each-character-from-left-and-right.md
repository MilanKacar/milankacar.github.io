---
layout: post  
title: "#81 📏🧮 2461. Maximum Sum of Distinct Subarrays With Length K 🧠🚀"  
categories: [LeetCode, Programming]  
---

You are given a string `s` containing only the characters `'a'`, `'b'`, and `'c'`, along with a non-negative integer `k`. The goal is to find the **minimum number of minutes** required to remove characters such that at least `k` instances of each character (`'a'`, `'b'`, `'c'`) remain in the string. 

You can only remove **one character per minute**, and the removal must be either from the **leftmost** or the **rightmost** side of the string. If it is **impossible** to meet the condition, return `-1`.

### **🛠️ Problem Details**  

- **Difficulty:** Medium  
- **Tags:** Hash Table, String, Sliding Window  
- **LeetCode Link:** [Take K of Each Character From Left and Right](https://leetcode.com/problems/take-k-of-each-character-from-left-and-right/)  

---

### 🧐 **Key Intuition**

To efficiently solve this problem, we use the **sliding window technique**. The main idea is to determine the **smallest window** that can be removed from the string while still ensuring at least `k` instances of `'a'`, `'b'`, and `'c'` remain. This leads us to the answer by computing:
\[
\text{Answer} = \text{Length of } s - \text{Size of the largest valid window found.}
\]

### 🔑 **Steps to Solve**
1. **Character Counting:** Use a frequency counter to track occurrences of `'a'`, `'b'`, and `'c'`. If any character has fewer than `k` instances initially, the answer is immediately `-1`.

2. **Sliding Window:** Identify the largest contiguous substring (or "window") that can be removed from `s`, leaving enough characters of `'a'`, `'b'`, and `'c'` outside the window to satisfy the condition.

3. **Optimize Window:** Adjust the window boundaries dynamically using two pointers (`i` and `j`). If removing characters causes the count of any character to drop below `k`, move the left pointer (`j`) forward to restore balance.

4. **Calculate Result:** The minimum time required is the difference between the total string length and the size of the largest removable window.

---

### 🔧 **Algorithm Implementation**

Here's a Python implementation of the above approach using the **sliding window** method:

```python
from collections import Counter

class Solution:
    def takeCharacters(self, s: str, k: int) -> int:
        # Count the frequency of each character in the string
        char_count = Counter(s)
        
        # Check if it's impossible to satisfy the condition
        if any(char_count[char] < k for char in "abc"):
            return -1  # Return -1 if any character occurs less than k times
        
        # Initialize pointers and variables
        max_window_size = 0
        left = 0
        n = len(s)
        
        # Iterate over the string with the right pointer
        for right in range(n):
            # Decrement the count of the current character
            char_count[s[right]] -= 1
            
            # If any character count drops below k, adjust the left pointer
            while any(char_count[char] < k for char in "abc"):
                char_count[s[left]] += 1
                left += 1
            
            # Update the maximum window size that can be removed
            max_window_size = max(max_window_size, right - left + 1)
        
        # The minimum time required is the length of the string minus the largest window
        return n - max_window_size
```

---

### 🛠 **Example Walkthrough**

Let’s work through an example step-by-step to fully understand the solution.

#### Example 1:  
**Input:**  
`s = "abbcabc"`  
`k = 2`  

**Steps:**

1. **Initial Frequency Count:**  
   ```
   {'a': 2, 'b': 3, 'c': 2}
   ```
   Each character occurs at least `k` times. Proceed to the sliding window.

2. **Sliding Window Process:**  
   - Start with two pointers `left = 0` and `right = 0`.
   - Iterate over the string:
   
   | `right` | Removed | Adjust `left` | Updated Counts           | Max Window |
   |---------|---------|---------------|--------------------------|------------|
   | 0       | `'a'`   | No            | `{'a': 1, 'b': 3, 'c': 2}` | 1          |
   | 1       | `'b'`   | No            | `{'a': 1, 'b': 2, 'c': 2}` | 2          |
   | 2       | `'b'`   | Yes           | `{'a': 2, 'b': 2, 'c': 2}` | 2          |
   | 3       | `'c'`   | No            | `{'a': 1, 'b': 2, 'c': 1}` | 3          |
   | ...     | ...     | ...           | ...                      | ...        |
   
3. **Result Calculation:**  
   - Largest valid window found: **"bca"** (size 3).  
   - Total string length: `7`.  
   - Answer: `7 - 3 = 4`.  

**Output:**  
`4`

---

### 🔬 **Complexity Analysis**

1. **Time Complexity:**  
   - The outer loop iterates over the string once (`O(n)`).
   - The inner loop adjusts the left pointer, but it only progresses forward, processing each character once (`O(n)`).
   - Total time complexity: **`O(n)`**.

2. **Space Complexity:**  
   - The space used by the `Counter` is constant (`O(1)`), as we only track counts for `'a'`, `'b'`, and `'c'`.
   - Total space complexity: **`O(1)`**.

---

### 🌟 **Advantages of the Sliding Window Approach**

- **Efficiency:** Linear time complexity ensures the solution scales well with longer strings.
- **Dynamic Adjustment:** The two-pointer technique dynamically adjusts the boundaries of the removable window, ensuring minimal removals.

---

### 🧮 **Edge Cases**

1. **Insufficient Characters:**  
   Input: `s = "aabbcc", k = 3`  
   Output: `-1` (impossible to satisfy the condition).

2. **Already Satisfied:**  
   Input: `s = "aaa", k = 1`  
   Output: `0` (no removals needed).

3. **Single Character Repeats:**  
   Input: `s = "aaaaaa", k = 2`  
   Output: `4` (remove 4 characters).

---

### 💡 **Conclusion**

The sliding window method offers a **clean and efficient** solution to this problem, focusing on the key task of finding the largest valid removable window. With **O(n)** time complexity, it performs well on large inputs and handles edge cases gracefully. This approach demonstrates the power of dynamic window adjustment in solving substring problems efficiently.
