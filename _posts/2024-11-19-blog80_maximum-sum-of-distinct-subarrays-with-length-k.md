---
layout: post  
title: "#80 📏🧮 2461. Maximum Sum of Distinct Subarrays With Length K 🧠🚀"  
categories: [LeetCode, Programming]  
---

When we think about finding **maximum subarray sums**, things can get tricky when there's a twist: **all elements in the subarray must be distinct**! 🤯 This problem brings an exciting challenge, combining sliding window techniques and a hint of hash magic 🪄 to create a solution that's efficient and clean.

---

### 💡 Problem Statement

You are given an integer array `nums` and an integer `k`. Find the **maximum subarray sum** of all the subarrays of `nums` that meet the following conditions:

1. The length of the subarray is `k`, and  
2. **All elements of the subarray are distinct**.

If no subarray meets the conditions, return `0`.

#### ✍️ Definitions

- A **subarray** is a contiguous, non-empty sequence of elements within an array.  
- A subarray must have exactly `k` elements to be considered valid.  
- If any element in a subarray repeats, the subarray is invalid.

---

### 🌟 Examples  

#### Example 1  

```plaintext
Input: nums = [1, 5, 4, 2, 9, 9, 9], k = 3
Output: 15
```

**Explanation:**  
The subarrays of `nums` with length `k` are:  
- `[1, 5, 4]` → Valid, sum = `10`  
- `[5, 4, 2]` → Valid, sum = `11`  
- `[4, 2, 9]` → Valid, sum = `15` (maximum sum 🎉)  
- `[2, 9, 9]` → Invalid (element `9` repeats)  
- `[9, 9, 9]` → Invalid (element `9` repeats)  

**Output:** `15`

---

#### Example 2  

```plaintext
Input: nums = [4, 4, 4], k = 3
Output: 0
```

**Explanation:**  
The only subarray of length `k` is `[4, 4, 4]`, which is invalid because element `4` repeats.  

**Output:** `0`

---

### 🔎 Constraints  

- `1 <= k <= nums.length <= 10⁵`  
- `1 <= nums[i] <= 10⁵`  

---

### 🚶 Step-by-Step Plan  

This problem is best solved using the **sliding window** technique with a **hash set** or **hash map** to efficiently track the distinct elements in the current window.

---

## 🛠️ Solution with Python (Optimized Single Solution)  

Since both the base and optimized solutions share the same time complexity, we’ll focus on the **optimized sliding window approach**.

### 📝 Algorithm  

1. **Initialize a sliding window** with size `k` and maintain:  
   - A `current_sum` for the current window’s sum.  
   - A `max_sum` to store the maximum sum of all valid windows.  
   - A `frequency_map` (or `set`) to ensure all elements in the window are distinct.  

2. Slide the window across the array:  
   - Add the new element at the end of the window to the `current_sum`.  
   - Remove the element falling out of the window (if any) from the `current_sum`.  
   - Use the `frequency_map` to check if all elements in the window are distinct.  

3. If a window is valid (all elements distinct), update the `max_sum`.  

4. Return `max_sum` if any valid window exists, else return `0`.

---

### 🐍 Python Implementation  

```python
def maximumSubarraySum(nums, k):
    from collections import defaultdict

    # Sliding window variables
    max_sum = 0
    current_sum = 0
    frequency_map = defaultdict(int)

    left = 0  # Left pointer of the sliding window

    for right in range(len(nums)):
        # Add the right element into the window
        frequency_map[nums[right]] += 1
        current_sum += nums[right]

        # If the window size exceeds k, shrink from the left
        if right - left + 1 > k:
            frequency_map[nums[left]] -= 1
            if frequency_map[nums[left]] == 0:
                del frequency_map[nums[left]]
            current_sum -= nums[left]
            left += 1

        # Check if the current window is valid (distinct elements only)
        if right - left + 1 == k and len(frequency_map) == k:
            max_sum = max(max_sum, current_sum)

    return max_sum
```

---

### ⏱️ Time Complexity  

- **Sliding Window Operations:** Each element is added/removed from the `frequency_map` exactly once.  
  Complexity = \(O(n)\), where \(n\) is the length of `nums`.  
- **Overall:** \(O(n)\).

### 🧵 Space Complexity  

- **Space for the `frequency_map`:** At most \(O(k)\), where \(k\) is the window size.  
- **Overall:** \(O(k)\).



---

### 🔍 Walkthrough of Example  

#### Input: `nums = [1, 5, 4, 2, 9, 9, 9], k = 3`

1. **Initial State:**  
   - `max_sum = 0`  
   - `current_sum = 0`  
   - `frequency_map = {}`  

2. **Sliding the Window:**  

   - **Window `[1, 5, 4]`:**  
     - Add `1, 5, 4` → Valid → `current_sum = 10` → `max_sum = 10`.  

   - **Window `[5, 4, 2]`:**  
     - Add `2`, remove `1` → Valid → `current_sum = 11` → `max_sum = 11`.  

   - **Window `[4, 2, 9]`:**  
     - Add `9`, remove `5` → Valid → `current_sum = 15` → `max_sum = 15`.  

   - **Window `[2, 9, 9]`:**  
     - Add `9`, remove `4` → Invalid (duplicate `9`).  

   - **Window `[9, 9, 9]`:**  
     - All elements invalid.  

**Output:** `15`

---

### ⚙️ Edge Cases  

1. **Single Element Array:**  
   ```plaintext
   nums = [7], k = 1
   Output: 7
   ```

2. **All Elements Identical:**  
   ```plaintext
   nums = [5, 5, 5], k = 2
   Output: 0
   ```

3. **Subarray Length Larger Than Array Length:**  
   ```plaintext
   nums = [1, 2], k = 3
   Output: 0
   ```

4. **Large Array with All Unique Elements:**  
   ```plaintext
   nums = [1, 2, 3, 4, ..., 10000], k = 5
   Output: Sum of max 5 unique consecutive elements
   ```

---

### 🏁 Conclusion  

This problem beautifully demonstrates the power of the **sliding window technique**! 🪟 By efficiently tracking distinct elements, we solved the problem in \(O(n)\) time complexity, handling edge cases gracefully.  

Happy Coding! 🚀
