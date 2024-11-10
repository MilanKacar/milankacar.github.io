---
layout: post  
title: "#70 0️⃣0️⃣1️⃣0️⃣ 3097. Shortest Subarray With OR at Least K II 🧠🚀"  
categories: [LeetCode, Programming]  
---

Given an array of non-negative integers, we aim to find the smallest subarray where the bitwise OR of all elements is at least a given integer `k`. This problem involves bit manipulation and sliding window techniques, which are essential for efficient solutions! 

Let’s dive in to understand the problem, explore a basic solution, optimize it, and walk through examples with emojis along the way! 🚀

---

### 📝 Problem Statement

We are given:

1. An array `nums` of non-negative integers.
2. An integer `k`.

A subarray is called **special** if the bitwise OR of all elements in the subarray is at least `k`. We want to find the **length of the shortest special subarray**. If no such subarray exists, return `-1`.

#### Constraints
- $$ 1 \leq \text{nums.length} \leq 200,000 $$
- $$ 0 \leq \text{nums}[i] \leq 10^9 $$
- $$ 0 \leq k \leq 10^9 $$

---

### 🔍 Examples

#### Example 1:
- **Input**: `nums = [1, 2, 3]`, `k = 2`
- **Output**: `1`
- **Explanation**: The subarray `[3]` has a bitwise OR of `3`, which is greater than `2`, so we return `1` as the shortest length.

#### Example 2:
- **Input**: `nums = [2, 1, 8]`, `k = 10`
- **Output**: `3`
- **Explanation**: The subarray `[2, 1, 8]` has a bitwise OR of `11`, meeting the condition with a length of `3`.

#### Example 3:
- **Input**: `nums = [1, 2]`, `k = 0`
- **Output**: `1`
- **Explanation**: The subarray `[1]` has an OR of `1`, which meets the requirement since `1 >= 0`.

---

### 🧠 Solution Approach

To solve this problem, we can use a **sliding window approach** paired with **bitwise operations**.

---

### 🚀 Basic Solution and Intuition

#### Key Observations:
1. **Bitwise OR**: The OR operation between numbers keeps the bits set in both numbers, but also sets any bits that are `1` in either number. This property means that once a bit is set, it will remain set, making the OR value non-decreasing within a window.
2. **Sliding Window**: We can attempt to keep a growing window and shrink it from the left once we meet or exceed the OR requirement. This helps find the minimum subarray length satisfying the condition.

#### Steps:
1. **Expand Window**: Start expanding the window by including elements to the OR operation.
2. **Shrink Window**: As soon as the OR of the window is greater than or equal to `k`, try to shrink it from the left while still meeting the requirement.
3. **Track Minimum Length**: Each time the condition is met, update the minimum length.

---

### ✨ Optimized Solution

The provided solution leverages a **sliding window with bitwise tracking**. We maintain a bit counter to track set bits within the window and modify the OR result dynamically. This allows efficient updates when expanding or shrinking the window.

Here’s the solution code:

```python
def shortest_subarray_with_or_at_least_k(nums, k):
    n = len(nums)
    cnt = [0] * 32  # Track the count of set bits
    ans = n + 1     # Initialize with a large value
    s = i = 0       # OR value and window start pointer
    
    for j, x in enumerate(nums):
        # Include x in the OR result
        s |= x
        # Update count of set bits
        for h in range(32):
            if x >> h & 1:
                cnt[h] += 1
        
        # Shrink the window while OR is at least k
        while s >= k and i <= j:
            ans = min(ans, j - i + 1)
            y = nums[i]
            for h in range(32):
                if y >> h & 1:
                    cnt[h] -= 1
                    if cnt[h] == 0:
                        s ^= 1 << h
            i += 1
    
    return -1 if ans > n else ans
```

---

### ⏱️ Time Complexity

This solution has a time complexity of **O(n)** because each element is processed once for expansion and once for contraction in the sliding window. This efficiency is essential for large inputs!

---

### 🧐 Example Walkthrough (Detailed)

Let’s go through **Example 2** in detail to understand the process step-by-step.

#### Input:
- `nums = [2, 1, 8]`, `k = 10`

#### Step-by-Step Execution:

1. **Initialize**:
   - `s = 0` (current OR)
   - `cnt = [0] * 32` (bit count)
   - `ans = len(nums) + 1 = 4`

2. **First Element (`2`)**:
   - `s |= 2` → `s = 2` (OR of `2` itself)
   - Update `cnt` for the bits of `2` (binary `10`):
     - `cnt[1] = 1`
   - `s < k`, so continue expanding.

3. **Second Element (`1`)**:
   - `s |= 1` → `s = 3`
   - Update `cnt` for the bits of `1` (binary `1`):
     - `cnt[0] = 1`
   - `s < k`, so continue expanding.

4. **Third Element (`8`)**:
   - `s |= 8` → `s = 11` (OR of `2, 1, 8`)
   - Update `cnt` for the bits of `8` (binary `1000`):
     - `cnt[3] = 1`
   - Now, `s >= k`, so we found a special subarray `[2, 1, 8]` with OR `11`.

5. **Shrink Window**:
   - Update `ans = 3` (length of `[2, 1, 8]`)
   - Try removing `2` from OR:
     - Adjust `cnt` for `2`, removing `cnt[1]`
     - `s = 9`, but `s < k` now, so stop shrinking.

---

### 🪖 Edge Cases

1. **No Solution**:
   - If `k` is very large, e.g., `nums = [1, 2, 4]` and `k = 1000`, there may be no subarray where OR reaches `k`. The function should return `-1`.

2. **Single Element**:
   - `nums = [k]`, `k` itself: return `1` since `[k]` meets the condition directly.

3. **All Elements are Zero**:
   - `nums = [0, 0, 0]`, `k = 1`: should return `-1` because OR will always be zero.

---

### 🔚 Conclusion

This problem showcases the importance of bitwise operations and sliding window techniques in handling subarray conditions efficiently. The optimized solution ensures that we meet the problem constraints without excessive time complexity.

By following this approach, you’ll gain valuable insights into how **bitwise operations** and **window techniques** complement each other for powerful problem-solving!

