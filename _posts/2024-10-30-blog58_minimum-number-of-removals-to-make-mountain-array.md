---
layout: post  
title: "#58 ⛰️ 1671. Minimum Number of Removals to Make Mountain Array 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Binary Search, Dynamic Programming, Greedy]
---

Creating a *mountain array* from an unordered sequence of numbers might seem complex at first, but the problem is ultimately about finding an efficient way to keep the “mountain” structure without losing too many elements. 🌄 This challenge boils down to identifying the longest possible sequence that forms a mountain and then calculating how many elements we need to remove to achieve this structure. This type of problem is a great exercise in applying **dynamic programming** techniques, as we’ll need to store and reuse values to optimize our solution. 

By the end of this post, you’ll understand:
1. The definition and characteristics of a mountain array.
2. How to break down the array into increasing and decreasing parts.
3. How to calculate the solution efficiently using **Longest Increasing Subsequence (LIS)** and **Longest Decreasing Subsequence (LDS)**.
4. Additional techniques for handling edge cases and interpreting complex test scenarios.

Let’s explore this problem in detail, with lots of emojis, examples, and step-by-step breakdowns! 🚀

---

### 📝 Problem Statement

Given an integer array `nums`, we want to make it a *mountain array* by removing the minimum number of elements possible. A mountain array meets the following conditions:

- **Length Requirement**: The array must contain at least three elements.
- **Structure**: There exists a peak element at index `i` such that:
  - From the start of the array to `i`, elements are strictly increasing.
  - From `i` to the end of the array, elements are strictly decreasing.
  - The peak element `nums[i]` should be the highest, satisfying `nums[i - 1] < nums[i] > nums[i + 1]`.

**Objective**: Find the minimum number of elements that need to be removed to transform `nums` into a mountain array.

---

### 🔍 Examples

Let’s look at a few examples to clarify the problem requirements:

**Example 1:**
```plaintext
Input: nums = [1,3,1]
Output: 0
Explanation: The array is already a mountain array, so no removals are necessary.
```

**Example 2:**
```plaintext
Input: nums = [2,1,1,5,6,2,3,1]
Output: 3
Explanation: By removing elements at indices 0, 1, and 5, we obtain the mountain array [1,5,6,3,1].
```

### 🧩 Approach and Solution

To solve this problem, let’s start by understanding the steps needed to maximize the *mountain-like* structure. The key insight is that we need to identify the **longest possible mountain subarray**. Once we know the length of this subarray, we can calculate how many elements we need to remove to leave only this mountain structure.

#### Breaking Down the Mountain Array Structure

To form a mountain array, we need:
1. **An increasing subsequence** up to the peak.
2. **A decreasing subsequence** from the peak to the end.

The essence of our solution is to maximize the length of the mountain by focusing on the *Longest Increasing Subsequence (LIS)* and *Longest Decreasing Subsequence (LDS)* for each index. Here’s how:

1. **Calculate LIS up to each element**: We’ll calculate the length of the longest strictly increasing sequence ending at each index. This sequence represents the ascending part up to each potential peak.
  
2. **Calculate LDS starting from each element**: Similarly, we’ll calculate the length of the longest strictly decreasing sequence starting from each index, representing the descending part from each potential peak.

#### Implementing the Solution

Here’s how we can break down the solution using these insights:

1. **Calculate LIS for each element from left to right**.
2. **Calculate LDS for each element from right to left**.
3. **Identify potential peaks**: Only indices where both LIS and LDS values are greater than 1 can serve as peaks.
4. **Calculate the length of the longest mountain**: For each valid peak, combine the LIS and LDS lengths to find the mountain length.
5. **Compute the number of removals**: Subtract the length of the longest mountain from the total number of elements in the array to find the minimum removals.

Let’s implement this step-by-step.

---

### 🚀 Solution Code

Here’s the Python code to calculate the minimum number of removals. This code handles each step, from calculating LIS and LDS to determining the minimum elements to remove.

```python
def minimumMountainRemovals(nums):
    n = len(nums)

    # Step 1: Longest Increasing Subsequence (LIS) from left to right
    lis = [1] * n
    for i in range(n):
        for j in range(i):
            if nums[i] > nums[j]:
                lis[i] = max(lis[i], lis[j] + 1)

    # Step 2: Longest Decreasing Subsequence (LDS) from right to left
    lds = [1] * n
    for i in range(n - 1, -1, -1):
        for j in range(i + 1, n):
            if nums[i] > nums[j]:
                lds[i] = max(lds[i], lds[j] + 1)

    # Step 3: Calculate the longest mountain length
    max_mountain_len = 0
    for i in range(1, n - 1):
        if lis[i] > 1 and lds[i] > 1:  # valid peak condition
            max_mountain_len = max(max_mountain_len, lis[i] + lds[i] - 1)

    # Minimum removals to get the mountain array
    return n - max_mountain_len
```

### ⏱️ Complexity Analysis

- **Time Complexity**: \(O(n^2)\) because calculating LIS and LDS for each element involves nested loops over the array.
- **Space Complexity**: \(O(n)\) for the `lis` and `lds` arrays.

### 🧪 Edge Cases

Consider the following edge cases to ensure your solution is robust:

1. **Already a Mountain**: If the array is already a mountain, we should return `0` as no removals are needed.
   - Input: `[1, 3, 5, 3, 1]`
   - Output: `0`

2. **All Equal Elements**: No mountain can form if all elements are the same.
   - Input: `[2, 2, 2, 2]`
   - Expected Output: The smallest mountain would need 3 elements, so `len(nums) - 3`.

3. **Single Peak Only**: If only increasing or only decreasing sequences are present, we should still remove elements to form the smallest possible mountain.
   - Input: `[1, 2, 3, 4]`
   - Expected Output: `1`, removing all but the last three elements to form a minimal mountain structure.

4. **Minimal Length Array**: If the array is exactly three elements and forms a mountain, we should return `0`.
   - Input: `[1, 3, 2]`
   - Output: `0`

---

### 🏁 Conclusion

This problem is a fantastic exercise for mastering **sequence transformations** and understanding the interplay of increasing and decreasing subsequences. By combining **dynamic programming** (DP) and **subsequence calculations**, we managed to identify the longest mountain subsequence efficiently.

📈 With the LIS and LDS technique, we were able to capture the essence of the problem, reducing our work to finding the longest mountain and subtracting its length from the total number of elements. This approach ensures we only make the minimum necessary changes while preserving the mountain structure. 🏔️
