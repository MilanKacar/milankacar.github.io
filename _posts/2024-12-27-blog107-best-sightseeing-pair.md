---
layout: post  
title: "#107 1014. Best Sightseeing Pair 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Dynamic Programming]
---


Ever planned a sightseeing tour and wondered which two spots would maximize your experience? While real-life planning can be tricky, solving this problem algorithmically is super fun and insightful! Today, we'll dive deep into a fascinating problem where we aim to find the *best sightseeing pair*. Get ready for a journey full of numbers, logic, and, of course, some fun examples! 🚀

---

## 🧩 **Problem Statement**  
We are given an array `values`, where each `values[i]` represents the "value" of the `i`-th sightseeing spot. A pair of sightseeing spots `(i, j)` has a **score** defined as:  

\[
\text{score} = \text{values}[i] + \text{values}[j] + i - j
\]

Here, the term \(i - j\) represents the **distance penalty** for selecting sightseeing spots that are farther apart.  

**Goal**: Return the maximum score for any valid pair `(i, j)` where \(i < j\).  

---

### Constraints  
1. \(2 \leq \text{values.length} \leq 50,000\)  
2. \(1 \leq \text{values}[i] \leq 1,000\)  

---

## ✍️ **Example**  

### Example 1:  
**Input**:  
```python
values = [8, 1, 5, 2, 6]
```  

**Output**:  
```python
11
```  

**Explanation**:  
For \(i = 0\) and \(j = 2\):  
\[
\text{score} = 8 + 5 + 0 - 2 = 11
\]  

### Example 2:  
**Input**:  
```python
values = [1, 2]
```  

**Output**:  
```python
2
```  

---


### 🌟 **Solution Approach: A Deeper Dive**  

Understanding how to approach the solution is key to mastering this problem. Let’s dissect the process step by step, exploring both the brute force and optimized strategies in greater detail. We'll also highlight how mathematical insights lead to efficient problem-solving.

---

### **Key Observations**  

Before diving into solutions, it’s essential to note a few key observations about the problem:  

1. **Pair Score Formula**:  
   The score for any pair \((i, j)\) is defined as:  
   \[
   \text{score} = \text{values}[i] + \text{values}[j] + i - j
   \]  
   This can be rearranged as:  
   \[
   \text{score} = (\text{values}[i] + i) + (\text{values}[j] - j)
   \]  

   Here, \(\text{values}[i] + i\) is a term that only depends on \(i\), while \(\text{values}[j] - j\) only depends on \(j\). This separation is critical for optimizing the solution.

2. **Reducing Redundancy**:  
   In a brute-force solution, we compute \(\text{values}[i] + \text{values}[j] + i - j\) for all pairs \((i, j)\). This involves \(O(n^2)\) operations. However, by keeping track of the maximum \((\text{values}[i] + i)\) as we iterate through the array, we can avoid recalculating this term repeatedly.

3. **Dynamic Maximum Tracking**:  
   As we iterate through the array, we can maintain a running maximum of \(\text{values}[i] + i\). This allows us to compute the score for each \(j\) in \(O(1)\) time.

---

### 💡 **Brute-Force Solution**  

The brute-force solution involves two nested loops to iterate over all possible pairs \((i, j)\), where \(i < j\). For each pair, we calculate the score and update the maximum score if the current pair’s score is greater.  

---

### Brute-Force Python Code  

```python
class Solution:
    def maxScoreSightseeingPair(self, values: List[int]) -> int:
        n = len(values)
        max_sum = 0
        
        for i in range(n):
            for j in range(i + 1, n):
                score = values[i] + values[j] + i - j
                max_sum = max(max_sum, score)
        
        return max_sum
```

---

### **Analysis of the Brute-Force Solution**  

1. **Time Complexity**:  
   - The brute-force solution requires two nested loops. For each \(i\), we iterate over all \(j > i\), leading to \(O(n^2)\) complexity.  
   - This is infeasible for large input sizes (e.g., \(n = 50,000\)) due to excessive computations.

