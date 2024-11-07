---
layout: post
title: "#67 0️⃣1️⃣ 2275. Largest Combination With Bitwise AND Greater Than Zero 🧠🚀"
categories: [LeetCode, Programming]
---

In this problem, we’re exploring bitwise operations, particularly the **bitwise AND** operation. Our goal is to find the **largest subset** of numbers from a list such that the **bitwise AND** of all numbers in the subset is **greater than zero**. 🔥

### 📜 Problem Statement

We are given an array of positive integers, `candidates`. Our task is to evaluate every possible combination of these integers and determine the **largest subset** for which the **bitwise AND** is greater than 0. Each number in `candidates` can only be used once per combination.

Let’s break down the requirements and the bitwise operations involved! 🚀

### 🧩 Example Walkthrough

#### Example 1
**Input**: `candidates = [16, 17, 71, 62, 12, 24, 14]`  
**Output**: `4`  
**Explanation**: The largest subset with a bitwise AND > 0 could be `[16, 17, 62, 24]` where the bitwise AND calculation yields a positive result. No larger combination exists that maintains this property.

#### Example 2
**Input**: `candidates = [8, 8]`  
**Output**: `2`  
**Explanation**: Here, both values are the same, and combining them results in a bitwise AND of `8`, which is positive. Therefore, the largest subset with a bitwise AND > 0 has size `2`.

---

### 🧠 Understanding the Approach and Edge Cases

#### Edge Cases to Consider 🛠️
1. **Single Element**: When `candidates` has only one element (e.g., `[5]`). Here, the answer is `1` because the element itself is the only valid subset.
2. **All Elements Are Power of Two**: Special case where numbers like `[1, 2, 4, 8]` are considered. Each only has one bit set to `1`, so the bitwise AND of any combination of them would be `0` unless repeated.
3. **High Number of Duplicates**: If all numbers are the same, such as `[8, 8, 8, 8]`, the answer is simply the length of the array because the bitwise AND will always yield a positive result.
4. **Very Large Array with Small Values**: If there’s a large number of small values, such as `[1, 1, 1, ...]` (up to max length), we should ensure our solution efficiently handles this without redundant calculations.

---

### 🚀 Solution Overview

The **bitwise AND** operation only results in a non-zero value if all numbers in the subset have **at least one common bit** set to `1`. Thus, for every bit position (up to 24 bits since `1 <= candidates[i] <= 10^7`), we can:

1. **Count how many numbers** in `candidates` have a `1` at each bit position.
2. For each bit position, the count of numbers with `1` at that position represents the size of the largest subset with a positive bitwise AND result when restricted to that bit.

#### Why This Works:
If a bit position has many numbers with `1`, these numbers can be combined, ensuring the **bitwise AND** result at that position remains **greater than zero**.

#### Steps:

1. **Iterate through each bit position** (up to 24 bits).
2. Count how many numbers have `1` in each position.
3. Track the **maximum count** across all bit positions, as this count will represent the size of the largest subset with a positive AND result.

### 💡 Optimized Solution

This optimized approach leverages bitwise operations efficiently by focusing only on the bits that matter rather than generating all combinations.

#### Python Code Implementation

Here’s how this solution would look in Python:

```python
def largestCombination(candidates):
    max_size = 0
    
    # Check each bit position up to 24 bits (sufficient for numbers up to 10^7)
    for bit in range(24):
        count = 0
        # Count how many numbers have '1' at the current bit position
        for num in candidates:
            if num & (1 << bit):
                count += 1
        # Update max_size if the current count is greater
        max_size = max(max_size, count)
    
    return max_size
```

### 🕹️ Explanation of the Code

1. **Bit Iteration**: We loop through each of the 24 possible bit positions, as a 24-bit integer can represent any number up to the problem's limit.
2. **Counting**: For each position, we count how many numbers in `candidates` have `1` at the current position using `num & (1 << bit)`.
3. **Updating Maximum**: The variable `max_size` keeps track of the largest subset size with a bitwise AND > 0.

### ⏱️ Time Complexity Analysis

- **Time Complexity**: `O(24 * n)`, where `n` is the length of `candidates`. This is efficient, given that `24` is a constant and `n` can be very large.
- **Space Complexity**: `O(1)`, as we use only a fixed number of additional variables.

---

### 🖼️ Example Walkthrough: Detailed Calculation with Larger Numbers

Let’s go through Example 1 with a detailed bit-level walkthrough to reinforce understanding.

**Example**: `candidates = [16, 17, 71, 62, 12, 24, 14]`

1. **Bit Representation**:
   - `16` -> `00010000`
   - `17` -> `00010001`
   - `71` -> `01000111`
   - `62` -> `00111110`
   - `12` -> `00001100`
   - `24` -> `00011000`
   - `14` -> `00001110`

2. **Counting `1`s at Each Bit Position**:
   - **Bit position 0**: `17, 71, 62, 14` → **4 numbers** have `1` here.
   - **Bit position 4**: `16, 17, 62, 24` → **4 numbers** have `1` here.
   - ...

3. **Result**: The largest count across all bit positions is `4`, so the output is `4`.

---

### 🌟 Edge Case Walkthroughs

1. **Single Element**:  
   - **Input**: `[1]`
   - **Output**: `1`
   - **Explanation**: Since there’s only one element, it forms the only valid subset.

2. **Duplicate Values**:  
   - **Input**: `[4, 4, 4, 4]`
   - **Output**: `4`
   - **Explanation**: Here, all values are identical, so we can combine all four to get a subset size of `4`.

3. **Mixed Large and Small Values**:  
   - **Input**: `[1, 2, 4, 8, 16]`
   - **Output**: `1`
   - **Explanation**: Each value has only one unique bit set to `1`, so no combination will have a positive bitwise AND result.

---

### 📊 Conclusion

In this problem, we explored how to use **bitwise operations** to solve a combinatorial problem efficiently! Instead of generating combinations, we focused on bitwise positions and identified the largest subset for each position that ensures a positive bitwise AND result. 🥳

This approach highlights how efficient thinking can transform complex problems into manageable solutions by focusing on **what actually matters** for a successful result—in this case, individual bit positions. 💪
