---
layout: post  
title: "#98 2554. Maximum Number of Integers to Choose From a Range I 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, HashTable, Binary Search, Greedy, Sorting]
---

In this blog post, we're tackling a fun and challenging problem: selecting integers while respecting some tricky rules. It’s like solving a puzzle where every piece must fit perfectly. 🤓 Let’s dive in and break it down step by step! 💪

---

### 📝 Problem Statement 📋

You are given an integer array `banned`, and two integers `n` and `maxSum`. Your task is to choose integers from the range `[1, n]` such that:

1. **No integers are from the `banned` list.**
2. **Each integer can be chosen at most once.**
3. **The sum of the chosen integers must not exceed `maxSum`.**

Your goal is to **maximize the number of integers you can choose** while following the above rules.

---

#### 🔍 Examples 🔗

**Example 1:**
```plaintext
Input: banned = [1,6,5], n = 5, maxSum = 6
Output: 2
Explanation: You can choose integers 2 and 4. Their sum is 6, which meets the constraints.
```

**Example 2:**
```plaintext
Input: banned = [1,2,3,4,5,6,7], n = 8, maxSum = 1
Output: 0
Explanation: You cannot choose any integer within the constraints.
```

**Example 3:**
```plaintext
Input: banned = [11], n = 7, maxSum = 50
Output: 7
Explanation: All integers from 1 to 7 can be chosen, as their sum is 28, which is within `maxSum`.
```

---

### 🛠️ Constraints 📐

- $$ 1 \leq \text{banned.length} \leq 10^4 $$
- $$ 1 \leq \text{banned[i]}, n \leq 10^4 $$
- $$ 1 \leq \text{maxSum} \leq 10^9 $$

---

### 🧠 Approach and Solutions 🧩

Understanding the problem and its nuances is key to crafting an efficient solution. Here's a more detailed breakdown of how we approach this problem and arrive at the optimal solution.

---

#### 🧩 Step 1: Analyze the Problem

We aim to maximize the count of integers selected from the range `[1, n]` while adhering to three constraints:

1. **No banned numbers**: Numbers in the `banned` list are off-limits.
2. **Unique selection**: Each number can only be chosen once.
3. **Sum constraint**: The cumulative sum of selected numbers cannot exceed `maxSum`.

**Key Observations**:
- The problem revolves around efficiently managing constraints while maximizing the selection.
- We need to identify valid numbers, starting from the smallest ones, to maximize the count before hitting the `maxSum` threshold.
- The order of numbers matters: prioritizing smaller numbers ensures we stay within the `maxSum` limit longer.

---

#### 🛠️ Step 2: Outline the Solution

1. **Filter the Banned Numbers**:
   - Use a set to store banned numbers. This ensures constant-time lookups, critical for keeping our solution efficient.
   
2. **Iterate Through the Range `[1, n]`**:
   - Start with the smallest number in the range. This is a greedy choice since smaller numbers contribute less to the cumulative sum, allowing us to maximize the count.
   - For each number:
     - Skip if it's banned.
     - Add it to the cumulative sum if it doesn’t exceed `maxSum`.

3. **Stop Early**:
   - Once adding another number would exceed `maxSum`, break the loop. There’s no need to evaluate further numbers.

4. **Return the Count**:
   - The final count represents the maximum number of integers we can choose.

---

#### 📋 Pseudocode for Clarity

Here’s the pseudocode that captures the above logic:

```plaintext
1. Initialize banned_set = set(banned)
2. Initialize total_sum = 0, count = 0
3. For each number i in range 1 to n:
      a. If i is in banned_set, skip to the next number
      b. If total_sum + i > maxSum, break the loop
      c. Otherwise, add i to total_sum and increment count
4. Return count
```

---

#### 💡 Why Greedy Works Here

The greedy approach works because:
- **Smaller numbers**: Selecting numbers from smallest to largest ensures we can pick the maximum count without breaching `maxSum`.
- **Banned set optimization**: Using a set to track banned numbers minimizes overhead, allowing us to focus on valid selections.

---

### 🔍 Alternative Approaches: Why Not Use Them?

