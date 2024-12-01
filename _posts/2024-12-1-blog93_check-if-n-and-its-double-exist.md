---
layout: post  
title: "#93 1346. Check If N and Its Double Exist 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Easy
tags: [Array HashTable, Two Pointers, Binary Search, Sorting]
---

In this blog post, we'll explore **LeetCode problem #1346: Check If N and Its Double Exist**. This problem challenges us to think critically about array manipulation and efficient lookups. We'll take a deep dive into solving this problem with a detailed explanation, optimized solutions, and an in-depth example walkthrough. Plus, there will be plenty of **emojis** to keep it engaging! Let's get started. 🚀

---

## 📝 Problem Statement  

Given an array `arr` of integers, determine whether there exist **two distinct indices** `i` and `j` such that:

1. $$ i \neq j $$
2. $$ 0 \leq i, j < \text{arr.length} $$
3. $$ \text{arr}[i] = 2 \times \text{arr}[j] $$

### Examples  

#### Example 1  
**Input:**  
`arr = [10, 2, 5, 3]`  
**Output:**  
`true`  

**Explanation:**  
For $$ i = 0 $$ and $$ j = 2 $$:  
$$
\text{arr}[i] = 10 \quad \text{and} \quad \text{arr}[j] = 5 \quad \Rightarrow \quad 10 = 2 \times 5
$$  

#### Example 2  
**Input:**  
`arr = [3, 1, 7, 11]`  
**Output:**  
`false`  

**Explanation:**  
No pair of indices $$ i $$ and $$ j $$ satisfy the given conditions.  

---

## 🛠 Constraints  

- $$ 2 \leq \text{arr.length} \leq 500 $$
- $$ -1000 \leq \text{arr}[i] \leq 1000 $$

---

## 🚀 Approach  

To solve the problem effectively, we need to focus on **efficiency** and **correctness**. This involves selecting the right data structures and designing an approach that minimizes unnecessary computations. Let's break it down step by step.  

---

### 🧠 Understanding the Problem  

The problem requires us to determine if there exist two indices $$ i $$ and $$ j $$ such that:  
- $$ i \neq j $$
- $$ \text{arr}[i] = 2 \times \text{arr}[j] $$  

This boils down to finding **pairs** of elements in the array where one is exactly double the other. To do this:  
- We must iterate through the array and compare elements efficiently.  
- We need a way to check if a value (or its double or half) exists in the array **quickly**.  

A naive **brute-force** approach would involve comparing each element with every other element. However, this would take $$ O(n^2) $$ time and is impractical for larger arrays.  

---

### 🔑 Key Insights  

1. **Leverage a Hashmap:**  
   A hashmap (or dictionary in Python) allows **constant-time lookups**. By storing each element of the array in a hashmap as we iterate, we can check if the double or half of the current number already exists in the hashmap. This reduces the complexity to $$ O(n) $$.  

2. **Simultaneous Checking and Storing:**  
   As we traverse the array, for each element:  
   - Check if $$ 2 \times \text{current_num} $$ is in the hashmap.  
   - Check if $$ \text{current_num} 2 $$ is in the hashmap.  
   - If either condition is true, return `True`.  
   - Otherwise, add the current number to the hashmap and proceed.  

3. **Edge Cases:**  
   - **Zero Handling:** Since $$ 0 = 2 \times 0 $$, we need to ensure we don’t falsely trigger on a single zero. If there are two or more zeros, we return `True`.  
   - **Negative Numbers:** The logic should work for negative numbers since multiplication and division rules are consistent.  

---

### 💾 Data Structures Used  

1. **Hashmap (Dictionary in Python):**  
   - **Purpose:** Store elements as keys and allow quick lookups to check if the double or half of a number exists.  
   - **Efficiency:** Lookups and insertions are $$ O(1) $$ on average due to hashing.  

2. **Array (Input):**  
   - The input is traversed linearly, and each element is processed once.  

---

### 🌟 Why Hashmaps Are Ideal  

Hashmaps are an excellent choice for this problem because:  
- They allow for **constant-time lookups**, ensuring that we can efficiently check for $$ 2 \times \text{current_num} $$ or $$ \text{current_num} 2 $$.  
- They reduce the need for nested loops, which would significantly increase runtime for larger arrays.  
- They maintain the order of insertion implicitly, although that’s not necessary for this problem.  

---

### 🛠 Steps in the Approach  

1. **Initialization:**  
   - Create an empty hashmap `hm` to store elements encountered so far.  

2. **Iterate Through the Array:**  
   - For each number in the array:  
     - Check if $$ 2 \times \text{num} $$ or $$ \text{num} / 2 $$ exists in `hm`.  
     - If either condition is true, return `True`.  
     - Otherwise, add the current number to the hashmap.  

3. **Return Result:**  
   - If the iteration completes without finding a valid pair, return `False`.  

---

### 🔍 Detailed Execution  

Let’s revisit the example `arr = [10, 2, 5, 3]` to walk through the approach:  

#### Step 1: Initialize Hashmap  
Start with an empty hashmap: `hm = {}`.  

#### Step 2: Process Each Element  
1. For $$ \text{num} = 10 $$:  
   - $$ 2 \times 10 = 20 $$, not in `hm`.  
   - $$ 10 / 2 = 5 $$, not in `hm`.  
   - Add $$ 10 $$ to `hm`: `hm = {10: True}`.  

