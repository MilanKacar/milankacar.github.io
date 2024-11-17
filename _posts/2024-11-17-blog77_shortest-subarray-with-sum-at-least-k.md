---
layout: post  
title: "#77 🔢📏 862. Shortest Subarray with Sum at Least K 🧠🚀"  
categories: [LeetCode, Programming]  
---

Finding the shortest subarray with a sum of at least $$ k $$ is a tricky but highly rewarding challenge. This problem merges concepts like prefix sums, sliding windows, and deques, making it an absolute gem for algorithm enthusiasts! Let’s solve it step by step while keeping it fun and engaging with loads of examples and insights! 🌟

---

### Problem Statement 📜

You are given:
- An integer array `nums`
- An integer `k`

Your task is to return the **length of the shortest non-empty subarray** of `nums` whose sum is **at least $$ k $$**.  
If no such subarray exists, return `-1`.  

A **subarray** is a contiguous part of an array.

---

### Examples 📊

#### Example 1:
**Input**:  
`nums = [1]`, `k = 1`  
**Output**:  
`1`  
**Explanation**:  
The single element `[1]` meets the condition $$ \geq k $$.

#### Example 2:
**Input**:  
`nums = [1, 2]`, `k = 4`  
**Output**:  
`-1`  
**Explanation**:  
No subarray has a sum $$ \geq 4 $$.

#### Example 3:
**Input**:  
`nums = [2, -1, 2]`, `k = 3`  
**Output**:  
`3`  
**Explanation**:  
The entire array `[2, -1, 2]` has a sum of $$ 3 $$, which satisfies the condition. It is the shortest subarray possible.

---

### Constraints 🔐

- $$ 1 \leq \text{nums.length} \leq 10^5 $$
- $$ -10^5 \leq \text{nums[i]} \leq 10^5 $$
- $$ 1 \leq k \leq 10^9 $$

---

### 🧩 Edge Cases to Consider:
1. **Array with one element**:
   - If the element is $$ \geq k $$, return 1. Otherwise, return -1.
2. **All elements negative**:
   - No subarray can satisfy $$ \geq k $$. Return -1.
3. **All elements positive**:
   - The shortest subarray with a sum $$ \geq k $$ will be valid.
4. **Array contains zeros**:
   - Zeros contribute nothing to the sum but impact subarray lengths.
5. **Large $$ k $$**:
   - If $$ k $$ is larger than the sum of all elements, return -1.

---

### Brute-Force Solution 🛠️

The brute-force approach is simple to understand:
1. Try every possible subarray.
2. Calculate its sum.
3. Check if it satisfies the condition $$ \geq k $$.

---

#### Code:
```python
def shortestSubarray_bruteforce(nums, k):
    n = len(nums)
    min_length = float('inf')
    
    for start in range(n):
        current_sum = 0
        for end in range(start, n):
            current_sum += nums[end]
            if current_sum >= k:
                min_length = min(min_length, end - start + 1)
                break

    return -1 if min_length == float('inf') else min_length
```

---

#### Time Complexity:
- **Outer Loop**: Runs $$ n $$ times.
- **Inner Loop**: Runs up to $$ n $$ times for each iteration.
- **Total**: $$ O(n^2) $$. Too slow for large inputs. 🚨

#### Space Complexity:
- **Memory**: $$ O(1) $$.

---

### Optimized Solution 💡

The brute-force solution is too slow for large arrays. We need an efficient approach that combines **prefix sums** and a **deque**.

#### Key Insights:
1. **Prefix Sums**:
   - Calculate cumulative sums of the array.
   - $$ \text{prefix\_sums}[i] $$ gives the sum of the first $$ i $$ elements.
   - A subarray sum $$ \geq k $$ can be expressed as:  
     $$ \text{prefix\_sums}[j] - \text{prefix\_sums}[i] \geq k $$.

2. **Using a Deque**:
   - Use a **monotonic deque** to efficiently find the shortest subarray.
   - Remove elements from the deque if they are no longer useful.

---

#### Algorithm:
1. Compute prefix sums.
2. Maintain a deque of indices of useful prefix sums.
3. Iterate through prefix sums and:
   - Update the shortest subarray length when possible.
   - Remove unhelpful indices from the deque.
4. Return the result.

---

#### Code:
```python
from collections import deque
from itertools import accumulate

class Solution:
    def shortestSubarray(self, nums, k):
        # Compute prefix sums
        prefix_sums = list(accumulate(nums, initial=0))
        deque_indices = deque()
        min_length = float("inf")

        for current_index, current_sum in enumerate(prefix_sums):
            # Check if deque front provides a valid subarray
            while deque_indices and current_sum - prefix_sums[deque_indices[0]] >= k:
                min_length = min(min_length, current_index - deque_indices.popleft())
            
            # Remove indices from deque back if not useful
            while deque_indices and prefix_sums[deque_indices[-1]] >= current_sum:
                deque_indices.pop()

            # Add current index to deque
            deque_indices.append(current_index)
        
        return -1 if min_length == float("inf") else min_length
```

---

#### Time Complexity:
- **Prefix Sums**: $$ O(n) $$
- **Deque Operations**: Each index is added/removed once $$ O(n) $$.
- **Total**: $$ O(n) $$. 🎉

#### Space Complexity:
- $$ O(n) $$ for prefix sums and deque.

---

### Walkthrough Example 🔍

#### Input:
`nums = [2, -1, 2], k = 3`

#### Steps:
1. **Prefix Sums**:  
   `prefix_sums = [0, 2, 1, 3]`

2. **Iteration**:
   - $$ i = 0 $$: Add index $$ 0 $$ to deque.
   - $$ i = 1 $$: Add index $$ 1 $$ to deque.
   - $$ i = 2 $$: Remove index $$ 1 $$ (no longer useful). Add $$ 2 $$.
   - $$ i = 3 $$: Subarray $$[2, -1, 2]$$ satisfies condition. Update `min_length = 3`.

#### Output:
`3`

---

### Edge Case Walkthrough 🌟

#### Input:
`nums = [-5, -4, -3], k = 1`

**Explanation**:
- All elements are negative. No subarray can satisfy $$ \geq k $$.  

**Output**: `-1`

---

### Conclusion ✨

This problem showcases how combining **prefix sums** with a **deque** can optimize sliding-window-like challenges.  
- **Brute-Force Approach**: Simple but inefficient ($$ O(n^2) $$).
- **Optimized Approach**: Elegant and efficient ($$ O(n) $$).

Mastering this technique will empower you to tackle similar hard-level problems in arrays. Happy coding! 😊
