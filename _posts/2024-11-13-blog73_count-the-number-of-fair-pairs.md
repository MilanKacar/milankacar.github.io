---
layout: post  
title: "#73 🧮 2563. Count the Number of Fair Pairs 🧠🚀"  
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Two Pointers, Binary Search, Sorting]
date: 2024-11-13
---

Counting pairs 🧮 in an array that meet specific conditions can be quite tricky—especially when large numbers are involved! In this post, we'll dive into finding *fair pairs* where the sum of two elements falls within a given range. With sorted arrays and two-pointer techniques, we'll show how to do this efficiently!

---

### Problem Statement 📝

Given:
- A **0-indexed integer array** `nums` of size `n`.
- Two integers `lower` and `upper`.

Return the number of *fair pairs* `(i, j)` in `nums`, where:
1. `0 <= i < j < n`, and
2. `lower <= nums[i] + nums[j] <= upper`

#### Constraints:
- `1 <= nums.length <= 10^5`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= lower <= upper <= 10^9`

#### Example

**Example 1**:
```plaintext
Input: nums = [0,1,7,4,4,5], lower = 3, upper = 6
Output: 6
Explanation: There are 6 fair pairs: (0,3), (0,4), (0,5), (1,3), (1,4), and (1,5).
```

**Example 2**:
```plaintext
Input: nums = [1,7,9,2,5], lower = 11, upper = 11
Output: 1
Explanation: There is a single fair pair: (2,3).
```

---

### Solution Approach 💡

#### 1. Brute Force (Basic Solution) 🔍

The simplest way to solve this problem is by checking every possible pair `(i, j)` in the array to see if they form a fair pair. This approach is straightforward but inefficient for large inputs due to a time complexity of `O(n^2)`.

#### Brute Force Solution 🔧
Here's a basic implementation of this brute-force approach:

```python
def count_fair_pairs(nums, lower, upper):
    count = 0
    n = len(nums)
    for i in range(n):
        for j in range(i + 1, n):
            if lower <= nums[i] + nums[j] <= upper:
                count += 1
    return count
```

#### Complexity Analysis 🕰️
- **Time Complexity**: `O(n^2)`, as it involves iterating over all pairs in `nums`.
- **Space Complexity**: `O(1)`, since we're using only a few extra variables.

#### Why Brute Force is Inefficient 🔍
While the brute-force solution works fine for small arrays, it will quickly become slow for large inputs due to the `O(n^2)` complexity. Given that `n` can be up to `10^5`, this approach would result in over `10^10` operations—unacceptable for competitive programming.

---

### Optimized Solution 💡

#### Step-by-Step Explanation 🔍

To reduce the time complexity, let's leverage **sorting** and the **two-pointer technique**. By sorting the array, we can efficiently find pairs that meet the condition using two pointers.

1. **Sort the Array**: First, sort `nums` in ascending order.
2. **Two-Pointer Technique**:
   - For each element in `nums`, treat it as a potential starting point of a pair.
   - Use two pointers to check pairs efficiently within the bounds `lower` and `upper`.
3. **Binary Search for Bounds**:
   - For each `nums[i]`, use binary search to locate the range of values that form a fair pair with `nums[i]`.

#### Optimized Code Implementation 🧑‍💻

Here’s how we implement this in Python:

```python
from bisect import bisect_left, bisect_right

def count_fair_pairs(nums, lower, upper):
    nums.sort()
    count = 0
    n = len(nums)
    
    for i in range(n):
        # Define target range
        left_bound = lower - nums[i]
        right_bound = upper - nums[i]
        
        # Find the range of valid pairs
        left_idx = bisect_left(nums, left_bound, i + 1, n)
        right_idx = bisect_right(nums, right_bound, i + 1, n) - 1
        
        # Count all elements within the bounds
        count += max(0, right_idx - left_idx + 1)
        
    return count
```

#### Complexity Analysis 🕰️

- **Time Complexity**: `O(n log n)` for sorting + `O(n log n)` for binary searching within each iteration. This gives a total complexity of `O(n log n)`.
- **Space Complexity**: `O(1)` if sorting is done in place, or `O(n)` if using additional space for sorted elements.

#### Why This Solution is Efficient 🌟

The use of binary search and sorting allows us to drastically reduce the number of comparisons needed. By only checking within a limited range defined by `lower` and `upper`, we avoid unnecessary operations.

---

### Example Walkthrough 📚

Let's walk through an example with a high number count to understand how the optimized solution works:

#### Example
```plaintext
Input: nums = [2, 3, 6, 8, 12, 15], lower = 10, upper = 18
```

1. **Sorting**: The array is already sorted.
2. **Counting Fair Pairs**:
   - For `nums[0] = 2`, target range is `[10 - 2, 18 - 2] = [8, 16]`. Use binary search to find pairs.
   - Continue similarly for other values in the array.

---

### Edge Cases 🧪

1. **Single Element**: If `n = 1`, no pairs can be formed, so return `0`.
2. **All Elements Same**: If `nums` contains identical values, the algorithm should still count pairs within `lower` and `upper`.
3. **Wide Range**: If `lower = -10^9` and `upper = 10^9`, all pairs should be counted.
4. **No Valid Pairs**: If no two elements in `nums` satisfy `lower <= sum <= upper`, the result should be `0`.

---

### Conclusion 🎉

In this post, we explored two approaches to solving **LeetCode Problem #2563: "Count the Number of Fair Pairs"**:
- A **brute-force solution** that checks all pairs but has an impractical time complexity for large inputs.
- An **optimized solution** that leverages sorting and binary search to achieve a feasible `O(n log n)` complexity.

The optimized solution is ideal for larger arrays, using efficient searching to quickly count valid pairs.

Whether you're brushing up on two-pointer techniques or refining your binary search skills, mastering this problem helps build a solid foundation for competitive programming!
