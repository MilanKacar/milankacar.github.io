---
layout: post  
title: "#109 🔢 2466. Count Ways To Build Good Strings 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [String, Dynamic Programming]
---

Imagine you're tasked with creating good strings 🧵 from two components (`zero` and `one`), while keeping their lengths within a range (`low` and `high`). The goal is to calculate how many such strings you can generate, modulo $$10^9 + 7$$. This problem combines recursion, memoization, and creativity in dynamic programming. Let's dive deep into this fascinating challenge! 🚀  

---

### 📜 Problem Statement  
Given integers `low`, `high`, `zero`, and `one`, the task is to count how many strings of lengths between `low` and `high` (inclusive) can be formed. A string length is defined as a sum of `zero` or `one` components.  

Each component can be added repeatedly to form valid lengths. The result should be calculated modulo $$10^9 + 7$$.  

---

### 🛠 Example  
**Input:**  
```  
low = 3, high = 10, zero = 2, one = 3  
```  

**Output:**  
```  
5  
```  

**Explanation:**  
The valid lengths are 3, 5, 6, 8, and 10. Here's how they can be formed:  
- Length 3: `3 * one`  
- Length 5: `2 * zero + one`  
- Length 6: `3 * two`  
- Length 8: `4 * zero`  
- Length 10: `5 dot ..  



Let’s continue expanding on this problem with all the details you asked for, including edge cases, example walkthroughs, and thorough explanations.

To summarize:
1. We are given integers `low`, `high`, `zero`, and `one`.
2. We need to count the number of valid string lengths $$ L $$ such that:
   - $$ low \leq L \leq high $$
   - $$ L $$ can be formed by repeatedly adding `zero` and `one`.
3. The result should be returned modulo $$10^9 + 7$$.

---

### 📝 Example Walkthrough

#### Input:  
```python
low = 3, high = 10, zero = 2, one = 3
```

#### Output:  
```python
5
```

#### Valid Lengths:  
We iterate through possible lengths from `low` to `high`:

1. **Length 3**: This is possible because $$ 1 \times \text{one} = 3 $$.
2. **Length 5**: Achievable with $$ 1 \times \text{zero} + 1 \times \text{one} = 5 $$.
3. **Length 6**: Possible using $$ 2 \times \text{three} 

.

Let's refine the walkthrough and detail all aspects of this problem!

---
Got it! Let’s dive into the **optimized solution** and focus on explaining the intuition, approach, and solution in-depth while skipping the brute force method.

---

### 🌟 Count Good Strings 🌟  
**Challenge Yourself with Dynamic Programming Magic**  

---

### 🎯 Intuition Behind the Problem  

At its core, this problem is about **constructing lengths** using two building blocks:  
1. A smaller increment (`zero`).  
2. A larger increment (`one`).  

The challenge lies in efficiently counting valid lengths between `low` and `high` that can be constructed by repeatedly adding these two building blocks.  

Here’s the thought process:  

1. **Recursive Exploration**:  
   Start with an initial length (e.g., 0) and explore all possible lengths by adding `zero` and `one`. This forms a tree-like structure where each node represents a possible length, and branches extend the length further.

2. **Validating the Range**:  
   Only lengths between `low` and `high` count as valid. If a length exceeds `high`, we prune further exploration since it will lead to invalid results.

3. **Avoid Redundant Work**:  
   Many intermediate lengths will be revisited multiple times through different paths. For example, a length of `6` can be reached by:  
   - Adding `zero` three times.  
   - Adding `one` twice.  
   
   To avoid recalculating the count for the same length repeatedly, we use **memoization**.

4. **Modulo Operation**:  
   Since the results can grow large, every intermediate calculation is taken modulo $$10^9 + 7$$.

---

### 💡 Approach: Optimized Solution Using Dynamic Programming  

We solve this problem with a **recursive + memoization approach**:  

1. **Recursive Function (DFS)**:  
   - Start at length `0`.  
   - At each step, explore the two options:  
     - Add `zero` to the current length.  
     - Add `one` to the current length.  
   - If the new length falls in the range `[low, high]`, it contributes to the count.  

2. **Memoization**:  
   - Store results of previously computed lengths in a dictionary (`dp`).  
   - This avoids recomputing the count for the same length and makes the solution efficient.  

3. **Base Cases**:  
   - If the current length exceeds `high`, return `0`.  
   - If the length is in `dp`, return the stored result.  

4. **Modulo Handling**:  
   - Use $$10^9 + 7$$ for all additions to handle large numbers.  

---

### 📝 Optimized Code  

```python
class Solution:
    def countGoodStrings(self, low: int, high: int, zero: int, one: int) -> int:
        dp = {}  # Memoization dictionary
        MOD = 10**9 + 7  # Modulo value

        def dfs(length):
            # Base case: length exceeds the valid range
            if length > high:
                return 0
            
            # Return memoized result
            if length in dp:
                return dp[length]

            # Start with current length's validity
            dp[length] = 1 if low <= length <= high else 0

            # Recursively explore the next lengths
            dp[length] += dfs(length + zero) + dfs(length + one)
            dp[length] %= MOD  # Apply modulo

            return dp[length]

        # Start recursion from length 0
        return dfs(0)
