---
layout: post
title: "#21 1590. 🧮 Make Sum Divisible by P Using the Smallest Subarray Removal 🚀"
categories: [LeetCode, Programming]
---

In this blog post, we’ll solve an interesting problem where the goal is to remove the smallest subarray from a list of integers such that the remaining sum is divisible by a given number, `p`. We'll explore both the problem and its optimized solution using a hash map.

Let’s break it down step by step! 🚀

---

## 📝 Problem Statement

We are given an array of positive integers `nums` and an integer `p`. Our task is to **remove the smallest subarray** (possibly empty) such that the sum of the remaining elements is divisible by `p`. 

- If it's **not possible** to make the sum divisible by `p`, we return `-1`.
- A subarray is defined as a contiguous block of elements in the array.

### Example 1:
```python
Input: nums = [3,1,4,2], p = 6
Output: 1
Explanation: The sum of nums is 10, which is not divisible by 6. 
We can remove the subarray [4], and the sum of the remaining elements is 6, which is divisible by 6.
```

### Example 2:
```python
Input: nums = [6,3,5,2], p = 9
Output: 2
Explanation: We cannot remove a single element to get a sum divisible by 9. The best way is to remove the subarray [5,2].
```

### Example 3:
```python
Input: nums = [1,2,3], p = 3
Output: 0
Explanation: The sum of nums is 6, which is already divisible by 3. 
No need to remove anything.
```

---

Here's the basic solution, which solves the problem in a straightforward but inefficient manner. It uses brute force to check every possible subarray and tries to remove it, checking if the sum of the remaining elements is divisible by `p`.

---

## 📝 Basic Solution: Brute Force Approach

The basic idea is to iterate over every possible subarray, remove it, and check if the remaining sum is divisible by `p`. This approach works for small inputs but is inefficient for larger arrays due to its time complexity.

### 🐍 Basic Python Code:

```python
class Solution:
    def minSubarray(self, nums: List[int], p: int) -> int:
        total_sum = sum(nums)
        remainder = total_sum % p
        
        # If the total sum is divisible by p, no removal needed
        if remainder == 0:
            return 0
        
        n = len(nums)
        min_len = n
        
        # Try removing every possible subarray
        for i in range(n):
            for j in range(i, n):
                subarray_sum = sum(nums[i:j+1])
                if (total_sum - subarray_sum) % p == 0:
                    min_len = min(min_len, j - i + 1)
        
        # Return -1 if no valid subarray was found
        return min_len if min_len < n else -1
```

### 🔍 Explanation of the Code:
1. **Calculate Total Sum**: First, we calculate the total sum of the array and check its remainder when divided by `p`.
2. **Early Exit**: If the total sum is divisible by `p`, we return `0` since no subarray needs to be removed.
3. **Brute Force Subarray Removal**: We iterate through every possible subarray `(i, j)`. For each subarray, we calculate its sum and check if removing it makes the remaining sum divisible by `p`.
4. **Track Minimum Length**: We track the length of the smallest subarray that can be removed and update the result accordingly.
5. **Final Check**: If no subarray can make the remaining sum divisible by `p`, we return `-1`.

### 🕒 Time Complexity:
- **O(n³)** because we calculate the sum of every possible subarray in a nested loop, making this approach very inefficient for larger arrays.

---

Feel free to experiment with both solutions and compare their performance! 😊
### Time Complexity:
- The brute force solution would involve checking all subarrays, leading to a **time complexity of O(n²)**, which can be inefficient when `n` (length of `nums`) is large.

---

## ⚡ Optimized Solution (Using Hash Maps)

The optimized approach leverages **prefix sums** and a **hash map** to track remainders of the prefix sums modulo `p`. This allows us to find the smallest subarray that we can remove efficiently.

### Key Insights:
1. **Total Sum Modulo `p`**: We calculate the remainder of the total sum modulo `p`. If the remainder is `0`, no subarray needs to be removed.
2. **Prefix Sums and Hash Map**: We compute the prefix sums while iterating over the array. The goal is to find subarrays that, when removed, leave the remaining elements divisible by `p`. 
3. **Target Calculation**: For each prefix sum, we calculate a target remainder that needs to be removed to make the remaining sum divisible by `p`.

### 🐍 Optimized Python Code

```python
class Solution:
    def minSubarray(self, nums: List[int], p: int) -> int:
        total_sum = sum(nums)
        remainder = total_sum % p
        
        # If total sum is divisible by p, no removal needed
        if remainder == 0:
            return 0
        
        n = len(nums)
        min_len = n
        prefix_sum = 0
        prefix_map = {0: -1}  # To store the prefix sums and their indexes
        
        for i, num in enumerate(nums):
            prefix_sum = (prefix_sum + num) % p
            target = (prefix_sum - remainder) % p
            
            # Check if we can remove a subarray to make the remaining sum divisible by p
            if target in prefix_map:
                min_len = min(min_len, i - prefix_map[target])
            
            # Store the current prefix sum in the map
            prefix_map[prefix_sum] = i
        
        # Return -1 if no valid subarray is found
        return min_len if min_len < n else -1
```

### 🔍 Explanation of the Code:
1. **Calculate the total sum**: We compute the sum of all elements in the array and find the remainder when divided by `p`.
2. **Early Exit**: If the remainder is `0`, we don’t need to remove any elements, so we return `0`.
3. **Prefix Sum Calculation**: As we iterate through the array, we compute the prefix sum modulo `p` and store it in a hash map with the corresponding index.
4. **Find the Target**: For each prefix sum, we calculate the target remainder that needs to be removed. If this target exists in the hash map, it means we can remove a subarray and update the minimum length.
5. **Final Check**: If a valid subarray is found, we return the minimum length. Otherwise, return `-1` if no subarray can make the sum divisible by `p`.

### 🕒 Time Complexity:
- **O(n)** where `n` is the length of the array. We only traverse the array once and perform constant-time lookups and insertions in the hash map.

---

## 🎯 Conclusion

- The optimized approach gives us a linear-time solution using a prefix sum and hash map, which is much more efficient than the brute force approach.
- This algorithm is particularly useful when working with large arrays, as it ensures we don't have to check every subarray manually.
- If the remainder after dividing the sum by `p` is non-zero, we efficiently track possible subarrays that can be removed using modular arithmetic.

Hope this helps you understand how to tackle problems involving divisible sums and subarray removals! 😄 Keep coding!
