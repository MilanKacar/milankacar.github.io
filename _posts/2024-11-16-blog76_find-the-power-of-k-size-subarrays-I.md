---
layout: post  
title: "#76 🔢 3254. Find the Power of K-Size Subarrays I 🧠🚀"   
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Sliding Window]
---

Welcome to another exciting blog post! 🚀 In this problem, we're exploring the **power of subarrays**. It’s all about finding whether elements are sorted and consecutive while dealing with dynamic arrays of size `k`. Along the way, you'll enhance your problem-solving skills and coding mastery! Let’s jump into it! 💻🤓

---

## 🧩 Problem Statement  
You are given:  
1️⃣ An array of integers `nums` of length `n`.  
2️⃣ A positive integer `k`.

The **power of an array** is defined as:  
- Its **maximum element** if all elements are consecutive integers and sorted in ascending order.  
- `-1` otherwise.  

Your goal is to find the power of **all subarrays of size `k`** and return the results as an array of size `n - k + 1`.  

---

## 🌟 Examples  

### Example 1  
**Input**:  
```python
nums = [1, 2, 3, 4, 3, 2, 5]  
k = 3
```  
**Output**:  
```python
[3, 4, -1, -1, -1]
```  

**Explanation**:  
- Subarray `[1, 2, 3]`: Consecutive, max = 3 ✅  
- Subarray `[2, 3, 4]`: Consecutive, max = 4 ✅  
- Subarray `[3, 4, 3]`: Not consecutive ❌  
- Subarray `[4, 3, 2]`: Not sorted ❌  
- Subarray `[3, 2, 5]`: Not consecutive ❌  

### Example 2  
**Input**:  
```python
nums = [2, 2, 2, 2, 2]  
k = 4
```  
**Output**:  
```python
[-1, -1]
```  

**Explanation**:  
- Subarray `[2, 2, 2, 2]`: Not consecutive ❌  
- Subarray `[2, 2, 2, 2]`: Not consecutive ❌  

### Example 3  
**Input**:  
```python
nums = [3, 2, 3, 2, 3, 2]  
k = 2
```  
**Output**:  
```python
[-1, 3, -1, 3, -1]
```  

---

## 🛠️ Constraints  
- $$ 1 \leq n = \text{nums.length} \leq 500 $$  
- $$ 1 \leq \text{nums}[i] \leq 10^5 $$  
- $$ 1 \leq k \leq n $$  

---

## 💡 Approach  

The problem asks us to analyze consecutive subarrays of size `k` to determine their "power." This involves:  
1️⃣ Checking if the subarray is **sorted in ascending order**.  
2️⃣ Verifying if elements are **consecutive integers**.  
3️⃣ Returning the **maximum element** if conditions are met; otherwise, return `-1`.  

---

### 🚀 Basic Solution: Brute Force  

In the brute force approach:  
1. Iterate through all subarrays of size `k`.  
2. For each subarray, check if it's sorted and consecutive.  
3. Return the maximum element if valid; else, return `-1`.

---

### 💻 Code for Brute Force Solution  
```python
class Solution:
    def resultsArray(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        results = []
        
        for i in range(n - k + 1):
            subarray = nums[i:i + k]
            sorted_subarray = sorted(subarray)
            
            # Check if sorted and consecutive
            if sorted_subarray == list(range(sorted_subarray[0], sorted_subarray[0] + k)):
                results.append(max(subarray))
            else:
                results.append(-1)
        
        return results
```

---

### ⏳ Time Complexity of Brute Force  
- Sorting each subarray of size `k`: $$ O(k \log k) $$  
- For $$ n - k + 1 $$ subarrays: $$ O((n - k + 1) \cdot k \log k) $$.  
- **Overall Time Complexity**: $$ O(n \cdot k \log k) $$.  

---

### 🌟 Optimized Solution  

Using an **efficient prefix-based approach**, we can track if numbers are consecutive:  
1. Use a prefix array `f`, where `f[i]` indicates if the current number is part of a consecutive sequence.  
2. Traverse the array only once, making the solution linear.

---

### 💻 Code for Optimized Solution  
```python
class Solution:
    def resultsArray(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        f = [1] * n  # Prefix array to track consecutive sequence
        
        # Build the prefix array
        for i in range(1, n):
            if nums[i] == nums[i - 1] + 1:
                f[i] = f[i - 1] + 1
        
        # Determine results for subarrays of size k
        return [nums[i] if f[i] >= k else -1 for i in range(k - 1, n)]
```

---

### ⏳ Time Complexity of Optimized Solution  
- Building the prefix array: $$ O(n) $$  
- Constructing the results array: $$ O(n) $$.  
- **Overall Time Complexity**: $$ O(n) $$.  

---

### 📖 Example Walkthrough  

#### Input:  
```python
nums = [1, 2, 3, 4, 3, 2, 5]
k = 3
```

#### Step 1: Build Prefix Array  
- Initialize `f = [1, 1, 1, 1, 1, 1, 1]`.  
- Traverse `nums` to check consecutive elements:  
  - $$ f[1] = 2 $$ since $$ nums[1] = nums[0] + 1 $$.  
  - $$ f[2] = 3 $$ since $$ nums[2] = nums[1] + 1 $$.  
  - $$ f[3] = 4 $$ since $$ nums[3] = nums[2] + 1 $$.  
  - $$ f[4] = 1 $$ since $$ nums[4] \neq nums[3] + 1 $$.  

#### Step 2: Construct Results Array  
- For $$ i = 2 $$ (subarray `[1, 2, 3]`): Power = 3.  
- For $$ i = 3 $$ (subarray `[2, 3, 4]`): Power = 4.  
- For $$ i = 4 $$ (subarray `[3, 4, 3]`): Power = -1.  

**Final Output**:  
```python
[3, 4, -1, -1, -1]
```

---

## 📚 Edge Cases  

1️⃣ **Smallest Inputs**:  
- $$ nums = [1], k = 1 $$: Always return `nums`.  

2️⃣ **No Consecutive Elements**:  
- $$ nums = [10, 20, 30], k = 2 $$: All elements will return `-1`.  

3️⃣ **All Same Elements**:  
- $$ nums = [5, 5, 5], k = 2 $$: Always return `-1`.  

---

## 🎉 Conclusion  

In this post, we explored how to efficiently compute the power of $$ k $$-size subarrays. By leveraging prefix arrays, we reduced the time complexity from $$ O(n \cdot k \log k) $$ to $$ O(n) $$. Whether you're preparing for an interview or just expanding your coding arsenal, this problem is a great way to practice **sliding window**, **prefix arrays**, and **array manipulation**! 🚀✨  

Happy coding! 💻🎉
