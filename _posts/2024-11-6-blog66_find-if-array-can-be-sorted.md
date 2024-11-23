---
layout: post  
title: "#66 🔁 3011. Find if Array Can Be Sorted 🧠🚀" 
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Bit Manipulation, Sorting]
---

Welcome back, coders! Today, we’re tackling an exciting array problem that tests our understanding of sorting with constraints. We’ll explore how to determine if an array can be sorted by swapping elements with the **same number of set bits** in their binary representation. Let’s dive in! 🏄‍♂️

---

## 📝 Problem Statement

You're given a 0-indexed array of positive integers, `nums`. In one operation, you can swap any two **adjacent elements** if they share the **same number of set bits**. You may perform this operation **any number of times** (including zero).

Return `True` if the array can be sorted by following these rules; otherwise, return `False`.

---

## Examples

### Example 1
**Input:** `nums = [8,4,2,30,15]`

**Output:** `True`

**Explanation:**
1. The binary representations of each number reveal the number of set bits:
   - `8 (1000)`, `4 (100)`, and `2 (10)` each have **one set bit**.
   - `30 (11110)` and `15 (1111)` each have **four set bits**.
2. We can sort this array with a few swaps:
   - Swap `8` and `4` ➡️ `[4, 8, 2, 30, 15]`
   - Swap `8` and `2` ➡️ `[4, 2, 8, 30, 15]`
   - Swap `4` and `2` ➡️ `[2, 4, 8, 30, 15]`
   - Swap `30` and `15` ➡️ `[2, 4, 8, 15, 30]`

The array is now sorted, so we return `True`.

### Example 2
**Input:** `nums = [1,2,3,4,5]`

**Output:** `True`

**Explanation:** The array is already sorted, so no swaps are needed.

### Example 3
**Input:** `nums = [3,16,8,4,2]`

**Output:** `False`

**Explanation:** It’s impossible to sort this array by swapping adjacent elements with the same number of set bits.

---

## Approach and Solution

This problem requires us to determine if we can rearrange the array to achieve a sorted order by swapping elements with the same number of set bits.

### Step-by-Step Approach 🧭

1. **Segment the Array**: We divide `nums` into segments where each segment contains consecutive elements with the **same number of set bits**.
2. **Sort Within Segments**: Within each segment, we should be able to rearrange the elements freely since they can be swapped as needed.
3. **Check Order of Segments**: Each segment’s maximum element should be less than or equal to the next segment’s minimum element to ensure global sorting.

---

### Solution Code

Here’s the Python solution that implements this approach.

```python
class Solution:
    def canSortArray(self, nums):
        # Initialize previous maximum to negative infinity.
        previous_max = -float('inf')
        # Initialize index and get the length of the list.
        i, n = 0, len(nums)
      
        # Iterate through the list of numbers.
        while i < n:
            # Initialize the next index and get the bit count of the current number.
            j = i + 1
            bit_count = bin(nums[i]).count('1')
            # Keep track of the minimum and maximum for the current bit count.
            current_min = current_max = nums[i]
          
            # Slide through the array to find consecutive numbers with the same bit count.
            while j < n and bin(nums[j]).count('1') == bit_count:
                current_min = min(current_min, nums[j])
                current_max = max(current_max, nums[j])
                j += 1
          
            # If the previous maximum number is greater than the current minimum,
            # the array cannot be sorted based on the conditions, hence return False.
            if previous_max > current_min:
                return False
            # Update previous maximum for the next iteration.
            previous_max = current_max
            # Move to the next segment with a different bit count.
            i = j
      
        # If we've reached this point, the array can be sorted, therefore return True.
        return True
```

---

## Explanation with Example Walkthrough 🔍

Let's walk through Example 1 to solidify our understanding.

**Input:** `nums = [8,4,2,30,15]`

1. **Step 1**: Segment based on set bits.
   - `8 (1000)`, `4 (100)`, and `2 (10)` each have one set bit. Segment `[8, 4, 2]`.
   - `30 (11110)` and `15 (1111)` each have four set bits. Segment `[30, 15]`.

2. **Step 2**: Sort within segments.
   - Segment `[8, 4, 2]` can be rearranged to `[2, 4, 8]` using allowed swaps.
   - Segment `[30, 15]` can be rearranged to `[15, 30]`.

3. **Step 3**: Verify that each segment aligns in sorted order.
   - The largest element of `[2, 4, 8]` (i.e., `8`) is smaller than the smallest element of `[15, 30]` (i.e., `15`), so the array can be fully sorted.

Therefore, the output is `True`.

---

## Edge Cases to Consider 🧐

1. **Already Sorted Array**: If `nums` is already sorted, it should return `True`.
2. **Single Element Array**: Any single-element array is trivially sorted.
3. **Elements with Different Set Bits Only**: If all elements have different numbers of set bits, sorting is straightforward as no internal segment swaps are required.
4. **Arrays with Repeating Set Bit Counts but Cannot Be Sorted**: Such cases will require checking segment boundaries carefully.

---

## Complexity Analysis

Let’s analyze the time complexity of our approach.

- **Time Complexity**: We loop through `nums` to check segments, and within each segment, we calculate min and max. Therefore, the complexity is `O(n)`.
- **Space Complexity**: We use constant extra space, `O(1)`.

---

## Conclusion 🎉

This problem challenges us to think critically about sorting within constraints, especially when binary representation comes into play. We segmented the array based on set bit counts, sorted within those segments, and checked segment order to determine if a full sort is achievable. With a combination of binary tricks and strategic swaps, we crafted a solution that ensures efficiency and correctness.

### ✨ Key Takeaways

- **Binary Representation**: Understanding set bits is key in solving this problem.
- **Segmenting for Order**: Breaking down the array into manageable chunks makes the problem easier to handle.
- **Efficiency Focus**: A linear approach ensures scalability even for larger inputs.

---

This comprehensive approach should cover all aspects of understanding and solving this problem, from explanation to edge cases, in detail. Happy coding! 😄
