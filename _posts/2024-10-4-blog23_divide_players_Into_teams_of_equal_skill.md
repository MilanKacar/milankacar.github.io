---
layout: post
title: "#23 2491. Divide Players Into Teams of Equal Skill 🧠🚀"
categories: [LeetCode, Programming]
---

In competitive games, forming balanced teams is essential to ensure fair play. Imagine you’re tasked with dividing players into teams where each team has the same combined skill level. Not only that, but the chemistry of each team is calculated as the product of their individual skills. Sounds like a challenge, right? Let's break down the problem and find a solution step by step!

---

### Problem Statement 📜

You are given an array of positive integers where each element represents the skill level of a player. Your task is to divide the players into pairs (teams of size 2), ensuring that the total skill of each team is the same. The chemistry of each team is defined as the product of the skills of the two players. You need to return the total chemistry of all teams. If it's impossible to divide the players into teams with equal total skill, return `-1`.

---

### Example 🔍

#### Example 1:
**Input**: `skill = [3, 2, 5, 1, 3, 4]`  
**Output**: `22`  
**Explanation**:  
We can divide the players into teams as follows:
- Team 1: Players with skills 1 and 5 → Chemistry = 1 × 5 = 5
- Team 2: Players with skills 2 and 4 → Chemistry = 2 × 4 = 8
- Team 3: Players with skills 3 and 3 → Chemistry = 3 × 3 = 9  
The total chemistry of all teams is `5 + 8 + 9 = 22`.

#### Example 2:
**Input**: `skill = [3, 4]`  
**Output**: `12`  
**Explanation**:  
We have only two players, so they form one team:  
- Team 1: Players with skills 3 and 4 → Chemistry = 3 × 4 = 12

#### Example 3:
**Input**: `skill = [1, 1, 2, 3]`  
**Output**: `-1`  
**Explanation**:  
It’s impossible to divide the players into teams with equal total skill. No valid team formation can be created.

---

### A Historical Connection: Gauss’ Clever Trick 🧠✨

This problem of dividing players into teams based on skill sums is reminiscent of a story from Carl Friedrich Gauss' childhood. When Gauss was a young student, his teacher asked the class to sum all the integers from 1 to 100, expecting it to be a time-consuming task. Instead, Gauss quickly found the answer by pairing the smallest and largest numbers:

\[
1 + 100, \, 2 + 99, \, 3 + 98, \dots, 50 + 51
\]

He realized that each pair had the same sum, 101. There were 50 pairs, so the total sum was simply:

\[
50 \times 101 = 5050
\]

Just like how Gauss paired numbers to find the sum, we’re pairing players in this problem to ensure that the total skill of each pair is the same! By sorting the array and pairing the smallest skill with the largest, we follow a similar logic to solve the problem efficiently.

---

### Basic Solution 💡

#### Approach:
To solve this problem, let's think through a few key points:
1. **Even number of players**: Since each team consists of two players, the array length must be even. If it’s odd, return `-1` right away.
2. **Sorting the array**: Sorting helps us pair the weakest and strongest players, which will give us a structured way to check if all pairs have the same total skill.
3. **Pairing players**: Once sorted, we will pair the smallest element with the largest, the second smallest with the second largest, and so on. If the sum of the two skills in any pair differs from the first pair, we return `-1`.
4. **Calculate chemistry**: For each valid pair, calculate their chemistry (product of their skills) and sum it up.

#### Python Code 🐍:
```python
class Solution:
    def dividePlayers(self, skill: List[int]) -> int:
        # If the number of players is odd, we can't form pairs
        if len(skill) % 2 != 0:
            return -1
        
        # Sort the skills in ascending order
        skill = sorted(skill)

        # Calculate the sum of the first and last player in the sorted array
        first_pair = skill[0] + skill[-1]
        chemistry = skill[0] * skill[-1]

        # Loop through the rest of the players and check if the sum is consistent
        for i in range(1, len(skill) // 2):
            if skill[i] + skill[-1 - i] != first_pair:
                return -1  # If any pair sum is different, return -1
            chemistry += skill[i] * skill[-1 - i]

        return chemistry
```

#### Time Complexity ⏱️:
- **Sorting** the array takes **O(n log n)**.
- **Looping** through the pairs takes **O(n/2)**, which simplifies to **O(n)**.
- Overall, the time complexity is **O(n log n)** due to sorting.

---

### Optimized Solution 🔄

In this case, the solution provided is already optimized. Sorting is necessary because it gives us a way to compare pairs of players consistently. After sorting, the process of checking and calculating chemistry is linear, so there isn’t much room for further optimization.

---

### Conclusion ✨

This problem highlights how sorting can simplify pairwise comparison problems. By sorting the array, we can easily check if all pairs of players have equal total skill. Once we confirm that the pairs are valid, calculating the total chemistry is straightforward. This method ensures we solve the problem efficiently with a time complexity of **O(n log n)**.

Just like Gauss' trick of pairing numbers to simplify the sum of a series, we apply the same concept to pair players by skill level, making the problem much easier to solve. Sometimes, solving a complex problem just takes looking at it from the right angle!
