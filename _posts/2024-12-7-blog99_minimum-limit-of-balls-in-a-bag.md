---
layout: post  
title: "#99 1760. Minimum Limit of Balls in a Bag 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Binary Search]
---

Let’s dive into an exciting problem from **LeetCode**: *1760. Minimum Limit of Balls in a Bag*. 🚀 We’ll explore the problem, break it into manageable parts, and tackle it step by step. Get ready for some problem-solving action with detailed explanations, walkthroughs, and Python code snippets! 🐍✨

---

## 📖 **Problem Statement**

You are given an array `nums` where each element represents the number of balls in a bag. Additionally, you're provided an integer `maxOperations`, which is the maximum number of operations you can perform. 

**Operations allowed**:
1. Choose a bag with `x` balls.
2. Divide it into two bags, such that each bag has a **positive** number of balls.

Your **goal** is to minimize the maximum number of balls in a bag (penalty) after performing at most `maxOperations`.

---

### **Constraints**:

- `1 <= nums.length <= 10⁵`
- `1 <= nums[i], maxOperations <= 10⁹`

---

## 🌟 **Examples**

### Example 1:
**Input**:  
`nums = [9], maxOperations = 2`  
**Output**: `3`

**Explanation**:
1. Divide the bag `[9]` into `[6, 3]` ➡️ Max = `6`.  
2. Divide `[6]` into `[3, 3]` ➡️ Max = `3`.  

The penalty is `3`, which is the **minimum** achievable.

---

### Example 2:
**Input**:  
`nums = [2, 4, 8, 2], maxOperations = 4`  
**Output**: `2`

**Explanation**:  
1. Divide `[8]` into `[4, 4]` ➡️ Max = `4`.  
2. Divide `[4]` into `[2, 2]` ➡️ Max = `2`.  

Continue until all operations are used. Penalty = `2`.

---

## 💡 **Key Insights**

- The **penalty** is directly related to how large the bags are.  
- Reducing a bag’s size involves **splitting**, which costs operations.  
- We need to determine the **minimum penalty** such that all splits are within `maxOperations`.

This problem can be solved using **binary search** on the penalty size. Let’s build this step by step! 👷‍♀️

---

## ✨ **Solution Design**

The core idea behind solving this problem lies in **minimizing the maximum penalty**, which translates to controlling the largest number of balls allowed in any bag after performing the given operations. To design the solution effectively, we can break it down into several logical components and decisions:

---

### 1️⃣ **Key Observations**

- **Minimizing the Penalty**:
   - The penalty is the largest bag size after dividing the balls.
   - Smaller penalties require more operations to split large bags into smaller ones.

- **Operation Count**:
   - For a bag with `x` balls and a target maximum size `mx`, the number of operations required to split the bag is:
     $$
     \text{operations} = \left\lfloor \frac{x - 1}{mx} \right\rfloor
     $$
     This ensures all sub-bags are of size ≤ `mx`.

- **Monotonicity of Valid Penalty**:
   - If a penalty value `p` is valid (i.e., you can achieve it within `maxOperations`), then any penalty larger than `p` will also be valid.  
   - Conversely, if `p` is invalid, any penalty smaller than `p` will also be invalid.  

This monotonic behavior allows us to apply **binary search** effectively. 🎯

---

### 2️⃣ **Choosing Binary Search**

The problem asks for the **minimum valid penalty**. Binary search is a natural choice for such minimization problems over a continuous or discrete range with monotonic properties.

#### Why Binary Search?
- Reduces the search space logarithmically, making it efficient even for large ranges like `1` to `10⁹`.
- Allows systematic testing of potential penalties (`midpoints`) while narrowing down the valid range.

---

### 3️⃣ **Helper Function: `check(mx)`**

The helper function determines whether a given penalty `mx` is achievable within the allowed `maxOperations`. It calculates the total number of operations needed to ensure all bags have a size ≤ `mx`.

#### How It Works:
1. Iterate through each bag size `x` in `nums`.
2. Calculate the number of operations needed to split `x` into sub-bags of size ≤ `mx`:
   $$
   \text{operations for bag } x = \left\lfloor \frac{x - 1}{mx} \right\rfloor
   $$
   This formula ensures that any remainder balls are included in one of the sub-bags.
3. Sum up the operations for all bags and compare with `maxOperations`:
   - If the total operations ≤ `maxOperations`, then `mx` is a valid penalty.
   - Otherwise, `mx` is invalid, and we need to try larger penalties.

#### Example:
Suppose `nums = [10, 15, 20]` and `maxOperations = 5`.  
For `mx = 7`, calculate operations:
- Bag `10`: $$ \left\lfloor \frac{10 - 1}{7} \right\rfloor = 1 $$
- Bag `15`: $$ \left\lfloor \frac{15 - 1}{7} \right\rfloor = 2 $$
- Bag `20`: $$ \left\lfloor \frac{20 - 1}{7} \right\rfloor = 2 $$  
**Total operations = 1 + 2 + 2 = 5**, which is valid.

---

### 4️⃣ **Binary Search Implementation**

The binary search algorithm is used to find the minimum penalty:

