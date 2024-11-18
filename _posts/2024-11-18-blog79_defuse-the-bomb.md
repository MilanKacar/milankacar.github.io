---
layout: post  
title: "#77 🔢📏 1652. Defuse the Bomb 🧠🚀"  
categories: [LeetCode, Programming]  
---

In a world filled with codes and secrets, you are the chosen one to *defuse the bomb*! 🕵️‍♂️💣 Your informer has passed you a **circular array** of numbers and a mysterious key. Can you decode it and save the day? 🌍🔥

---

### 📜 Problem Statement

You are given a **circular array** `code` of length `n` and a key `k`. To decrypt the bomb, replace each number in the array with a specific value based on the following rules:

1. If `k > 0`, replace the $$i^{th}$$ number with the sum of the next `k` numbers in the circular array.
2. If `k < 0`, replace the $$i^{th}$$ number with the sum of the previous `|k|` numbers in the circular array.
3. If `k == 0`, replace the $$i^{th}$$ number with `0`.

The numbers wrap around in the circular array, meaning the next element after the last is the first element, and the previous element before the first is the last element.

---

### 🌟 Examples

#### Example 1:
**Input**:  
`code = [5,7,1,4]`, `k = 3`  
**Output**:  
`[12, 10, 16, 13]`  
**Explanation**:  
Replace each number with the sum of the next `3` numbers:  
- For index 0: `7 + 1 + 4 = 12`  
- For index 1: `1 + 4 + 5 = 10`  
- For index 2: `4 + 5 + 7 = 16`  
- For index 3: `5 + 7 + 1 = 13`  

---

#### Example 2:
**Input**:  
`code = [1,2,3,4]`, `k = 0`  
**Output**:  
`[0, 0, 0, 0]`  
**Explanation**:  
When `k == 0`, every number is replaced with `0`.

---

#### Example 3:
**Input**:  
`code = [2,4,9,3]`, `k = -2`  
**Output**:  
`[12, 5, 6, 13]`  
**Explanation**:  
Replace each number with the sum of the previous `2` numbers:  
- For index 0: `3 + 9 = 12`  
- For index 1: `2 + 3 = 5`  
- For index 2: `4 + 2 = 6`  
- For index 3: `9 + 4 = 13`  

---

### 🚀 Basic Solution

Let's implement a straightforward solution that directly simulates the process:

1. Loop through each element of the array.
2. Depending on the sign of `k`:
   - If `k > 0`, sum the next `k` elements.
   - If `k < 0`, sum the previous `|k|` elements.
3. Wrap indices around using modular arithmetic to handle the circular array.

---

#### 🐍 Python Code for the Basic Solution

```python
def decrypt(code, k):
    n = len(code)
    result = [0] * n
    
    if k == 0:
        return result
    
    for i in range(n):
        if k > 0:
            result[i] = sum(code[(i + j) % n] for j in range(1, k + 1))
        else:
            result[i] = sum(code[(i - j) % n] for j in range(1, abs(k) + 1))
    
    return result
```

---

#### ⏱ Time Complexity

- **Time Complexity**: $$O(n \cdot |k|)$$, as for each of the $$n$$ elements, we sum up to $$|k|$$ elements.  
- **Space Complexity**: $$O(n)$$, for the result array.

---

### ⚡ Optimized Solution

The basic solution works but is not efficient for larger values of `n` and `|k|`. To optimize:

1. Use a **sliding window** approach:
   - Maintain a running sum of the required elements.
   - Update the running sum efficiently as you move through the array.
2. Handle the circular array using modular arithmetic.

---

#### 🐍 Python Code for the Optimized Solution

```python
def decrypt(code, k):
    n = len(code)
    result = [0] * n
    
    if k == 0:
        return result
    
    start, end, step = (1, k, 1) if k > 0 else (n - 1, n + k - 1, -1)
    window_sum = sum(code[i % n] for i in range(start, end + 1, step))
    
    for i in range(n):
        result[i] = window_sum
        window_sum -= code[(start + i * step) % n]
        window_sum += code[(end + 1 + i * step) % n]
    
    return result
```

---

#### ⏱ Time Complexity

- **Time Complexity**: $$O(n)$$, as the sliding window reduces redundant calculations.  
- **Space Complexity**: $$O(n)$$, for the result array.

---

### 🔍 Example Walkthrough

#### Input: `code = [5, 7, 1, 4, 6]`, `k = 3`  
1. **Initial Setup**:  
   - Start: $$1$$, End: $$3$$, Step: $$1$$  
   - Initial Window Sum: $$7 + 1 + 4 = 12$$.  

2. **Decryption Process**:  
   - **Index 0**: Replace with $$12$$. Update window: Add $$6$$, Remove $$7$$. New sum: $$1 + 4 + 6 = 11$$.  
   - **Index 1**: Replace with $$11$$. Update window: Add $$5$$, Remove $$1$$. New sum: $$4 + 6 + 5 = 15$$.  
   - **Index 2**: Replace with $$15$$.  

**Final Result**: `[12, 11, 15, 13, ...]`  

---

### 🔍 Edge Cases

1. **Empty Array**: Return an empty array.  
2. **Single Element**: If `n = 1`, the output depends on `k`:
   - If $$k = 0$$: `[0]`
   - If $$k > 0$$: Repeat the element `k` times.
3. **Large $$k$$**: Ensure modular arithmetic handles wrapping.  

---

### 🏁 Conclusion

This problem highlights the importance of understanding circular arrays and optimizing solutions for efficiency.  

- The **basic solution** is easy to implement but scales poorly for larger arrays.  
- The **optimized solution** leverages a sliding window approach to significantly improve performance.  

Keep learning and happy coding! 🚀
