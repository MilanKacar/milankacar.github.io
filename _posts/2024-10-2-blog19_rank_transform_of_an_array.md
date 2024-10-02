
---
layout: post
title: "#19 1331. Rank Transform of an Array 🚀"
categories: [LeetCode, Programming]
---

Hey there, coding enthusiasts! 👋 Today, we’re going to tackle a simple yet interesting problem: ranking the elements of an array based on their values. Sounds fun, right? Let's dive in! 🚀

### Problem Statement 📋

Given an array of integers `arr`, replace each element with its **rank**. The rank follows these rules:

1. **Rank** is an integer starting from 1.
2. The larger the element, the larger its rank.
3. If two elements are equal, their rank must be the same.
4. **Rank** should be as small as possible.

### Example 🧐

- **Example 1**:
  ```plaintext
  Input: arr = [40, 10, 20, 30]
  Output: [4, 1, 2, 3]
  Explanation: 
    - 40 is the largest → rank 4.
    - 10 is the smallest → rank 1.
    - 20 is the second smallest → rank 2.
    - 30 is the third smallest → rank 3.
  ```

- **Example 2**:
  ```plaintext
  Input: arr = [100, 100, 100]
  Output: [1, 1, 1]
  Explanation: All elements are the same, so they share the same rank.
  ```

- **Example 3**:
  ```plaintext
  Input: arr = [37, 12, 28, 9, 100, 56, 80, 5, 12]
  Output: [5, 3, 4, 2, 8, 6, 7, 1, 3]
  ```

### 🛠️ Optimized Solution

Let’s walk through the efficient solution step-by-step.

#### Steps:

1. **Sort the array** to determine the rank of each element.
2. **Map each unique element** to its rank.
3. **Replace the elements** in the original array with their corresponding ranks.

Here’s the Python implementation:

```python
def arrayRankTransform(arr):
    if not arr:
        return []
    
    # Step 1: Create a sorted list of unique elements
    sorted_unique = sorted(set(arr))
    
    # Step 2: Create a dictionary mapping element to its rank
    rank_map = {val: rank + 1 for rank, val in enumerate(sorted_unique)}
    
    # Step 3: Replace each element in the original array with its rank
    return [rank_map[num] for num in arr]
```

### Explanation 🧑‍🏫

- **Step 1**: We create a sorted list of unique elements from the array, as the rank depends on the order of distinct values.
- **Step 2**: Using `enumerate()`, we assign a rank (starting from 1) to each unique value and store this in a dictionary.
- **Step 3**: We replace each element in the original array with its corresponding rank from the dictionary.

### Time Complexity ⏳

- **Time Complexity**: The solution runs in **O(n log n)**, where `n` is the length of the array. This is due to sorting.
- **Space Complexity**: **O(n)** for storing the rank map and the unique sorted elements.

### Conclusion 🎯

In this problem, we solved the task by sorting the unique elements and mapping them to their ranks. This efficient solution works well within the constraints and handles edge cases like duplicates smoothly. 🎉

### Key Takeaways 📝

- **Sorting** is the key technique to rank elements.
- We use a dictionary to map unique elements to their ranks for quick lookup.
- The time complexity is **O(n log n)**, which is optimal for this problem.

That’s it! I hope this explanation helps you understand the **Rank Transform of an Array** problem. Keep practicing and coding! 💻🔥