It’s worth exploring other potential solutions to understand why the greedy approach is optimal.

---

#### 1. **Sort and Filter First**
One approach could involve:
- Filtering out banned numbers from the range `[1, n]`.
- Sorting the remaining numbers.
- Iterating through the sorted list and summing them until reaching `maxSum`.

**Downside**:
- Sorting adds unnecessary overhead of $$ O(n \log n) $$, while the greedy approach avoids sorting altogether by iterating directly.

---

#### 2. **Dynamic Programming**
We could think about this as a subset-sum problem and try solving it with dynamic programming (DP). Each number could either be included or excluded, and we’d track the maximum count of selected integers.

**Downside**:
- DP requires additional memory and computational complexity ($$ O(n \cdot \text{maxSum}) $$), which isn’t practical for larger inputs.

---

### 🔑 Detailed Steps in Greedy Approach

Let’s break down each part of the greedy approach for clarity.

#### 1. **Using a Set for Banned Numbers**
```python
banned_set = set(banned)
```
- The `set` ensures $$ O(1) $$ lookups for each number, keeping our algorithm efficient.
- This step preprocesses the banned numbers, which is necessary to quickly check validity during iteration.

#### 2. **Iterate and Skip Banned Numbers**
```python
for i in range(1, n + 1):
    if i in banned_set:
        continue
```
- By iterating from `1` to `n`, we evaluate all possible candidates.
- The `continue` statement skips numbers that are banned.

#### 3. **Check Sum Constraint**
```python
if total_sum + i > maxSum:
    break
```
- This is a critical check. Once the cumulative sum exceeds `maxSum`, we stop processing further numbers.

#### 4. **Update Total and Count**
```python
total_sum += i
count += 1
```
- For valid numbers, we update the cumulative sum and increment the count.

---

### 🛠️ Space and Time Complexity Analysis

#### 🕒 Time Complexity
1. **Creating the Banned Set**:
   - Takes $$ O(b) $$, where $$ b $$ is the length of the `banned` array.
   
2. **Iterating Through Range**:
   - Takes $$ O(n) $$, where $$ n $$ is the upper limit of the range.

Overall: $$ O(n + b) $$, which is highly efficient.

#### 💾 Space Complexity
1. **Banned Set**:
   - Requires $$ O(b) $$ additional space for storing banned numbers.

Overall: $$ O(b) $$.

---

### 🔍 Edge Cases 🧩

Here are additional edge cases to consider:

#### Edge Case 1: `n` is very large, but `maxSum` is very small
```plaintext
Input: banned = [], n = 10^4, maxSum = 1
Output: 0
```
**Explanation**: Even though the range is large, the small `maxSum` prevents selecting any numbers.

---

#### Edge Case 2: All numbers are banned
```plaintext
Input: banned = [1, 2, 3, 4, 5], n = 5, maxSum = 15
Output: 0
```
**Explanation**: No numbers can be selected because they are all banned.

---

#### Edge Case 3: `maxSum` is extremely large
```plaintext
Input: banned = [], n = 100, maxSum = 10^9
Output: 100
```
**Explanation**: The sum constraint is effectively irrelevant, so we can select all numbers.

---

#### Edge Case 4: Large `banned` list with sparse valid numbers
```plaintext
Input: banned = [1, 3, 5, 7, 9], n = 10, maxSum = 15
Output: 4
```
**Explanation**: The valid numbers are $$ 2, 4, 6, 8 $$, and their sum $$ 2 + 4 + 6 + 8 = 20 $$ exceeds `maxSum`. We only take $$ 2, 4, 6 $$.

---

#### 🔑 Key Insights

1. **Filter banned numbers**: Create a set of banned numbers for quick lookup.
2. **Iterate through valid numbers**: Loop through numbers $$ 1 $$ to $$ n $$ and skip the banned ones.
3. **Maintain sum constraint**: Track the cumulative sum and stop adding when exceeding `maxSum`.
4. **Maximize count**: Count how many numbers we can include.

---

### 🛠️ Implementation: One Optimal Solution 🚀

Since both the **basic** and **optimized** solutions have the same time complexity, we'll focus on the single optimal approach.