1. **Define Search Space**:
   - Start with `low = 1` (minimum possible penalty).  
   - Set `high = max(nums)` (maximum possible penalty if no operations are performed).

2. **Binary Search Loop**:
   - Compute the midpoint: $$ mid = \left\lfloor \frac{low + high}{2} \right\rfloor $$.
   - Use the helper function `check(mid)` to determine if `mid` is a valid penalty:
     - If valid, update `high = mid` (try smaller penalties).
     - If invalid, update `low = mid + 1` (try larger penalties).

3. **Termination**:
   - When `low == high`, the minimum valid penalty is found.

---

### 5️⃣ **Efficiency of the Design**

- **Binary Search**:
   - The number of iterations depends on the range of penalties (`1` to `max(nums)`):
     $$
     O(\log(\text{max(nums)}))
     $$

- **Helper Function**:
   - For each midpoint, iterate through `nums` to compute the total operations:
     $$
     O(n)
     $$

- **Overall Complexity**:
   - Combining binary search and helper function:
     $$
     O(n \cdot \log(\text{max(nums)}))
     $$

This design ensures the solution is efficient, even for large input sizes (`n ≈ 10⁵` and `nums[i] ≈ 10⁹`).

---

### 6️⃣ **Edge Cases**

While the design is robust, let’s highlight potential edge cases to test its correctness:

1. **Single Bag**:
   - `nums = [1]`, `maxOperations = 0`:  
     No operations needed, penalty = `1`.

2. **Maximum Ball Count**:
   - `nums = [10⁹]`, `maxOperations = 1`:  
     Must split the bag into two. Penalty = `5 \times 10⁸`.

3. **All Bags Are Small**:
   - `nums = [1, 2, 3]`, `maxOperations = 10`:  
     No need for operations; penalty = `3`.

4. **Insufficient Operations**:
   - `nums = [100, 200]`, `maxOperations = 1`:  
     Impossible to achieve a penalty ≤ `50`. The algorithm should return the best achievable penalty.

5. **Uniform Bag Sizes**:
   - `nums = [5, 5, 5]`, `maxOperations = 2`:  
     Optimal splitting distributes operations evenly.

By incorporating these edge cases, the solution ensures robustness across diverse scenarios. ✅

---

With this comprehensive design in mind, let’s tackle the example walkthrough next! 🛠️

---

## 🧑‍💻 **Python Implementation**

Here’s the Python implementation of the optimized solution:

```python
from typing import List
from bisect import bisect_left

class Solution:
    def minimumSize(self, nums: List[int], maxOperations: int) -> int:
        def check(mx: int) -> bool:
            # Calculate required operations for a given maximum bag size
            return sum((x - 1) // mx for x in nums) <= maxOperations

        # Binary search to find the minimum penalty
        return bisect_left(range(1, max(nums) + 1), True, key=check) + 1
```

---

## 📊 **Time Complexity Analysis**

- **Binary Search**:
  - Search range: `1` to `max(nums)` ➡️ `O(log(max(nums)))`.  
- **Checking Function**:
  - Iterates through `nums` ➡️ `O(n)`.  

**Overall Time Complexity**:  
$$ O(n \cdot \log(\text{max(nums)})) $$  

**Space Complexity**:  
$$ O(1) $$ (no extra space used).

---

## 🔍 **Example Walkthrough**

Let’s apply the solution to `nums = [27, 15, 10]`, `maxOperations = 5`:

### **Step 1**: Define the search range
- Minimum penalty = `1`.  
- Maximum penalty = `27` (largest bag size).

---

### **Step 2**: Binary Search on Penalty

1. **Initial Midpoint**:  
   Penalty = `(1 + 27) // 2 = 14`.  

2. **Check if Penalty = 14 is valid**:
   - Bag `[27]`: Requires `1` operation ➡️ `[14, 13]`.  
   - Bag `[15]`: Requires `1` operation ➡️ `[14, 1]`.  
   - Bag `[10]`: No operation needed.  
   Total = `2 operations` (valid).  

   Update range: `[1, 14]`.

---

3. **New Midpoint**:  
   Penalty = `(1 + 14) // 2 = 7`.

4. **Check if Penalty = 7 is valid**:
   - Bag `[27]`: Requires `3` operations ➡️ `[7, 7, 7, 6]`.  
   - Bag `[15]`: Requires `2` operations ➡️ `[7, 7, 1]`.  
   Total = `5 operations` (valid).  

   Update range: `[1, 7]`.

---

5. **New Midpoint**:  
   Penalty = `(1 + 7) // 2 = 4`.

6. **Check if Penalty = 4 is valid**:
   - Bag `[27]`: Requires `6` operations (exceeds limit).  

   Update range: `[5, 7]`.

---

### Final Result
The minimum penalty is `7`. 🎉

---

## 🚀 **Conclusion**

This problem elegantly combines **binary search** and **greedy logic** to minimize the penalty for splitting bags of balls. The optimized solution handles large inputs efficiently, ensuring a fast and reliable approach.  

Keep tackling challenges like these to sharpen your problem-solving skills! 💪 If you enjoyed this, stay tuned for more insights and tutorials. ✨