2. For $$ \text{num} = 2 $$:  
   - $$ 2 \times 2 = 4 $$, not in `hm`.  
   - $$ 2 / 2 = 1 $$, not in `hm`.  
   - Add $$ 2 $$ to `hm`: `hm = {10: True, 2: True}`.  

3. For $$ \text{num} = 5 $$:  
   - $$ 2 \times 5 = 10 $$, **exists in `hm`!**  
   - Return `True`.  

---

### 🔄 Handling Edge Cases  

1. **Multiple Zeros:**  
   - If there are at least two zeros, return `True` since $$ 0 = 2 \times 0 $$.  
   - **Example:**  
     - Input: `arr = [0, 0]`  
     - Output: `True`  

2. **Single Element:**  
   - If the array has fewer than two elements, it’s impossible to satisfy $$ i \neq j $$.  
   - **Example:**  
     - Input: `arr = [1]`  
     - Output: `False`  

3. **Negative Numbers:**  
   - The algorithm works for negatives due to consistent multiplication and division rules.  
   - **Example:**  
     - Input: `arr = [-2, -4, -8]`  
     - Output: `True`  

4. **No Valid Pair:**  
   - If no such pair exists in the array, return `False`.  
   - **Example:**  
     - Input: `arr = [3, 1, 7, 11]`  
     - Output: `False`  

---

### ⚖️ Comparing Brute Force vs. Optimized Approach  

| **Aspect**       | **Brute Force**          | **Optimized (Hashmap)**       |
|-------------------|--------------------------|--------------------------------|
| **Time Complexity** | $$ O(n^2) $$             | $$ O(n) $$                    |
| **Space Complexity** | $$ O(1) $$ (no extra storage) | $$ O(n) $$ (for hashmap)       |
| **Performance**   | Slow for large inputs    | Fast and scalable             |
| **Implementation** | Nested loops, verbose   | Single loop, concise          |

The hashmap-based solution is significantly more efficient and clean to implement.  

---

### 🚀 Why This Approach Works  

- **Efficiency:** Hashmaps allow quick lookups, reducing the need for nested iterations.  
- **Simplicity:** The algorithm is straightforward to implement and understand.  
- **Scalability:** Even with larger arrays (up to 500 elements), this solution performs efficiently.  

By using a hashmap, we ensure that we process each element only once and perform constant-time operations for lookups and insertions. This is why this approach is optimal for the problem. 🎉
---

## ✨ Solution  

### Optimized Solution  

We can solve the problem using a **hashmap** to store the elements of the array as we iterate through it. For every element $$ \text{arr}[i] $$, we check if its double ($$ 2 \times \text{arr}[i] $$) or half ($$ \text{arr}[i] / 2 $$) exists in the hashmap. If either condition is true, we return `True`. Otherwise, we continue processing.  

---

### Python Code  

```python
from typing import List

class Solution:
    def checkIfExist(self, arr: List[int]) -> bool:
        hm = {}
        for num in arr:
            if num * 2 in hm or num / 2 in hm:
                return True
            hm[num] = True
        return False
```

---

## 🕒 Time Complexity  

- **Optimized Solution:** $$ O(n) $$  
  - We iterate through the array once, and the hashmap operations (lookup, insertion) are $$ O(1) $$.  
- **Space Complexity:** $$ O(n) $$  
  - We store all array elements in the hashmap.

---

## 🔎 Example Walkthrough  

### Example: `arr = [10, 2, 5, 3]`

#### Step-by-Step Execution  

1. **Initialize hashmap `hm`:** Start with an empty hashmap: `{}`.  
2. **Iterate through the array:**  
    - For $$ \text{num} = 10 $$:  
      - $$ 2 \times 10 = 20 $$, not in `hm`.  
      - $$ 10 / 2 = 5 $$, not in `hm`.  
      - Add $$ 10 $$ to `hm`: `{10: True}`.  
    - For $$ \text{num} = 2 $$:  
      - $$ 2 \times 2 = 4 $$, not in `hm`.  
      - $$ 2 / 2 = 1 $$, not in `hm`.  
      - Add $$ 2 $$ to `hm`: `{10: True, 2: True}`.  
    - For $$ \text{num} = 5 $$:  
      - $$ 2 \times 5 = 10 $$, **exists in `hm`!** Return `True`.  

**Output:** `True`

---

## 🧪 Edge Cases  

1. **Negative Numbers:** The solution should handle negatives correctly.  
   - **Input:** `arr = [-2, -4, -8]`  
     **Output:** `True`  
2. **Zeros:** If the array contains more than one zero, return `True` since $$ 0 = 2 \times 0 $$.  
   - **Input:** `arr = [0, 0]`  
     **Output:** `True`  
3. **Single Element Array:** With fewer than two elements, the conditions cannot be satisfied.  
   - **Input:** `arr = [1]`  
     **Output:** `False`  

---

## 🏁 Conclusion  

This problem is a great example of using hashmaps for efficient lookups in linear time. The optimized approach leverages $$ O(1) $$ hashmap operations to reduce computational overhead. 💡  

### Key Takeaways:  

- **Hashmaps** are powerful for reducing search complexity from $$ O(n^2) $$ to $$ O(n) $$.  
- Always consider **edge cases**, such as zeros and negatives.  
- Properly analyze and understand constraints to avoid unnecessary computations.  

I hope you enjoyed solving this problem with me! 🎉 If you found it helpful, let me know. 😊
