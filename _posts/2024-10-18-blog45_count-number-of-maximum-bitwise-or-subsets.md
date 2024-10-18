---
layout: post
title: "#45 2044. Count Number of Maximum Bitwise-OR Subsets 🧠🚀"
categories: [LeetCode, Programming]
---

Ever wanted to unlock the **maximum power** from an array using just bitwise operations? 💡 Well, today’s problem gives us exactly that opportunity! In this exciting challenge, we need to find the **maximum possible bitwise OR** from all the non-empty subsets of an array, and count how many subsets can achieve that value! 🚀

It’s like assembling a powerful weapon 🔧 by combining different elements of the array in the most efficient way possible! ⚡ If you love bitwise magic or just want to solve problems smartly, then let’s dive in together! 🧠

---

### 📝 Problem Statement
Given an integer array `nums`, your task is to find the maximum possible bitwise OR of a subset of `nums` and return the number of different non-empty subsets with this maximum bitwise OR. Remember, subsets are considered different if their elements come from different indices.

The **bitwise OR** of a subset is calculated by performing the `OR` operation between all elements in the subset.

#### Example 1:
**Input**: `nums = [3,1]`  
**Output**: `2`  
**Explanation**: The maximum possible bitwise OR is `3`. There are 2 subsets with a bitwise OR of `3`:
- `[3]`
- `[3,1]`

#### Example 2:
**Input**: `nums = [2,2,2]`  
**Output**: `7`  
**Explanation**: All non-empty subsets have a bitwise OR of `2`. There are `2^3 - 1 = 7` non-empty subsets.

#### Example 3:
**Input**: `nums = [3,2,1,5]`  
**Output**: `6`  
**Explanation**: The maximum possible bitwise OR is `7`. There are 6 subsets with a bitwise OR of `7`:
- `[3,5]`
- `[3,1,5]`
- `[3,2,5]`
- `[3,2,1,5]`
- `[2,5]`
- `[2,1,5]`

---

### 💡 Basic Solution
The simplest approach is to:
1. **Generate all subsets** of the array.
2. **Compute the OR** for each subset.
3. **Count** the number of subsets whose OR matches the maximum OR we can obtain.

#### Time Complexity
Since we are generating all possible subsets, the time complexity is  O(n \cdot 2^n) , where  n  is the number of elements in the array.

---

#### Python Code
```python
from itertools import combinations

def countMaxOrSubsets(nums):
    max_or = 0
    n = len(nums)
    
    # Calculate the max OR value from the entire array
    for num in nums:
        max_or |= num
    
    count = 0
    # Generate all subsets and check their OR values
    for r in range(1, n+1):
        for subset in combinations(nums, r):
            current_or = 0
            for num in subset:
                current_or |= num
            if current_or == max_or:
                count += 1
    
    return count
```

### 🕒 Time Complexity of Basic Solution
- **Time Complexity**:  O(n \cdot 2^n) , because we generate  2^n  subsets and then for each subset, we perform the bitwise OR operation.
  
---

### 🚀 Optimized Solution
We can optimize the approach using **Depth-First Search (DFS)**. Instead of generating all subsets directly, we traverse through the array while maintaining the current OR value. This reduces redundant recalculations and helps us explore subsets more efficiently.

#### Time Complexity
Even though we still explore all subsets, we avoid recalculating the OR for each subset from scratch. Thus, the time complexity remains  O(n \cdot 2^n) , but the optimized DFS reduces computational overhead.

---

#### Optimized Python Code
```python
def countMaxOrSubsets(nums):
    max_or = 0
    n = len(nums)
    
    # Calculate the maximum OR value for the entire array
    for num in nums:
        max_or |= num
    
    count = 0

    # DFS to find all subsets
    def dfs(index, current_or):
        nonlocal count
        if index == n:
            if current_or == max_or:
                count += 1
            return
        # Include the current number
        dfs(index + 1, current_or | nums[index])
        # Exclude the current number
        dfs(index + 1, current_or)
    
    dfs(0, 0)
    return count
```

### 🕒 Time Complexity of Optimized Solution
- **Time Complexity**:  O(n \cdot 2^n) , due to subset exploration, but DFS minimizes redundant OR recalculations.

---

### 🧩 Example Walkthrough

Let’s break down an example step-by-step:

**Input**: `[3, 2, 1, 5]`

1. **Step 1: Calculate Maximum OR**:  
   - Initial OR:  0 
   - After  3 :  0 \,|\, 3 = 3 
   - After  2 :  3 \,|\, 2 = 3  
   - After  1 :  3 \,|\, 1 = 3 
   - After  5 :  3 \,|\, 5 = 7   
   So, the **maximum OR** is  7 .

2. **Step 2: Find Subsets with OR = 7**:  
   We now explore subsets that yield this maximum OR:
   - Subset `[3,5]` has OR  7 
   - Subset `[3,1,5]` has OR  7 
   - Subset `[3,2,5]` has OR  7 
   - Subset `[3,2,1,5]` has OR  7 
   - Subset `[2,5]` has OR  7 
   - Subset `[2,1,5]` has OR  7   
   
   There are **6 subsets** with an OR of  7 .

---

### 🏁 Conclusion
- The **basic solution** works by brute-forcing all possible subsets and checking their OR values, but it’s slower for larger inputs. ⌛
- The **optimized DFS solution** improves efficiency by avoiding unnecessary OR recalculations, though the overall time complexity  O(n \cdot 2^n)  remains the same due to the need to explore all subsets.
  
In the end, we’ve successfully counted the number of subsets with the **maximum bitwise OR**—and used both brute force 💪 and smart DFS 🔥 to get there!