2. **Space Complexity**:  
   - The solution uses only a few variables for tracking the maximum score, so the space complexity is \(O(1)\).  

---

### 🚀 **Optimized Solution: Key Idea**  

The optimized solution leverages the separation of terms in the score formula:  
\[
\text{score} = (\text{values}[i] + i) + (\text{values}[j] - j)
\]  

- As we iterate through the array, we maintain the maximum value of \((\text{values}[i] + i)\) seen so far. Let’s call this `first`.  
- For each \(j\), we compute the score using `first` and \((\text{values}[j] - j)\).  
- We update the maximum score if the current score is greater than the previous maximum.  

---

### **Step-by-Step Explanation**  

1. **Initialization**:  
   - Start by setting `first` to the value of \(\text{values}[0] + 0\), as this is the only valid value for \(i = 0\) initially.  
   - Initialize `max_sum` to 0 to track the maximum score.

2. **Iterate Through the Array**:  
   - For each index \(j\) starting from 1, compute the current score using `first` and \(\text{values}[j] - j\).  
   - Update `max_sum` if the current score is higher than the previous maximum.  

3. **Update `first`**:  
   - After computing the score for \(j\), update `first` to the maximum of its current value and \(\text{values}[j] + j\). This ensures that `first` always holds the best \((\text{values}[i] + i)\) for indices up to \(j\).  

---

Instead of iterating through all pairs, we can break the problem into smaller subproblems. Notice that:  
\[
\text{score} = (\text{values}[i] + i) + (\text{values}[j] - j)
\]  

This allows us to maintain a running maximum of \((\text{values}[i] + i)\) as we iterate through the array.  

### Python Code:  
```python
class Solution:
    def maxScoreSightseeingPair(self, values: List[int]) -> int:
        first = values[0]  # Tracks the best `values[i] + i` seen so far
        max_sum = 0
        
        for j in range(1, len(values)):
            # Calculate the score with the current `j`
            second = values[j] - j
            max_sum = max(max_sum, first + second)
            
            # Update the `first` for the next iteration
            first = max(first, values[j] + j)
        
        return max_sum
```

---

### **Time Complexity**  
This approach uses a single loop, making it **O(n)**. It is much more efficient than the basic solution and handles large inputs effectively.

---


### 🔢 Example Walkthrough

#### Input:
`values = [8, 1, 5, 2, 6]`

#### Step-by-Step Process:
| Iteration | `j` | `first`  | `second`       | `max_sum` | Explanation                                 |
|-----------|------|----------|----------------|-----------|---------------------------------------------|
| Initial   | -    | `8 + 0 = 8` | -              | `0`       | Initialize first term with `values[0]`.     |
| 1         | 1    | `8`      | `1 - 1 = 0`    | `8 + 0 = 8` | Add the first and second terms.             |
| 2         | 2    | `8`      | `5 - 2 = 3`    | `8 + 3 = 11`| New max sum found!                          |
| 3         | 3    | `8`      | `2 - 3 = -1`   | `11`      | Keep the max sum as no better score exists. |
| 4         | 4    | `8`      | `6 - 4 = 2`    | `11`      | No improvement in the max sum.              |

#### Output:
`11`

#### Edge Cases:
1. **Small Input:** `values = [1, 2]`  
   - Only one valid pair exists; output is `2`.
2. **High Values:** `values = [1000, 999, 998, …]`  
   - Ensure the solution handles large values efficiently.
3. **Uniform Values:** `values = [1, 1, 1, 1, 1]`  
   - All pairs yield the same score; output should match correctly.


---

## 📚 **Conclusion**  

- The **basic solution** provides a simple approach but is computationally expensive with \(O(n^2)\) complexity.  
- The **optimized solution** achieves \(O(n)\) complexity by leveraging a running maximum for the first term and iterating through the array efficiently.  

With the optimized approach, this problem becomes solvable even for large input sizes. 🎉  

Try implementing this yourself and see how smoothly it works! 🚀  


