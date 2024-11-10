---
layout: post  
title: "#69 0️⃣0️⃣1️⃣0️⃣ 3133. Minimum Array End 🧠🚀"  
categories: [LeetCode, Programming]  
---

Today, we’ll dive into an interesting problem where we have to construct a **strictly increasing array** with a given length `n`, ensuring that a bitwise `AND` operation across all elements results in a specific number `x`. Our goal? To keep the last element of this array—the largest one—as **small as possible**! 🚀 Let’s break down the problem, understand the logic, and see how Python code helps us solve it efficiently! 🐍💡

---

### 📝 Problem Statement 📝

You are given:
- **Two integers**, `n` and `x`.

Your task:
- Construct an array, `nums`, of positive integers with **size `n`** where:
    - The array is strictly increasing: `nums[i + 1] > nums[i]` for `0 <= i < n - 1`.
    - The **bitwise AND** of all elements in `nums` is equal to `x`.

**Objective:** Return the **minimum possible value of `nums[n - 1]`**, the last element of the array.

---

### 🔍 Examples 🔍

Let’s explore a few examples to see the problem in action!

#### Example 1

- **Input:** `n = 3`, `x = 4`
- **Output:** `6`
- **Explanation:** One possible array is `[4, 5, 6]` and the last element is `6`. The array `[4, 5, 6]` satisfies:
  - **Strictly increasing**: Yes, `4 < 5 < 6`.
  - **Bitwise AND equals `x`**: `4 & 5 & 6 = 4`.

#### Example 2

- **Input:** `n = 2`, `x = 7`
- **Output:** `15`
- **Explanation:** An array `[7, 15]` satisfies the conditions, with the last element as `15`:
  - **Strictly increasing**: Yes, `7 < 15`.
  - **Bitwise AND equals `x`**: `7 & 15 = 7`.

---

### 🚀 Solution Overview 🚀

To tackle this problem, we need to understand:
1. **Bitwise AND** and how it restricts our choices.
2. How to keep the last element of `nums` **as small as possible** while respecting the `AND` requirement.
3. The key insight: We can construct each element of the array by **merging** `x` with different numbers, ensuring the AND operation holds.

### 💡 Key Insights 💡

1. **Bitwise AND Behavior**: For a bitwise AND to yield `x`, the array elements must have the **same bits set** as in `x`. However, extra bits can be added without impacting the AND result.
2. **Constructing Elements**: To ensure `nums[i + 1] > nums[i]` and retain the AND behavior, each `nums[i]` can be seen as a **bitwise merge** of `x` with another integer. Each additional bit allows us to create strictly increasing elements.
3. **Iterative Bit Masking**: By manipulating bits from lower to higher positions, we can achieve the **smallest last element**.

---

### 🧑‍💻 Code Explanation 🧑‍💻

Here’s the Python code for the optimized solution:

```python
class Solution:
    def minEnd(self, n: int, x: int) -> int:
        n -= 1  # Use (n-1) to create a range of numbers
        ans = x  # Start with x as the base answer
        for i in range(31):  # Loop over 31 bits (assuming we work with 32-bit integers)
            if x >> i & 1 == 0:  # Check if bit `i` in x is not set
                ans |= (n & 1) << i  # Set the bit `i` in `ans` based on `n`
                n >>= 1  # Move `n` to the right to handle the next bit
        ans |= n << 31  # Account for any remaining bits in `n` shifted to the 31st position
        return ans
```

---

### 📊 Time Complexity Analysis 📊

The code has a **time complexity of O(1)**. Since the solution involves bitwise operations over a fixed number of bits (31 in this case), the time complexity does not grow with the input size. Instead, it remains constant.

---

### 🕵️ Walkthrough with a Higher Number Example 🕵️

Let’s break down the solution step-by-step with `n = 4` and `x = 9`. Here’s how each part of the process unfolds:

1. **Initialize Variables**:
   - `n -= 1` results in `n = 3` (which allows indexing for a range of 4 elements).
   - Set `ans = x = 9`.

2. **Bitwise Merging**:
   - We loop over each bit from 0 to 31.
   - For each bit, if it’s not set in `x`, we set it in `ans` according to bits in `n`.
   - Example progression with `x = 9`:
     - Initial `x` in binary: `0000...1001`
     - Set bits of `ans` based on lower bits of `n` until all are shifted.

3. **Final Adjustment**:
   - After the loop, any leftover bits in `n` are merged by shifting to the 31st position.

After running through the solution, you’ll get `ans` as the minimum possible last element. 🎉

---

### 📌 Edge Cases to Consider 📌

1. **Small Values of `n`**: When `n = 1`, `nums` contains only `x`.
2. **Small Values of `x`**: When `x = 1`, the smallest bit is set, limiting our choices for merging.
3. **High Bit Positions**: The solution correctly handles bit positions up to 31, respecting 32-bit integer constraints.

---

### 🔍 Conclusion 🔍

Through this problem, we learned the power of **bitwise operations** in creating optimized solutions. Using bitwise manipulation allowed us to minimize the last element while ensuring the AND result. This approach provides an efficient solution, meeting both the **strictly increasing** and **bitwise constraints** with minimal code!
