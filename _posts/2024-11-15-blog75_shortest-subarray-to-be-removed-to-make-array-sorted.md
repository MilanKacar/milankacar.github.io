---
layout: post  
title: "#75 🔢 1574. Shortest Subarray to be Removed to Make Array Sorted 🧠🚀"  
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Two Pointers, Binary Search, Stack, Monotonic Stack]
---

Sorting arrays is a common requirement in computer science and forms the basis for many efficient algorithms. But what if you’re given an array that’s almost sorted? In this problem, we need to remove the **shortest contiguous subarray** to transform the remaining elements into a sorted (non-decreasing) sequence. 

This problem balances logical thinking and computational efficiency. By leveraging properties of sorted subarrays and efficient merging techniques, we can achieve optimal results. Let’s dive deep into the solution while exploring the nuances of this problem! 🔍✨

---

### Problem Statement 📜

> **Given** an integer array `arr`, **remove** a subarray (it can be empty) so that the remaining elements in `arr` form a non-decreasing sequence.
>
> **Return** the length of the shortest subarray to remove.

---

### 🖼️ Intuition and Approach

To solve this problem effectively, the key insight is:
> After removing the subarray, the result will consist of two parts: a **prefix** and a **suffix**, where the prefix ends before the subarray, and the suffix starts after it.

Here’s how we break down the solution:

1. **Find the Longest Sorted Prefix and Suffix**:
   - The **prefix** is the longest subarray from the start that is sorted.
   - The **suffix** is the longest subarray from the end that is sorted.

2. **Try to Merge the Prefix and Suffix**:
   - The middle subarray between the prefix and suffix will be removed.
   - Check all possible boundaries for merging the prefix and suffix to minimize the length of the removed subarray.

3. **Calculate the Minimum Removal Length**:
   - Test all scenarios and calculate the smallest length of the subarray to remove.

---

### 🧮 Algorithm Walkthrough with Detailed Example

Let’s work through the problem step by step with **Example 1:**

#### Example Input:

```plaintext
arr = [1, 2, 3, 10, 4, 2, 3, 5]
```

#### Step 1: Find the Longest Sorted Prefix
Starting from the first element, find the longest subarray that is non-decreasing:

- Compare consecutive elements:
  - $$1 \leq 2$$ ✅
  - $$2 \leq 3$$ ✅
  - $$3 \leq 10$$ ✅
  - $$10 \nleq 4$$ ❌

The prefix is `[1, 2, 3, 10]`, which ends at index **3**.

#### Step 2: Find the Longest Sorted Suffix
Starting from the last element, find the longest subarray that is non-decreasing:

- Compare consecutive elements from the end:
  - $$3 \leq 5$$ ✅
  - $$2 \leq 3$$ ✅
  - $$4 \nleq 2$$ ❌

The suffix is `[3, 5]`, which starts at index **6**.

#### Step 3: Test Middle Subarrays
Now, we test various scenarios for merging the prefix and suffix:

- Remove everything between the prefix and suffix:
  - This removes `[10, 4, 2]`.
  - Length of removed subarray = **3**.

- Attempt to merge the prefix with the suffix at various points:
  - Prefix ends at index **3**; suffix starts at index **6**:
    - Test if `arr[3] (10)` can merge with `arr[6] (3)`. ❌
    - Advance pointers to find a valid connection:
      - Try `arr[2] (3)` with `arr[6] (3)`. ✅
      - Removal length = $$6 - 2 - 1 = 3$$.

#### Result:
The minimum subarray length to remove is **3**, corresponding to removing `[10, 4, 2]`.

---

### 🔎 Edge Cases to Consider

1. **Already Sorted Array**:
   ```plaintext
   Input: [1, 2, 3]
   Output: 0
   Explanation: The array is already non-decreasing; no removal is needed.
   ```

2. **Strictly Decreasing Array**:
   ```plaintext
   Input: [5, 4, 3, 2, 1]
   Output: 4
   Explanation: Only one element can remain in this scenario, so the rest must be removed.
   ```

3. **Single Element Array**:
   ```plaintext
   Input: [10]
   Output: 0
   Explanation: A single element is trivially sorted; no removal is needed.
   ```

4. **Alternating Highs and Lows**:
   ```plaintext
   Input: [1, 10, 2, 9, 3, 8]
   Output: Varies based on merging strategy.
   ```

---

### 🧠 Optimized Solution with Explanation

The optimized approach uses a two-pointer technique to merge the prefix and suffix efficiently.

#### Python Code:

```python
def shortestSubarrayToRemove(arr):
    n = len(arr)
    
    # Step 1: Find the longest sorted prefix
    left = 0
    while left < n - 1 and arr[left] <= arr[left + 1]:
        left += 1
    
    # If the whole array is sorted, no removal needed
    if left == n - 1:
        return 0
    
    # Step 2: Find the longest sorted suffix
    right = n - 1
    while right > 0 and arr[right - 1] <= arr[right]:
        right -= 1
    
    # Step 3: Initialize result as the minimal removal
    min_len_to_remove = min(n - left - 1, right)
    
    # Step 4: Try to merge prefix and suffix
    i, j = 0, right
    while i <= left and j < n:
        if arr[i] <= arr[j]:
            min_len_to_remove = min(min_len_to_remove, j - i - 1)
            i += 1
        else:
            j += 1
    
    return min_len_to_remove
```

#### Complexity Analysis:
- **Time Complexity**: $$O(n)$$, where $$n$$ is the length of the array.
- **Space Complexity**: $$O(1)$$.

---

### 🚀 Enhanced Walkthrough with Large Numbers

Let’s test the solution with larger numbers:

#### Input:
```plaintext
arr = [1, 3, 7, 9, 8, 6, 10, 12, 15]
```

1. **Prefix**: `[1, 3, 7, 9]` (ends at index **3**).
2. **Suffix**: `[10, 12, 15]` (starts at index **6**).
3. **Middle**: `[8, 6]` must be removed.

By checking merges and boundaries:
- Remove `[8, 6]`: Removal length = **2**.

#### Output:
```plaintext
Result: 2
```

---

### 📝 Conclusion

This problem showcases the power of efficient two-pointer techniques to solve array manipulation tasks. By splitting the array into a prefix and suffix and testing merges, we ensure minimal removal with $$O(n)$$ time complexity. Through careful walkthroughs and edge cases, you can master this type of problem confidently. Happy coding! 🚀
