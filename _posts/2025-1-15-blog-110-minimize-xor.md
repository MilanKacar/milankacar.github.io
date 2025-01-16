---
layout: post  
title: "#110 1️⃣1️⃣0️⃣ 2429. Minimize XOR 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Greedy, Bit Manipulation]
---

Bitwise operations often feel like solving puzzles 🤔, and the "Minimize XOR" problem brings an intriguing challenge to the table. This problem combines set bits, XOR operations, and some clever decision-making to find a unique solution. Ready to dive into this binary journey? Let’s decode it together! 🖥️🔍

---

### 📜 Problem Statement 📜

Given two positive integers `num1` and `num2`, find a positive integer `x` such that:

1. `x` has the same number of set bits (1's in its binary representation) as `num2`, and  
2. The value `x XOR num1` is minimal.

The problem guarantees that a unique `x` exists for the given input.

**Constraints:**

- $$1 \leq \text{num1}, \text{num2} \leq 10^9$$

---

### ✨ Examples ✨

#### Example 1:

- **Input:** `num1 = 3`, `num2 = 5`  
- **Output:** `3`  
- **Explanation:**  
  The binary representations are:  
  - `num1`: `0011` (decimal `3`)  
  - `num2`: `0101` (decimal `5`, with 2 set bits).  
  Among integers with 2 set bits, `3` gives the minimal XOR value (`3 XOR 3 = 0`).

#### Example 2:

- **Input:** `num1 = 1`, `num2 = 12`  
- **Output:** `3`  
- **Explanation:**  
  The binary representations are:  
  - `num1`: `0001` (decimal `1`)  
  - `num2`: `1100` (decimal `12`, with 2 set bits).  
  Among integers with 2 set bits, `3` minimizes `x XOR num1`.

---

### 🔍 Understanding the Problem 🔍

The essence of this problem lies in **bit manipulation** and understanding how XOR behaves. Let's break it down step by step:

1. **XOR Properties**:  
   - $$ a \, \text{XOR} \, a = 0 $$ (a number XORed with itself is 0).  
   - XOR highlights differences in bits, so minimizing the XOR value means making the binary representations of `x` and `num1` as similar as possible.

2. **Set Bits**:  
   The task asks us to construct a number `x` that has the **same number of set bits** (1's in its binary form) as `num2`. This ensures that we honor the condition provided.

3. **Minimization Objective**:  
   To minimize $$ x \, \text{XOR} \, \text{num1} $$, align the **set bits of `x`** with the **set bits of `num1`** wherever possible, starting from the **most significant bits (MSBs)**.  
   - MSBs contribute more to the numerical value than LSBs, so prioritize them.

4. **Filling Remaining Bits**:  
   If the required number of set bits is not fulfilled by aligning with `num1`, use the **least significant bits (LSBs)** to complete the count.

---

### 🛠️ Approach to the Solution 🛠️

Here’s how the problem is tackled in your solution:

1. **Count Set Bits in `num2`**:  
   Use `num2.bit_count()` to determine the number of set bits (1's). This tells us how many bits `x` must have.

2. **Align Set Bits from `num1`**:  
   Traverse the bits of `num1` from MSB to LSB:
   - If a bit in `num1` is set, include it in `x`.  
   - Decrement the `bits` count until it reaches zero (quota fulfilled).

3. **Fill Remaining Bits Using LSBs**:  
   If the quota of set bits isn’t met by aligning with `num1`, iterate over the LSBs of `num1` and set additional bits in `x` as needed.

4. **Return the Result**:  
   Once all the required set bits are placed, return the constructed `x`.

---

### 🚀 Python Code 🚀

Here’s the exact code provided, explained step by step:

```python
class Solution:
    def minimizeXor(self, num1: int, num2: int) -> int:
        kMaxBit = 30  # Maximum number of bits to consider
        bits = num2.bit_count()  # Count the set bits in num2

        # If num1 already has the same number of set bits as num2, return num1
        if num1.bit_count() == bits:
            return num1

        ans = 0

        # Use MSBs of num1 to align with the number of set bits in num2
        for i in reversed(range(kMaxBit)):
            if num1 >> i & 1:  # Check if the ith bit of num1 is set
                ans |= 1 << i  # Set the ith bit in the result
                bits -= 1  # Decrement the remaining set bits quota
                if bits == 0:  # If the quota is fulfilled, return the result
                    return ans

        # Fill the remaining set bits using LSBs
        for i in range(kMaxBit):
            if (num1 >> i & 1) == 0:  # Check if the ith bit of num1 is NOT set
                ans |= 1 << i  # Set the ith bit in the result
                bits -= 1  # Decrement the remaining set bits quota
                if bits == 0:  # If the quota is fulfilled, return the result
                    return ans

        return ans  # Return the final constructed number
```

---

### 🧠 Intuition Behind the Code 🧠

1. **Maximize Alignment with `num1`**:  
   Start from the **most significant bits** of `num1` because these have the highest value. By aligning `x`’s bits with these, we minimize the XOR value early.

2. **Handle Unmet Quota with LSBs**:  
   If we can’t fully satisfy the quota with MSBs, fill the remaining bits starting from the least significant position. This ensures that the additional bits contribute minimally to the XOR value.

3. **Efficiency**:  
   By limiting the loop to $$O(30)$$ (the number of bits in a 32-bit integer), the algorithm runs in constant time for practical purposes.

---

### ✨ Example Walkthrough ✨

Let’s break down an example with **higher numbers** to see the solution in action.

#### Input:
- `num1 = 29`  
  Binary: `11101` (4 set bits)  
- `num2 = 7`  
  Binary: `00111` (3 set bits)  

---

#### Step-by-Step Execution:

1. **Set Bits in `num2`**:  
   `bits = 3` (we need `x` to have 3 set bits).  

2. **Align with MSBs of `num1`**:  
   Start from the MSB:
   - Bit 4 (value 16): Set in `num1` → Include in `x` → `x = 16`, `bits = 2`.  
   - Bit 3 (value 8): Set in `num1` → Include in `x` → `x = 24`, `bits = 1`.  
   - Bit 2 (value 4): Set in `num1` → Include in `x` → `x = 28`, `bits = 0`.  

   No more set bits are needed.

3. **Final Value of `x`**:  
   `x = 28` (binary `11100`).

4. **Calculate XOR**:  
   $$ 29 \, \text{XOR} \, 28 = 1 $$ (minimal value).

---

Let’s use another example with new numbers for better clarity. 😊

### **Example Input**

- `num1 = 45`  
  Binary Representation: `101101`  
- `num2 = 15`  
  Binary Representation: `01111`  

Our goal is to find a number $$ x $$ such that:  
1. $$ x $$ has the **same number of set bits** (1's) as `num2` (4 set bits).  
2. $$ x \, \text{XOR} \, \text{num1} $$ is **minimized**.

---

### **Step-by-Step Execution**

---

#### **Step 1: Count Set Bits in `num2`**

We start by counting the number of `1`s in `num2`.

- Binary of `num2`: `01111`  
- Set bits in `num2`: `4`  

So, `bits = 4`.  
This means $$ x $$ must have exactly **4 set bits**.

---

#### **Step 2: Align with Most Significant Bits (MSBs) of `num1`**

To minimize $$ x \, \text{XOR} \, \text{num1} $$, we try to align the set bits of $$ x $$ with the set bits of `num1`, starting from the **most significant bits (MSBs)**.

1. **Binary of `num1`:** `101101`  
2. Traverse each bit of `num1` from left (MSB) to right (LSB):  
   - **Bit 5** (Value $$ 32 $$):  
     - `num1` has a `1` here.  
     - Set the corresponding bit in $$ x $$.  
     - $$ x = 100000 $$ (Decimal: $$ 32 $$)  
     - Remaining `bits = 3`.

   - **Bit 4** (Value $$ 16 $$):  
     - `num1` has a `0` here.  
     - Do nothing.  
     - $$ x = 100000 $$ (Decimal: $$ 32 $$)  

   - **Bit 3** (Value $$ 8 $$):  
     - `num1` has a `1` here.  
     - Set the corresponding bit in $$ x $$.  
     - $$ x = 101000 $$ (Decimal: $$ 40 $$)  
     - Remaining `bits = 2`.

   - **Bit 2** (Value $$ 4 $$):  
     - `num1` has a `1` here.  
     - Set the corresponding bit in $$ x $$.  
     - $$ x = 101100 $$ (Decimal: $$ 44 $$)  
     - Remaining `bits = 1`.

   - **Bit 1** (Value $$ 2 $$):  
     - `num1` has a `0` here.  
     - Do nothing.  
     - $$ x = 101100 $$ (Decimal: $$ 44 $$)  

   - **Bit 0** (Value $$ 1 $$):  
     - `num1` has a `1` here.  
     - Set the corresponding bit in $$ x $$.  
     - $$ x = 101101 $$ (Decimal: $$ 45 $$)  
     - Remaining `bits = 0`.

---

#### **Step 3: Fill Remaining Bits if Needed**

If we still have remaining `bits` after aligning with `num1`, we turn on the **least significant bits (LSBs)** in $$ x $$. However, in this case, the required set bits (`bits = 4`) have already been filled.

---

#### **Step 4: Verify the XOR Minimization**

- $$ x = 45 $$  
- Binary of $$ x $$: `101101`  
- Set bits in $$ x $$: `4` (matches `num2`).

Now calculate $$ x \, \text{XOR} \, \text{num1} $$:  
- $$ 45 \, \text{XOR} \, 45 = 0 $$.  

Since the XOR result is $$ 0 $$, this is the **minimal possible value**.

---

### **Visualization of Execution**

| Bit Position | `num1` Bit | `x` Bit | Reason                   |
|--------------|------------|---------|--------------------------|
| 5            | 1          | 1       | Align MSB from `num1`.   |
| 4            | 0          | 0       | No set bit in `num1`.    |
| 3            | 1          | 1       | Align MSB from `num1`.   |
| 2            | 1          | 1       | Align MSB from `num1`.   |
| 1            | 0          | 0       | No set bit in `num1`.    |
| 0            | 1          | 1       | Align MSB from `num1`.   |

---

### **Edge Cases**

1. **Case 1: Same Number of Set Bits in `num1` and `num2`**  
   - Example: `num1 = 7` (Binary: `111`), `num2 = 11` (Binary: `1011`)  
   - $$ x = \text{num1} $$, since $$ x \, \text{XOR} \, \text{num1} = 0 $$.

2. **Case 2: `num1` is Smaller than `num2` in Set Bits**  
   - Example: `num1 = 2` (Binary: `10`), `num2 = 7` (Binary: `111`).  
   - Fill all set bits from `num1`, then use LSBs to meet `num2`'s bit count.

3. **Case 3: `num2 = 1`**  
   - `num2` has only 1 set bit, so $$ x $$ will be the **least significant set bit of `num1`** or $$ 1 $$ if no set bits exist.

---


### 📚 Edge Cases 📚

1. **Exact Match**:  
   If `num1` already has the same number of set bits as `num2`, the result is simply `num1`.

2. **Small Inputs**:  
   - Example: `num1 = 1`, `num2 = 1`.  
   Result: `1`.

3. **Large Inputs**:  
   - Example: `num1 = 10^9`, `num2 = 10^9`.  
   The solution works efficiently within $$O(30)$$.

---

### Time Complexity 🕒

- **Counting Set Bits**: $$O(\log(\text{num2}))$$  
- **Bit Manipulation Loops**: $$O(32 + 32) = O(64)$$ (constant time for fixed 32-bit integers).  

**Overall Time Complexity**: **$$O(1)$$** for practical purposes.  
**Space Complexity**: $$O(1)$$.

---

### 🏁 Conclusion 🏁

The "Minimize XOR" problem is a fascinating dive into bit manipulation and optimization techniques. By carefully aligning set bits of `x` with those of `num1`, and filling any gaps from the LSBs, we achieve the smallest possible XOR value.  

Your provided code not only solves the problem efficiently but also demonstrates excellent use of bitwise operations. Mastering problems like this helps you tackle a wide range of challenges involving binary representations. Keep coding and conquering! 🚀