#### Optimal Solution 💡

**Steps:**
1. Add all banned numbers to a set for $$ O(1) $$ lookup.
2. Iterate through numbers $$ 1 $$ to $$ n $$:
   - Skip the number if it's in the banned set.
   - Add the number to the cumulative sum if it doesn't exceed `maxSum`.
   - Stop if adding another number would exceed `maxSum`.
3. Return the total count of selected numbers.

**Time Complexity:** $$ O(n + b) $$, where $$ n $$ is the upper limit of the range, and $$ b $$ is the length of the banned array.

---

### 🐍 Python Code 🖥️

```python
def maxCount(banned, n, maxSum):
    # Step 1: Create a set of banned numbers
    banned_set = set(banned)
    total_sum = 0
    count = 0

    # Step 2: Iterate over the range [1, n]
    for i in range(1, n + 1):
        if i in banned_set:
            continue  # Skip banned numbers
        if total_sum + i > maxSum:
            break  # Stop if adding i exceeds maxSum
        total_sum += i
        count += 1

    return count
```

---

### 📊 Example Walkthrough: Large Case 🔍

Let’s walk through a larger example step-by-step to solidify the understanding:

**Input:**
```plaintext
banned = [5, 10, 15]
n = 20
maxSum = 50
```

**Process:**
1. **Initialize**: 
   - `banned_set = {5, 10, 15}`
   - `total_sum = 0`, `count = 0`

2. **Iterate from 1 to 20**:
   - $$ i = 1 $$: Not banned, $$ total\_sum = 1 $$, $$ count = 1 $$
   - $$ i = 2 $$: Not banned, $$ total\_sum = 3 $$, $$ count = 2 $$
   - $$ i = 3 $$: Not banned, $$ total\_sum = 6 $$, $$ count = 3 $$
   - $$ i = 4 $$: Not banned, $$ total\_sum = 10 $$, $$ count = 4 $$
   - $$ i = 5 $$: **Banned**. Skip.
   - $$ i = 6 $$: Not banned, $$ total\_sum = 16 $$, $$ count = 5 $$
   - $$ i = 7 $$: Not banned, $$ total\_sum = 23 $$, $$ count = 6 $$
   - $$ i = 8 $$: Not banned, $$ total\_sum = 31 $$, $$ count = 7 $$
   - $$ i = 9 $$: Not banned, $$ total\_sum = 40 $$, $$ count = 8 $$
   - $$ i = 10 $$: **Banned**. Skip.
   - $$ i = 11 $$: Adding exceeds `maxSum`. **Stop.**

**Output:** 
```plaintext
8
```

---

### 🔍 Edge Cases 🧩

1. **All numbers are banned**:
   ```plaintext
   Input: banned = [1,2,3,4,5], n = 5, maxSum = 10
   Output: 0
   ```
   Explanation: No valid numbers to choose.

2. **High maxSum**:
   ```plaintext
   Input: banned = [], n = 10, maxSum = 1000
   Output: 10
   ```
   Explanation: All numbers can be chosen since the sum constraint is easily satisfied.

3. **Small maxSum**:
   ```plaintext
   Input: banned = [1], n = 10, maxSum = 1
   Output: 0
   ```
   Explanation: The sum constraint makes it impossible to choose any number.

4. **Sparse banned numbers**:
   ```plaintext
   Input: banned = [3, 8], n = 10, maxSum = 25
   Output: 6
   ```
   Explanation: Chosen numbers are $$ 1, 2, 4, 5, 6, 7 $$, with a sum of $$ 25 $$.

---

### 🏁 Conclusion 🎯

This problem demonstrates the power of a **greedy approach** and the importance of efficiently managing constraints. By leveraging sets for fast lookups and iterating systematically, we maximize the number of integers while respecting all the rules.

---

### ⭐ Final Thoughts 🌟

Problems like this are great for honing your skills in **algorithmic thinking** and **constraint management**. Whether you're prepping for interviews or sharpening your problem-solving abilities, tackling these challenges will make you a better programmer. 🚀

Ready for the next challenge? Let’s keep coding! 💻
