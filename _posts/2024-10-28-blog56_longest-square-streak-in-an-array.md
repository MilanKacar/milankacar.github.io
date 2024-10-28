---
layout: post
title: "#56 2501. Longest Square Streak in an Array 🧠🚀"
categories: [LeetCode, Programming]
---



In this problem, we’re hunting for streaks of squares within a sequence of numbers! Imagine a magical sequence where each number is the square of the one before it—our task is to find the **longest such sequence** (or confirm if no such streak exists). Let’s dive into the details and explore a step-by-step approach to tackle it! 🧩

---

### 📝 Problem Statement
You are given an integer array `nums`. A subsequence of `nums` is called a *square streak* if:
1. The length of the subsequence is at least 2, and
2. After sorting the subsequence, each element (except the first one) is the square of the previous number.

Return the **length of the longest square streak** in `nums`, or return `-1` if no square streak exists.

A *subsequence* is an array derived from another array by deleting some or no elements without changing the order of the remaining elements.

---

### 📚 Examples

#### Example 1
- **Input**: `nums = [4,3,6,16,8,2]`
- **Output**: `3`
- **Explanation**:
  - One possible subsequence: `[4,16,2]`
  - After sorting, it becomes `[2,4,16]` with:
    - `4 = 2 * 2`
    - `16 = 4 * 4`
  - Therefore, `[4,16,2]` forms a square streak of length `3`.

#### Example 2
- **Input**: `nums = [2,3,5,6,7]`
- **Output**: `-1`
- **Explanation**: No square streak exists in `nums`.

---

### 🧩 Approach

**Goal**: Identify the longest square streak in the array `nums`, where each element (except the first) is the square of its predecessor after sorting.

### 🛠️ Solution Plan

Given the constraints, we need an efficient way to check if elements follow a square-streak pattern. Here’s a structured plan:

1. **Sort the Input Array**: Sorting simplifies finding square patterns in subsequences.
2. **Use a Set for Fast Lookups**: Converting `nums` to a set allows quick checks to verify if a square (or root) of a number exists in `nums`.
3. **Track Longest Streak**: For each number, check if it starts a square streak, and if so, count its length. Track the longest streak found.

This strategy balances efficiency with readability, making the problem manageable even for larger input sizes. Let’s look at our basic solution in action. 🌟

---

### 💡 Basic Solution

Our basic solution follows the approach above, using sorting and a set for fast square checks. We iterate over each element to try forming a square streak and keep track of the longest one.

#### **Python Code**

```python
def longestSquareStreak(nums):
    nums.sort()  # Sort nums for easier handling of squares
    num_set = set(nums)  # Convert to set for quick existence check
    max_streak = -1  # Initialize max_streak as -1 (in case no streak is found)

    for num in nums:
        current_streak = 1  # Start with the current number
        current_num = num
        
        # Check if square exists in num_set
        while current_num * current_num in num_set:
            current_num *= current_num  # Move to next square
            current_streak += 1  # Increase streak length

        # Update max_streak if current_streak is longest so far
        if current_streak >= 2:
            max_streak = max(max_streak, current_streak)
    
    return max_streak
```

#### **Time Complexity**

- **Sorting**: \(O(n \log n)\)
- **Square Checking Loop**: In the worst case, we might check squares repeatedly for every number, which could approach \(O(n)\) within the constraints.
- **Overall Complexity**: \(O(n \log n + n)\), which is efficient given our constraints.

---

### 🏎 Optimized Solution

In this case, our basic solution is already optimized, using a set and sorting to keep operations manageable. Therefore, we’ll proceed with the same logic in our optimized solution.

### 🧩 Example Walkthrough with Higher Numbers

Let’s use a larger, detailed example to see how the function finds a square streak:

**Input**: `nums = [9, 81, 6561, 3, 243, 27, 729, 2187]`

#### Step-by-Step Execution
1. **Sort `nums`**: `[3, 9, 27, 81, 243, 729, 2187, 6561]`
2. **Convert to Set** for quick access: `{3, 9, 27, 81, 243, 729, 2187, 6561}`
3. **Initialize Variables**:
   - `max_streak = -1`
   - Iterate over each element in sorted order.

#### Walkthrough of Each Element
- **Starting with `3`**:
  - **Current Streak = 1** (starts with `3`)
  - Check if `3 * 3 = 9` exists → Yes.
  - **Current Streak = 2** (now includes `9`)
  - Check if `9 * 9 = 81` exists → Yes.
  - **Current Streak = 3** (now includes `81`)
  - Check if `81 * 81 = 6561` exists → Yes.
  - **Current Streak = 4** (now includes `6561`)
  - No further squares exist; end streak.
  - **Update max_streak**: `max_streak = 4`.

- **Continue with Remaining Elements**: Checking `9`, `27`, etc., yields shorter streaks, so `max_streak` remains `4`.

**Output**: `4` (Longest square streak is `[3, 9, 81, 6561]`).

---

### 📊 Conclusion

In this problem, the set and sorting approach lets us efficiently check square sequences without unnecessary recalculations, achieving an optimal solution with minimal complexity. Here’s a recap of the strategy:

1. **Sort and Set for Fast Lookup**: Sorting and using a set minimizes computation.
2. **Iterative Square Checks**: For each number, we form and check streaks using square relationships.
3. **Track and Update Longest Streak**: Keeping a `max_streak` ensures we get the longest square streak.

---

### 🎉 Final Thoughts
This problem elegantly combines **sequence analysis** with **efficient data structures**. From sorting to utilizing sets, we leverage multiple techniques to handle larger inputs effectively. With some persistence and logical checks, square streaks reveal themselves, and you’re on your way to the longest streak! Happy coding, and may your algorithms be efficient and streaks be long! 🌟
