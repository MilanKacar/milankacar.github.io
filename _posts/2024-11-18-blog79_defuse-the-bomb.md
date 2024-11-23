---
layout: post  
title: "#79 💣 1652. Defuse the Bomb 🧠🚀"   
categories: [LeetCode, Programming]
difficulty: Easy
tags: [Array, Sliding Window]
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

### 🔥 Optimal Solution: Sliding Window Magic! 🔥

To efficiently solve the "Defuse the Bomb" problem, we can use the **sliding window technique**, which reduces redundant computations and leverages the circular nature of the array. This method is faster and more elegant compared to directly iterating over every possible sum.

---

### 🛠️ Implementation

Here’s the Python implementation of the sliding window solution:

```python
def decrypt(code, k):
    n = len(code)
    result = [0] * n  # Initialize the result array with zeros

    if k == 0:
        return result  # If k == 0, all values are replaced with 0

    # Create an extended array to handle the circular nature
    extended_code = code + code

    # Define the window range
    start, end = (1, k) if k > 0 else (n + k, n - 1)
    
    # Compute the initial sum for the sliding window
    window_sum = sum(extended_code[start:end + 1])
    
    for i in range(n):
        result[i] = window_sum
        # Slide the window by updating the sum
        window_sum -= extended_code[start]
        start += 1
        end += 1
        window_sum += extended_code[end]

    return result
```

---

### 💡 How It Works

1. **Handle Circular Array with `extended_code`**:
   - Instead of manually wrapping around the array using modular arithmetic, we extend the array by concatenating it with itself. This simplifies accessing circular elements.

2. **Sliding Window Technique**:
   - Compute the initial sum of the range `[start:end + 1]`.
   - Slide the window by:
     - Subtracting the element leaving the window.
     - Adding the element entering the window.
   - This avoids recalculating the sum from scratch for each index.

3. **Adjusting Indices for `k > 0` and `k < 0`**:
   - When `k > 0`, the window includes the next `k` elements (`start=1`, `end=k`).
   - When `k < 0`, the window includes the previous `|k|` elements (`start=n+k`, `end=n-1`).

---

### 🧮 Example Walkthrough: `code = [2, 4, 9, 3], k = -2`

#### Step-by-Step:

1. **Setup**:
   - `n = 4`, `extended_code = [2, 4, 9, 3, 2, 4, 9, 3]`.
   - `start = 2` (n + k), `end = 3` (n - 1).
   - Initial window: `[9, 3]`, `window_sum = 12`.

2. **Iteration**:
   - **Index 0**:
     - `result[0] = 12`.
     - Slide the window:
       - Subtract `9`, add `2`.
       - `window_sum = 12 - 9 + 2 = 5`.

   - **Index 1**:
     - `result[1] = 5`.
     - Slide the window:
       - Subtract `3`, add `4`.
       - `window_sum = 5 - 3 + 4 = 6`.

   - **Index 2**:
     - `result[2] = 6`.
     - Slide the window:
       - Subtract `2`, add `9`.
       - `window_sum = 6 - 2 + 9 = 13`.

   - **Index 3**:
     - `result[3] = 13`.
     - Slide the window:
       - Subtract `4`, add `3`.
       - `window_sum = 13 - 4 + 3 = 12`.

#### Final Output:
- `result = [12, 5, 6, 13]`.

---

### 🕒 Time Complexity

- **Window Initialization**: $$O(|k|)\) to compute the initial sum of the window.
- **Sliding Window**: $$O(n)\), as each index is processed exactly once.
- **Overall**: $$O(n + |k|)\).

### 🧠 Space Complexity

- **Extended Array**: $$O(n)\), due to the concatenation.
- **Result Array**: $$O(n)\).
- **Overall**: $$O(n)\).

---

### 🔍 Edge Cases

1. **`k = 0`**:
   - Input: `code = [1, 2, 3], k = 0`
   - Output: `[0, 0, 0]` (all values are replaced with 0).

2. **Single Element Array**:
   - Input: `code = [10], k = 1`
   - Output: `[0]` (no other elements to sum).

3. **Negative `k`**:
   - Input: `code = [1, 2, 3, 4], k = -1`
   - Output: `[4, 1, 2, 3]`.

4. **Maximum `k`**:
   - Input: `code = [1, 2, 3, 4], k = 3`
   - Output: `[9, 9, 9, 9]`.

---

### 🔥 Conclusion

The sliding window approach significantly optimizes the problem by avoiding redundant computations and leveraging the circular array property effectively. It ensures optimal performance and handles all edge cases gracefully.

Keep learning and happy coding! 🚀
