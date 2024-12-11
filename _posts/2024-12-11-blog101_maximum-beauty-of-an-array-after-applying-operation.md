---
layout: post  
title: "#101 😍 2779. Maximum Beauty of an Array After Applying Operation 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Binary Search, Sliding Window, Sorting]
---

Have you ever wondered how to make an array as beautiful as possible by adjusting its values? 😍 This problem is all about maximizing the beauty of an array by tweaking its elements. Let's dive into an intriguing challenge that combines sorting and sliding window techniques for a highly efficient solution! ✨

---

### **Problem Statement**

You are given a **0-indexed array** `nums` and a non-negative integer `k`.

In one operation, you can:

1. Choose an index `i` (not chosen before) from the range `[0, nums.length - 1]`.
2. Replace `nums[i]` with any integer from the range `[nums[i] - k, nums[i] + k]`.

The **beauty** of the array is defined as the length of the **longest subsequence** consisting of equal elements.

Return the **maximum possible beauty** of the array `nums` after applying the operation any number of times.

**Note**: You can apply the operation to each index only **once**.

---

### **Example 1**

#### **Input:**
```python
nums = [4, 6, 1, 2]
k = 2
```

#### **Output:**
```python
3
```

#### **Explanation:**
1. Replace the element at index 1 with `4` (within the range [4, 8]), resulting in `nums = [4, 4, 1, 2]`.
2. Replace the element at index 3 with `4` (within the range [0, 4]), resulting in `nums = [4, 4, 1, 4]`.
3. The beauty of the array is now `3`, corresponding to the subsequence `[4, 4, 4]`.

---

### **Example 2**

#### **Input:**
```python
nums = [1, 1, 1, 1]
k = 10
```

#### **Output:**
```python
4
```

#### **Explanation:**
No operations are needed because all elements are already equal. The beauty is `4` (the entire array).

---

### **Constraints:**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i], k <= 10^5`

---

Let me elaborate further on the solution approach for this problem. We'll break it down into more detailed steps and address nuances in the logic, along with intuitive reasoning behind each decision.

---

### **Solution Approach (In Detail)**

#### 1️⃣ Understanding the Problem:
The key challenge is to transform the array `nums` by adjusting its elements to maximize the length of a subsequence where all elements are equal. Given that we can modify each element within a range `[nums[i] - k, nums[i] + k]`, the task boils down to finding overlapping ranges across elements that can align to a single value.

---

#### 2️⃣ Breaking Down the Solution:
The problem can be thought of in terms of two key actions:
- **Adjusting the Range:** For each element in `nums`, determine the possible range of values it can take after modification.
- **Finding Overlap:** Identify the maximum number of elements whose adjusted ranges overlap. This ensures they can all be aligned to a single value.

To achieve this efficiently:
1. **Sort the Array:** Sorting ensures that the values are in increasing order, simplifying the process of finding overlapping ranges.
2. **Use a Sliding Window:** The sliding window technique dynamically tracks the current range of valid values. It efficiently determines how many elements can belong to the same subsequence.

---

#### 3️⃣ Algorithm Explanation:
Here’s a step-by-step breakdown of the algorithm:

1. **Sort the Array:**
   Sorting ensures that smaller values come first. This allows us to maintain a continuous range `[nums[l], nums[r]]` for the sliding window.

2. **Initialize the Sliding Window:**
   - Start with `l = 0`, representing the left boundary of the current valid range.
   - Use `r` to traverse through the sorted array, representing the right boundary of the window.

3. **Sliding Window Logic:**
   - For each element `nums[r]`, check whether the current range `[nums[l], nums[r]]` is valid, i.e., `nums[r] - nums[l] ≤ 2 * k`.
   - If the range is valid, expand the window by moving `r` to the next element.
   - If the range is invalid (i.e., `nums[r] - nums[l] > 2 * k`), shrink the window by incrementing `l`. This ensures that the range remains valid.

4. **Calculate Maximum Beauty:**
   - At each step, the size of the current window (`r - l + 1`) represents the length of the longest subsequence where all elements can be aligned.
   - Keep track of the maximum window size during traversal.

5. **Return the Result:**
   After completing the traversal, the maximum window size is the maximum beauty of the array.

---

#### 4️⃣ Why Sliding Window Works:
- The sliding window is optimal because it avoids recalculating the entire range for every element. Instead, it adjusts the range dynamically by moving `l` and `r`.
- Sorting ensures that any invalid range caused by `nums[r]` exceeding `nums[l] + 2 * k` can only be resolved by moving `l` forward, making the approach efficient and logical.

---

#### Edge Cases to Consider:
1. **Single Element Array:**
   - Example: `nums = [5], k = 3`
   - Output: `1` (A single element forms a subsequence of size 1.)

2. **All Elements Already Equal:**
   - Example: `nums = [7, 7, 7], k = 0`
   - Output: `3` (No operations are required.)

3. **No Overlapping Ranges:**
   - Example: `nums = [1, 100], k = 0`
   - Output: `1` (Elements are too far apart to align.)

4. **Large Input Sizes:**
   - Ensure the algorithm handles large arrays (`nums.length = 10^5`) efficiently.

---

