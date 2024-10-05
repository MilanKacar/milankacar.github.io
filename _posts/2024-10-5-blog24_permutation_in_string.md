---
layout: post
title: "#24 567. Permutation in String 🧠🚀"
categories: [LeetCode, Programming]
---

You're given two strings `s1` and `s2`. The goal is to check if `s2` contains a permutation of `s1`. In other words, you want to return `True` if any permutation of `s1` exists as a substring in `s2`, and `False` otherwise. The challenge lies in optimizing this process! 🚀

---

### 📝 Problem Statement

Given two strings `s1` and `s2`, return `True` if `s2` contains a permutation of `s1`, or `False` otherwise.

#### **Example 1:**
**Input**:  
`s1 = "ab"`,  
`s2 = "eidbaooo"`

**Output**: `True`  
**Explanation**:  
`s2` contains one permutation of `s1` ("ba").

#### **Example 2:**
**Input**:  
`s1 = "ab"`,  
`s2 = "eidboaoo"`

**Output**: `False`

---

### 🛠️ Base Solution (Brute Force)

In the brute force solution, we generate **all permutations** of `s1` and check if any of them appear as a substring of `s2`. This approach is straightforward but inefficient for large inputs.

### 📜 Python Code for Base Solution:

```python
from itertools import permutations

def checkInclusion_brute_force(s1: str, s2: str) -> bool:
    perms = [''.join(p) for p in permutations(s1)]
    
    # Check if any permutation is a substring in s2
    for perm in perms:
        if perm in s2:
            return True
    return False
```

### ⏳ Time Complexity of Base Solution:

The time complexity for this brute-force method would be **O(n!)** for generating permutations (where `n` is the length of `s1`), and then for each permutation, checking for its presence in `s2` takes **O(m)** time (where `m` is the length of `s2`). This makes it extremely inefficient for larger inputs.

---

### 🏎️ Optimized Solution (Sliding Window Without `Counter`)

To optimize this, we use the fact that **two strings are permutations if and only if they have the same frequency of characters**. We can manually maintain a frequency array of size 26 (since there are 26 lowercase letters) and use a sliding window over `s2` to track character frequencies.

#### 🔍 Explanation:  
1. Create two frequency arrays: one for `s1` and one for the current window in `s2`.
2. The arrays will track the occurrence of each character (from 'a' to 'z') by using their positions (i.e., index `0` for 'a', `1` for 'b', etc.).
3. Slide the window of size `len(s1)` across `s2`, updating the frequency array for `s2` and checking if it matches the frequency array of `s1`.

### 📜 Python Code for Optimized Solution:

```python
def checkInclusion(s1: str, s2: str) -> bool:
    if len(s1) > len(s2):
        return False

    # Initialize frequency arrays for s1 and s2's first window
    s1_freq = [0] * 26
    s2_freq = [0] * 26
    
    # Fill frequency arrays for s1 and the first window of s2
    for i in range(len(s1)):
        s1_freq[ord(s1[i]) - ord('a')] += 1
        s2_freq[ord(s2[i]) - ord('a')] += 1

    # Compare frequency arrays of s1 and the initial window of s2
    if s1_freq == s2_freq:
        return True

    # Sliding window across s2
    for i in range(len(s1), len(s2)):
        # Add the new character to the window
        s2_freq[ord(s2[i]) - ord('a')] += 1
        # Remove the character that moves out of the window
        s2_freq[ord(s2[i - len(s1)]) - ord('a')] -= 1

        # Compare after adjusting the window
        if s1_freq == s2_freq:
            return True

    return False
```

### ⏳ Time Complexity of Optimized Solution:

This solution ensures that we only traverse `s2` once, and the character comparison is constant time (since the size of the frequency array is fixed at 26). Therefore, the overall time complexity is **O(m)**, where `m` is the length of `s2`.

---

### 🔧 Solution for Different Problem (Dividing Players)

You've shared an additional problem-solving approach to **divide players** into pairs based on their skill levels. Let’s break that down too!

#### **Python Code for Optimal Solution**:

```python
class Solution:
    def dividePlayers(self, skill: List[int]) -> int:
        if len(skill) % 2 != 0:
            return -1

        if len(skill) == 2:
            return skill[0] * skill[-1]

        skill = sorted(skill)
        j = -1
        first_pair = skill[0] + skill[-1]
        chemistry = skill[0] * skill[-1]

        for i in range(1, len(skill) // 2):
            temp1 = skill[i]
            temp2 = skill[j - i]
            if temp1 + temp2 != first_pair:
                return -1
            chemistry += temp1 * temp2

        return chemistry
```

### 📊 Explanation:

In this problem, we aim to divide players into pairs such that each pair’s combined skill is the same. If it’s impossible to form such pairs, return `-1`.

- **Sort the array**: Start by sorting the skill list, then pair the smallest and largest values.
- **Check pairing consistency**: Ensure the sum of the skills of each pair is consistent.
- **Chemistry Calculation**: Calculate the chemistry (product of skills) for each valid pair.

**Time Complexity**:  
Since sorting dominates this approach, the overall time complexity is **O(n log n)**, where `n` is the number of players.

---

### ✨ Conclusion:

1. **Brute Force vs. Optimized for Permutation Problem**:  
   The brute force solution involves generating permutations and is inefficient with **O(n!)** complexity. The sliding window method, especially using frequency arrays, is much more efficient with **O(m)** complexity for this permutation detection problem. 🎉
   
2. **Dividing Players**:  
   Sorting-based approaches, as used in the second problem, are common when you need to pair elements and solve in **O(n log n)** time.

Both of these optimized solutions provide an efficient way to tackle their respective problems! 🌟