```

---

Let’s break these important steps down to understand their purpose and how they contribute to solving the problem.

### Step 1: `dp[length] = 1 if low <= length <= high else 0`  

**Purpose:**  
This step checks whether the current length (`length`) falls within the valid range $$[ \text{low}, \text{high} ]$$:  

1. **Valid Length**:  
   If the `length` satisfies $$ \text{low} \leq \text{length} \leq \text{high} $$, then it is a valid "good string," and we initialize the count for this length as `1`.

2. **Invalid Length**:  
   If the `length` does not fall in this range, it is not a "good string," and its count is initialized as `0`.

---

#### Example:  
- **Input:** `low = 3, high = 10`  
- Current `length`:  
  - **Case 1:** `length = 4`  
    - $$3 \leq 4 \leq 10$$ is true, so `dp[4] = 1`.  
  - **Case 2:** `length = 2`  
    - $$3 \leq 2 \leq 10$$ is false, so `dp[2] = 0`.

---

### Step 2: `dp[length] += dfs(length + zero) + dfs(length + one)`  

**Purpose:**  
This step recursively explores further possible lengths that can be constructed by adding either `zero` or `one` to the current length. The results of these recursive calls are added to the current length's count.

---

#### Breaking it Down:  

1. **Explore Length `length + zero`**:  
   Call `dfs(length + zero)` to compute the count of valid strings starting from `length + zero`.  

2. **Explore Length `length + one`**:  
   Similarly, call `dfs(length + one)` to compute the count of valid strings starting from `length + one`.

3. **Accumulate Results**:  
   Add the counts obtained from these recursive calls to the current length's count. This way, the `dp[length]` value accumulates:  
   - The count of valid strings for `length`.  
   - The count of valid strings that can be formed by extending `length` further using `zero` or `one`.

---

#### Why Recursion Works:  
- Each recursive call explores deeper levels of possible lengths.  
- If a length exceeds `high`, the recursion terminates early (base case).  
- If a length falls within the valid range, it contributes to the count and triggers further exploration.

---

#### Example Walkthrough:  

**Input:** `low = 3, high = 10, zero = 2, one = 3`  
Let’s assume `length = 3`.

1. **Initialize `dp[3]`**:  
   $$3 \geq \text{low}$$ and $$3 \leq \text{high}$$, so:  
   $$
   dp[3] = 1
   $$

2. **Explore `length + zero` (3 + 2 = 5)**:  
   Call `dfs(5)`. If `dfs(5)` returns `2` (valid paths from `5`), we add this to `dp[3]`.

3. **Explore `length + one` (3 + 3 = 6)**:  
   Call `dfs(6)`. If `dfs(6)` returns `3` (valid paths from `6`), we add this to `dp[3]`.

4. **Accumulate Results**:  
   After adding both contributions, `dp[3]` becomes:  
   $$
   dp[3] = 1 + 2 + 3 = 6
   $$

This means starting from `length = 3`, there are **6 valid paths** to construct lengths within the range `[low, high]`.

---

### Key Points  

- **Initialization**: The line `dp[length] = 1 if low <= length <= high else 0` ensures each valid length contributes at least `1` to the count.  
- **Recursive Accumulation**: The line `dp[length] += dfs(length + zero) + dfs(length + one)` adds contributions from all valid extensions of the current length.  

This combination ensures we efficiently calculate the total count of valid strings while avoiding redundant work with memoization. 🚀

---

### 🔍 Intuition Simplified  

Imagine you're building "good strings" as paths on a tree:  

1. **Start from Root**: Begin at length 0.  
2. **Branch Out**: Each step extends the current length by either `zero` or `one`.  
3. **Check Validity**: At every node (length), check if it’s between `low` and `high`. If yes, it’s a valid string!  
4. **Prune the Tree**: If a length exceeds `high`, stop further branching—it won’t contribute to the count.  
5. **Memoize**: Once a length is calculated, store the result to reuse it for future branches.  

This process ensures we only explore valid paths and reuse computations wherever possible.

---

### 📚 Example Walkthrough  

Let’s break down the recursive process with an example:  

#### Input:  
```python
low = 3, high = 10, zero = 2, one = 3
```

#### Execution Steps  

1. **Start at Length 0**:  
   $$ \text{dfs}(0) $$  
   - $$ \text{Add zero: dfs}(2) $$  
   - $$ \text{Add one: dfs}(3) $$  

2. **Process Length 2**:  
   $$ \text{dfs}(2) $$  
   - $$ \text{Add zero: dfs}(4) $$  
   - $$ \text{Add one: dfs}(5) $$  

3. **Process Length 3**:  
   $$ \text{dfs}(3) $$  
   - Valid length ($$3 \geq \text{low}$$) contributes 1.  
   - $$ \text{Add zero: dfs}(5) $$  
   - $$ \text{Add one: dfs}(6) $$  

4. **Continue Recursion**:  
   - Explore lengths $$5, 6, 8, 10$$ similarly.  
   - Stop when a length exceeds 10.  

#### Final Count:  
The valid lengths are $$3, 5, 6, 8, 10$$, resulting in a total count of $$5$$.

---


### Edge Cases  

1. **Small Ranges**  
   - Input: `low = 1, high = 1, zero = 1, one = 1`  
   - Output: $$1$$, as only length 1 is valid.  

2. **Zero-Length Range**  
   - Input: `low = 5, high = 4, zero = 1, one = 2`  
   - Output: $$0$$, as `low > high`.  

3. **Large Inputs**  
   - Input: `low = 1, high = 10^6, zero = 2, one = 3`  
   - Ensure the solution handles large ranges efficiently.  


---

### 🕒 Time Complexity  
- Each unique length is computed once, resulting in $$ O(\text{high}) $$.  
- Space complexity: $$ O(\text{high}) $$ for the memoization table.  

---

### 📚 Example Walkthrough with Optimized Solution  

#### Input  
```python
low = 3, high = 10, zero = 2, one = 3
```

#### Execution Steps  
1. Start with `length = 0`.  
2. Recursive calls:  
   - `length + zero = 2`  
   - `length + one = 3`  

3. Continue recursion, storing results in `dp`:  
   - Valid lengths: $$ 3, 5, 6, 8, 10 $$.  

4. Result: $$ dp[0] = 5 $$.  

---

### ✨ Conclusion  

- **Brute Force**: Simple but inefficient for large inputs.  
- **Dynamic Programming**: Efficient and scalable, especially for large ranges.  

This problem showcases how **recursion and memoization** can optimize solutions for combinatorial problems. 🧵 Dynamic programming reduces unnecessary computations, allowing us to handle even the largest inputs effectively. 🚀  

Happy coding! 😊
