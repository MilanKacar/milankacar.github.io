---
layout: post  
title: "#108 🔢 1639. Number of Ways to Form a Target String Given a Dictionary 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Dynamic Programming]
---

Let's dive into an exciting problem that challenges our understanding of dynamic programming! 🚀 We're tasked with figuring out the number of ways to form a target string using characters from a list of strings, under some specific constraints. Ready to explore this fascinating challenge? Let’s go! 🎉

---

### 📜 Problem Statement

You are given:

- A list of strings `words`, where each string has the same length.
- A string `target`.

Your task is to determine how many ways you can form the `target` string by selecting characters from `words`. The rules are as follows:

1. **Target formation:** Form the `target` string from left to right.
2. **Character selection:** To form the `i-th` character of `target`, you can pick any character from the same column (`k-th` position) of any string in `words` if it matches the character in `target[i]`.
3. **Index restriction:** Once you use a character at index `k` from any string in `words`, all characters at indices ≤ `k` in every string become unavailable.
4. **Repetition allowed:** You can use multiple characters from the same string.

Return the number of ways to form the `target` string, modulo \(10^9 + 7\).

---

### 🖼️ Examples

#### Example 1

**Input:**

```python
words = ["acca", "bbbb", "caca"]
target = "aba"
```

**Output:**

```python
6
```

**Explanation:**

There are six ways to form `"aba"`:

1. `"aba"` -> index 0 ("acca"), index 1 ("bbbb"), index 3 ("caca")
2. `"aba"` -> index 0 ("acca"), index 2 ("bbbb"), index 3 ("caca")
3. `"aba"` -> index 0 ("acca"), index 1 ("bbbb"), index 3 ("acca")
4. `"aba"` -> index 0 ("acca"), index 2 ("bbbb"), index 3 ("acca")
5. `"aba"` -> index 1 ("caca"), index 2 ("bbbb"), index 3 ("acca")
6. `"aba"` -> index 1 ("caca"), index 2 ("bbbb"), index 3 ("caca")

---

#### Example 2

**Input:**

```python
words = ["abba", "baab"]
target = "bab"
```

**Output:**

```python
4
```

**Explanation:**

There are four ways to form `"bab"`:

1. `"bab"` -> index 0 ("baab"), index 1 ("baab"), index 2 ("abba")
2. `"bab"` -> index 0 ("baab"), index 1 ("baab"), index 3 ("baab")
3. `"bab"` -> index 0 ("baab"), index 2 ("baab"), index 3 ("baab")
4. `"bab"` -> index 1 ("abba"), index 2 ("baab"), index 3 ("baab")

---

### 🧠 Thought Process

Before diving into the solution, let’s take a step back and understand the intuition behind solving the problem. The problem asks us to find the number of ways to form a target string from a list of words. This might seem challenging at first due to the constraints, but breaking it into smaller subproblems reveals an efficient approach using **dynamic programming**.

#### Step 1: Understand the Problem 🔍
The primary challenge is:
1. **Forming the target string**: Each character in the target can be chosen from any string in the `words` list, but the selection rules are strict:
   - Characters in each word are accessed left-to-right.
   - Once a character is used, all earlier indices in that word are "locked" and cannot be reused.

This means:
- To form a character in the target, we must count how many times that character appears at a given index across all words.
- We need to combine these counts recursively to determine the total number of ways to form the entire target.

#### Step 2: Break Down the Problem 🧩
To form the `target[i]` using the characters in `words`, we must decide:
1. **Skip** the current index `k` in `words` and move to the next index. This decision ensures we leave the current column untouched for forming later parts of the target.
2. **Use** the current column to form `target[i]`. Here, the number of options depends on how often `target[i]` appears in column `k` of `words`. We then recursively attempt to form the rest of the target (`target[i+1:]`) from the remaining columns.

#### Step 3: Dynamic Programming (DP) Insight 💡
Dynamic programming helps us efficiently compute overlapping subproblems:
- Define `dp[i][k]` as the number of ways to form the substring `target[i:]` using columns `k` to the end of `words`.
- Base cases:
  - If `i == len(target)`, we’ve formed the entire target, so return 1.
  - If `k == len(words[0])`, we’ve exhausted the columns and cannot form the target, so return 0.
- Recurrence relation:
  - Skip the current column: `dp[i][k] += dp[i][k+1]`.
  - Use the current column if `target[i]` exists in column `k`: `dp[i][k] += frequency[target[i]][k] * dp[i+1][k+1]`.

#### Key Observations 🔍
1. **Precompute Frequencies**: Instead of repeatedly counting occurrences of `target[i]` in each column, precompute a frequency table `freq` where `freq[c][k]` is the count of character `c` in column `k`.
2. **Recursive Transition**: Each state depends on two smaller subproblems: one where the current column is skipped, and one where it contributes to forming the target.
3. **Modulo Constraint**: Since the answer can be very large, every addition is computed modulo \(10^9 + 7\).

---

### Dynamic Programming Approach with Example 🚀

Let’s now implement this approach step-by-step.

#### Precomputing Frequency Table 📊
To efficiently count how many times each character appears in each column:
- Iterate over all words.
- For each column `k`, update the frequency of each character.

```python
from collections import defaultdict

words = ["acca", "bbbb", "caca"]
target = "aba"

# Precompute frequencies
freq = defaultdict(lambda: [0] * len(words[0]))
for word in words:
    for i, char in enumerate(word):
        freq[char][i] += 1
```

After running this code, `freq` will look like:
```
{
 'a': [2, 0, 1, 1],
 'b': [0, 3, 0, 0],
 'c': [1, 0, 2, 1]
}
```