### **Visualizing the Sliding Window**

Let’s walk through an example step by step with larger numbers:

#### Input:
```python
nums = [10, 15, 20, 25, 30]
k = 5
```

#### Execution:

1. **Sort `nums`:**
   ```python
   nums = [10, 15, 20, 25, 30]
   ```

2. **Initialize Variables:**
   ```python
   d = 2 * k = 10
   l = 0
   ```

3. **Traverse Using Sliding Window:**
   - Start with `l = 0` and iterate over each element:
   
   **Step 1:** `r = 0, n = 10`:
   ```python
   nums[l] + d = 10 + 10 = 20
   n = 10
   Valid range: [10, 10].
   ```

   **Step 2:** `r = 1, n = 15`:
   ```python
   nums[l] + d = 10 + 10 = 20
   n = 15
   Valid range: [10, 15].
   ```

   **Step 3:** `r = 2, n = 20`:
   ```python
   nums[l] + d = 10 + 10 = 20
   n = 20
   Valid range: [10, 20].
   ```

   **Step 4:** `r = 3, n = 25`:
   ```python
   nums[l] + d = 10 + 10 = 20
   n = 25
   nums[l] is too small; move `l` to 1.
   Valid range: [15, 25].
   ```

   **Step 5:** `r = 4, n = 30`:
   ```python
   nums[l] + d = 15 + 10 = 25
   n = 30
   nums[l] is too small; move `l` to 2.
   Valid range: [20, 30].
   ```

4. **Calculate Result:**
   ```python
   Maximum window size: len(nums) - l = 5 - 2 = 3
   ```

#### Output:
```python
3
```

---

By carefully considering the range overlap and leveraging sorting with a sliding window, this approach efficiently solves the problem within the constraints. It’s elegant, scalable, and intuitive! 😊

#### **Optimal Solution**

We can sort the array and use a **sliding window approach** to find the longest subarray where the difference between the smallest and largest values is less than or equal to `2 * k`.

---

### **Python Code**

```python
from typing import List

class Solution:
    def maximumBeauty(self, nums: List[int], k: int) -> int:
        nums.sort()
        d = 2 * k
        l = 0
        for n in nums:
            if nums[l] + d < n:
                l += 1
        return len(nums) - l
```

---

### **Explanation of the Code**

1. **Sort the Array:**
   Sorting `nums` ensures we can easily find subsequences where the difference between elements is bounded.

2. **Sliding Window Logic:**
   - Maintain a window `[l, r]` where the difference between the smallest (`nums[l]`) and current element (`nums[r]`) is at most `2 * k`.
   - If the difference exceeds `2 * k`, move the left pointer `l` to reduce the window size.

3. **Result Calculation:**
   - The size of the window gives the maximum beauty because all elements in the window can be adjusted to the same value within the range `[nums[i] - k, nums[i] + k]`.

---

### **Time Complexity**

- **Sorting:** `O(n log n)`
- **Sliding Window Traversal:** `O(n)`

Overall time complexity: **`O(n log n)`**.

### **Space Complexity**

The solution uses constant extra space: **`O(1)`**.

---

### **Example Walkthrough**

#### **Input:**
```python
nums = [10, 15, 20, 25, 30]
k = 5
```

#### **Step-by-Step Execution:**

1. **Sort `nums`:**
   ```python
   nums = [10, 15, 20, 25, 30]
   ```

2. **Set `d` to `2 * k`:**
   ```python
    d = 10
   ```

3. **Initialize Sliding Window:**
   - Start with `l = 0`.

4. **Traverse Array with Sliding Window:**

   - For `n = 10`:
     ```python
     nums[l] + d = 10 + 10 = 20
     n = 10
     Window is valid.
     ```
   
   - For `n = 15`:
     ```python
     nums[l] + d = 10 + 10 = 20
     n = 15
     Window is valid.
     ```
   
   - For `n = 20`:
     ```python
     nums[l] + d = 10 + 10 = 20
     n = 20
     Window is valid.
     ```
   
   - For `n = 25`:
     ```python
     nums[l] + d = 10 + 10 = 20
     n = 25
     nums[l] is too small; move `l` to 1.
     ```

   - For `n = 30`:
     ```python
     nums[l] + d = 15 + 10 = 25
     n = 30
     nums[l] is too small; move `l` to 2.
     ```

5. **Final Window Size:**
   ```python
   len(nums) - l = 5 - 2 = 3
   ```

#### **Output:**
```python
3
```

---

### **Edge Cases**

1. **All Elements Equal:**
   - Input: `nums = [7, 7, 7], k = 0`
   - Output: `3` (No changes needed).

2. **Large Range of Values:**
   - Input: `nums = [1, 10^5, 10^5 - 1], k = 1`
   - Output: `1` (Impossible to make elements equal).

3. **Single Element Array:**
   - Input: `nums = [42], k = 10`
   - Output: `1` (Only one element, beauty is 1).

---

### **Conclusion**

This problem elegantly combines sorting and sliding window techniques to achieve optimal performance. By understanding the properties of ranges and focusing on maintaining a valid window, we can efficiently determine the maximum beauty of the array. ✨🚀

Happy coding! 💪😎

