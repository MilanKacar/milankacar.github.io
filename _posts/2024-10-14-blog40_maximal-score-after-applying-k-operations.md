---
layout: post  
title: "#40 2530. Maximal Score After Applying K Operations 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Greedy, Heap (Priority Queue)]
---

In this problem, we are tasked with maximizing the score by applying operations to an array. The goal is to select the maximum possible element, increase the score, and then replace it with its ceiling after dividing by 3. Let's break this down and find an efficient approach! 🚀

---

### Problem Statement

You are given a 0-indexed integer array `nums` and an integer `k`. You have a starting score of 0.

In one operation, you:

1. Choose an index `i` such that `0 <= i < nums.length`.
2. Increase your score by `nums[i]`.
3. Replace `nums[i]` with `ceil(nums[i] / 3)`.

Return the maximum possible score you can achieve after exactly `k` operations.

**Constraints:**

- `1 <= nums.length, k <= 10^5`
- `1 <= nums[i] <= 10^9`

---

### Example 1:

**Input:** `nums = [10,10,10,10,10]`, `k = 5`  
**Output:** `50`  
**Explanation:** Apply the operation to each array element exactly once. The final score is 10 + 10 + 10 + 10 + 10 = 50.

---

### Example 2:

**Input:** `nums = [1,10,3,3,3]`, `k = 3`  
**Output:** `17`  
**Explanation:**
- Operation 1: Select `i = 1`, so `nums` becomes `[1, 4, 3, 3, 3]`. Score increases by 10.
- Operation 2: Select `i = 1`, so `nums` becomes `[1, 2, 3, 3, 3]`. Score increases by 4.
- Operation 3: Select `i = 2`, so `nums` becomes `[1, 1, 1, 3, 3]`. Score increases by 3.
The final score is `10 + 4 + 3 = 17`.

---

### 🔹 Basic Solution

The key to solving this problem is to always pick the maximum element in `nums` and apply the operation. Since this problem involves repeatedly finding the maximum value, sorting the array or using a data structure that allows fast maximum retrieval is essential.

#### Approach:
- Sort the array and then repeatedly select the largest element.
- After selecting the largest element, replace it with its ceiling value after division by 3.

#### Code:
```python
import math

def maxScore(nums, k):
    nums.sort(reverse=True)
    score = 0
    for i in range(k):
        score += nums[0]
        nums[0] = math.ceil(nums[0] / 3)
        nums.sort(reverse=True)
    return score
```

#### Time Complexity:
- Sorting the array in each iteration results in a time complexity of **O(k * n log n)**, where `n` is the length of the array.

This solution is not efficient for large inputs. Let’s explore a more optimized approach. ⚡

---

### 🔹 Optimized Solution

We can optimize our solution using a **max-heap (priority queue)** to efficiently retrieve the maximum element in `O(log n)` time, instead of sorting in every iteration.

#### Approach:
1. Use a max-heap to always extract the maximum element in constant time.
2. After extracting the element, replace it with `ceil(nums[i] / 3)` and reinsert it into the heap.

#### Code:
```python
import heapq
import math

def maxScore(nums, k):
    # Convert to a max-heap by negating all numbers (heapq is a min-heap by default)
    max_heap = [-num for num in nums]
    heapq.heapify(max_heap)
    
    score = 0
    for _ in range(k):
        # Extract the maximum value
        max_val = -heapq.heappop(max_heap)
        score += max_val
        # Push the new value (ceiling of max_val / 3) into the heap
        heapq.heappush(max_heap, -math.ceil(max_val / 3))
    
    return score
```

#### Example Walkthrough:

For `nums = [1, 10, 3, 3, 3]` and `k = 3`:

1. Initial heap: `[-10, -3, -3, -3, -1]` (the max element is `10`)
   - Add `10` to the score, replace `10` with `4` (`ceil(10 / 3)`).
   - Updated heap: `[-4, -3, -3, -1, -3]`, score = `10`.

2. Next, extract `4`, add it to the score, and replace it with `2` (`ceil(4 / 3)`).
   - Updated heap: `[-3, -3, -3, -1, -2]`, score = `14`.

3. Extract `3`, add it to the score, and replace it with `1` (`ceil(3 / 3)`).
   - Updated heap: `[-3, -3, -2, -1, -1]`, score = `17`.

#### Time Complexity:
- Using a max-heap allows us to perform `k` operations in **O(k log n)**, where `n` is the number of elements in `nums`. This is much more efficient than the basic approach for large input sizes.

---

### 🔹 Conclusion

To solve the problem of maximizing the score after applying `k` operations, the optimized solution with a max-heap reduces the time complexity significantly compared to a basic sorting-based approach. Using a heap allows us to efficiently manage and retrieve the largest elements. 🎯