#### Recursive Function with Memoization 📚
Using the precomputed frequency table, define the recursive function `dfs(i, k)`:
- `i` is the index in `target` we are forming.
- `k` is the column in `words` we are considering.

---

### Example Walkthrough 🔍

#### Input:
```python
words = ["acca", "bbbb", "caca"]
target = "aba"
```

#### Precomputed Frequency Table:

| Column (Index) | 'a' | 'b' | 'c' |
|----------------|-----|-----|-----|
| 0              | 2   | 0   | 1   |
| 1              | 0   | 3   | 0   |
| 2              | 1   | 0   | 2   |
| 3              | 1   | 0   | 1   |

#### Recursive Calls:
1. Start at `dfs(0, 0)` (forming "aba", starting from column 0):
   - **Skip column 0**: `dfs(0, 1)`.
   - **Use column 0 ('a')**: Add `freq['a'][0] * dfs(1, 1)`.

2. At `dfs(1, 1)` (forming "ba", starting from column 1):
   - **Skip column 1**: `dfs(1, 2)`.
   - **Use column 1 ('b')**: Add `freq['b'][1] * dfs(2, 2)`.

3. At `dfs(2, 2)` (forming "a", starting from column 2):
   - **Skip column 2**: `dfs(2, 3)`.
   - **Use column 2 ('a')**: Add `freq['a'][2] * dfs(3, 3)`.

4. At `dfs(3, 3)`:
   - Base case: Return 1 (target formed).

---

### 💡 Basic Solution (Brute Force)

#### Approach:

1. For each character of `target`, iterate through all columns of `words`.
2. Count how many characters in the current column match the current `target` character.
3. Recursively find all possible ways to form the remaining part of `target`.

---

#### Code:

```python
def numWays(words, target):
    MOD = 10**9 + 7

    def countWays(i, k):
        if i == len(target):  # Successfully formed the target
            return 1
        if k == len(words[0]):  # Ran out of columns
            return 0

        # Count the number of matches for target[i] in column k
        matches = sum(1 for word in words if word[k] == target[i])

        # Skip the current column or use it and move to the next character
        return (countWays(i, k + 1) + matches * countWays(i + 1, k + 1)) % MOD

    return countWays(0, 0)
```

---

#### Time Complexity:

The brute force approach is highly inefficient due to repeated work. Each recursive call has two branches, leading to exponential complexity:

- \( O(2^{\text{len(words[0])}} \cdot \text{len(target)}) \).

---

### ⚡ Optimized Solution (Dynamic Programming)

We can improve the brute force approach by using **memoization** to store the results of subproblems. This avoids redundant calculations.

#### Key Ideas:

1. **Frequency Table:** Precompute the frequency of each character in every column of `words`.
2. **Memoization:** Store the results of `(i, k)` in a dictionary `dp` to avoid recalculating.
3. **Recursion:** Use a recursive function to calculate the number of ways to form the target using columns starting from `k`.

---

#### Code:

```python
from collections import defaultdict

class Solution:
    def numWays(self, words, target):
        MOD = 10**9 + 7
        cnt = defaultdict(int)

        # Build frequency table for each column
        for word in words:
            for i, c in enumerate(word):
                cnt[(i, c)] += 1

        dp = {}

        def dfs(i, k):
            if i == len(target):  # Successfully formed the target
                return 1
            if k == len(words[0]):  # Ran out of columns
                return 0
            if (i, k) in dp:  # Return memoized result
                return dp[(i, k)]

            # Option 1: Skip the current column
            dp[(i, k)] = dfs(i, k + 1)

            # Option 2: Use the current column if it matches target[i]
            c = target[i]
            if (k, c) in cnt:
                dp[(i, k)] += cnt[(k, c)] * dfs(i + 1, k + 1)

            dp[(i, k)] %= MOD
            return dp[(i, k)]

        return dfs(0, 0)
```

---

#### Time Complexity:

- **Building Frequency Table:** \( O(n \cdot m) \), where \( n \) is the number of strings in `words` and \( m \) is their length.
- **Dynamic Programming:** \( O(t \cdot m) \), where \( t \) is the length of `target`.

Total time complexity: \( O(n \cdot m + t \cdot m) \).

#### Space Complexity:

- Frequency table: \( O(26 \cdot m) \) for storing character counts.
- DP table: \( O(t \cdot m) \).

---

### 🔍 Example Walkthrough

**Input:**

```python
words = ["abba", "baab"]
target = "bab"
```

1. **Frequency Table:**

| Column | 'a' | 'b' |
|--------|-----|-----|
| 0      | 1   | 1   |
| 1      | 1   | 1   |
| 2      | 1   | 1   |
| 3      | 1   | 1   |

2. **Recursive DP Steps:**

- Starting at \( i=0, k=0 \), match 'b' using column 0, 1, or 2.
- Move to \( i=1, k=1 \), match 'a'.
- Move to \( i=2, k=2 \), match 'b'.

3. **Final Output:** \( 4 \) ways.

---

### 🛠️ Edge Cases

1. **No Matching Characters:** If `target` contains characters not in `words`, return \( 0 \).
2. **Empty Target:** If `target` is an empty string, there's exactly one way to form it: do nothing.
3. **Empty Words:** If `words` is empty, return \( 0 \).

---

### 🌟 Conclusion

This problem highlights the power of dynamic programming in solving complex combinatorial problems efficiently. By using memoization and a precomputed frequency table, we reduced what would have been an exponential time complexity to a manageable polynomial one. 🎉

Ready to tackle more problems like this? 🚀
