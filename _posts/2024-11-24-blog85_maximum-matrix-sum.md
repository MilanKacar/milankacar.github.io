---
layout: post  
title: "#85 1975. Maximum Matrix Sum 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Greedy, Matrix]
---

Have you ever worked with numbers in a matrix and wondered how to manipulate them for maximum value? 💭 This problem lets us explore a fascinating technique where flipping the signs of adjacent numbers helps us maximize the total matrix sum. Intriguing, right? Let's dive into the mechanics, supported by plenty of explanations, examples, and edge cases to make sure you master it! 🚀✨  

---

### **The Problem in Simple Terms 📝**  

You are given an \( n \times n \) matrix where each element is an integer. You can perform the following operation as many times as you like:  

- Select two **adjacent elements** (horizontally or vertically connected) and flip their signs, i.e., multiply each of them by \(-1\).  

Your task is to find the **maximum possible sum** of the matrix after optimally applying this operation.  

---

### Why is This Problem Tricky? 🤔  

At first glance, flipping adjacent numbers might seem overwhelming because of the many combinations possible.  
- Which numbers should you flip?  
- How do you decide whether flipping will increase the sum?  
- Is there a mathematical shortcut to avoid brute force?  

This problem challenges us to **think logically and strategically**. We need to shift our mindset from individual flips to understanding the **bigger picture** of how negatives and positives interact.  

---

### **Key Observations 🔍**  

Before jumping to a solution, let's break down some critical insights:  

#### 1. **The Goal is Maximization**  
The absolute values of numbers determine their potential contribution to the sum. For example:  
- A positive \( 5 \) contributes \( +5 \).  
- A negative \( -5 \), if flipped to \( 5 \), can contribute \( +5 \) instead of \( -5 \).  

#### 2. **Flipping Negatives**  
- Negatives can be turned into positives by flipping.  
- If two negatives are adjacent, flipping both will leave them negative again.  
- If two positives are adjacent, flipping them makes them negative, reducing the sum.  

This means we must be **selective** in flipping negatives while ensuring that unnecessary flips do not hurt the total.  

#### 3. **Theoretical Maximum**  
The **ideal maximum** sum is achieved when:  
- All elements are positive.  
- This happens when the matrix sum equals the sum of **absolute values** of all elements.  

#### 4. **Dealing with Odd Negatives**  
If there’s an odd number of negative numbers in the matrix, one negative will always remain. Why?  
- Flipping an even number of negatives cancels them out entirely, turning all into positives.  
- But an odd count leaves one unpaired negative, which will persist in the final matrix.  

To deal with this, we need to minimize the penalty from this one unavoidable negative. The **smallest absolute value** becomes key, as it minimizes the impact of the leftover negative.  

---

### **The Plan to Solve It 🚀**  

1. Calculate the **sum of absolute values** of all elements. This gives us the maximum theoretical sum.  
2. Count the total number of negative elements.  
3. Identify the **smallest absolute value** in the matrix.  
4. If the count of negatives is **even**, no penalty is applied, and we achieve the maximum sum.  
5. If the count is **odd**, subtract twice the smallest absolute value from the total to account for the one unavoidable negative.  

This approach works because it balances the number of flips needed while ensuring the maximum sum is achieved efficiently.  

---

### **Implementation in Python 💻**  

Here’s how the solution looks in Python:  

```python
def maxMatrixSum(matrix):
    total_sum = 0
    neg_count = 0
    min_abs = float('inf')  # Start with infinity for comparison
    
    # Traverse the matrix
    for row in matrix:
        for value in row:
            # Add absolute values to total_sum
            total_sum += abs(value)
            # Count negatives
            if value < 0:
                neg_count += 1
            # Track smallest absolute value
            min_abs = min(min_abs, abs(value))
    
    # If the count of negatives is odd, adjust the result
    if neg_count % 2 == 0:
        return total_sum
    else:
        return total_sum - 2 * min_abs
```

---

### **Step-by-Step Walkthrough 🛠️**  

Let’s break it down with **Example 1**:  

**Input**:  
\[
\text{matrix} = \begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}
\]  

#### Step 1: Compute Absolute Sum  
- Absolute values: \( |1| + |-1| + |-1| + |1| = 1 + 1 + 1 + 1 = 4 \).  
- So, \( \text{total_sum} = 4 \).  

#### Step 2: Count Negatives  
- Number of negatives: \( -1, -1 \Rightarrow 2 \).  

#### Step 3: Smallest Absolute Value  
- Smallest value: \( 1 \).  

#### Step 4: Adjust for Odd Negatives  
- Since \( 2 \) is even, no adjustment is needed.  

**Output**: \( 4 \).  

---

Now, let’s tackle **Example 2**, which is more complex.  

**Input**:  
\[
\text{matrix} = \begin{bmatrix} 1 & 2 & 3 \\ -1 & -2 & -3 \\ 1 & 2 & 3 \end{bmatrix}
\]  

#### Step 1: Compute Absolute Sum  
- Absolute values:  
  \[
  |1| + |2| + |3| + |-1| + |-2| + |-3| + |1| + |2| + |3| = 18
  \]  
- So, \( \text{total_sum} = 18 \).  

#### Step 2: Count Negatives  
- Negatives: \( -1, -2, -3 \Rightarrow 3 \).  

#### Step 3: Smallest Absolute Value  
- Smallest value: \( 1 \).  

#### Step 4: Adjust for Odd Negatives  
- Since \( 3 \) is odd, subtract \( 2 \times 1 \):  
  \[
  \text{Adjusted Sum} = 18 - 2 \cdot 1 = 16
  \]  

**Output**: \( 16 \).  

---

### **Edge Cases 🧩**  

1. **All Positives**  
   Input:  
   \[
   \begin{bmatrix} 1 & 2 \\ 3 & 4 \end{bmatrix}
   \]  
   Output: \( 10 \).  
   Explanation: No negatives, so sum is already maximum.  

2. **All Negatives**  
   Input:  
   \[
   \begin{bmatrix} -1 & -2 \\ -3 & -4 \end{bmatrix}
   \]  
   Output: \( 10 \).  
   Explanation: All negatives can be flipped to positives.  

3. **Single Negative**  
   Input:  
   \[
   \begin{bmatrix} 1 & 2 \\ 3 & -4 \end{bmatrix}
   \]  
   Output: \( 10 \).  
   Explanation: Only one negative, which is unavoidable.  

---

### **Complexity Analysis ⏱️**  

- **Time Complexity**: \( O(n^2) \) — Traverse each element in the \( n \times n \) matrix.  
- **Space Complexity**: \( O(1) \) — Only constants are used.  

---

### **Conclusion 🎉**  

This problem beautifully illustrates how mathematical insight can simplify what seems like a complex task. By focusing on the **absolute sum**, **negative counts**, and **smallest values**, we crafted an efficient solution with just one traversal of the matrix.  

Matrix manipulation problems like this sharpen your problem-solving skills and prepare you for more advanced challenges. Keep coding and conquering! 🚀
