---
layout: post
title: "#65 🧩 2914. Minimum Number of Changes to Make Binary String Beautiful 🧠🚀"
categories: [LeetCode, Programming]
---


Binary strings are at the core of many computer science problems. Today, we’re going to dive into transforming a binary string into a beautiful one. What does it mean to make a binary string “beautiful”? Here, it’s all about structuring the string so that it can be split into even-length sections containing only `0`s or only `1`s. This problem is all about finding the **minimum number of changes required** to achieve that “beautiful” transformation.

In this blog post, we’ll:
- Cover the problem statement and requirements in detail.
- Go through examples to solidify our understanding.
- Explore the most efficient solution using Python.
- Discuss complexities, edge cases, and performance considerations.
- Walk through code with detailed explanations.

And we’ll sprinkle plenty of emojis throughout to keep things fun! 😄 Let’s get into it!

---

## Problem Statement 📜

You are given a binary string `s` (containing only `0`s and `1`s) with an even length. A binary string is “**beautiful**” if it can be divided into one or more substrings where:
1. Each substring has an even length.
2. Each substring contains either only `1`s or only `0`s.

**Goal**: You can change any character in the string `s` to `0` or `1`. The task is to determine the **minimum number of changes** required to make the string beautiful.

---

### Understanding the Requirements 🤔

This problem involves working with binary strings of even lengths, and we want to transform them to meet the criteria for “beauty.” To understand how we can achieve this, let’s break down the requirements:

1. **Partition into Uniform Blocks**: The key to solving this problem is to break down the string into **blocks of two characters each**. If each block has identical characters (like `"00"` or `"11"`), it can contribute to making the string beautiful.
  
2. **Count Changes**: For each block that doesn’t meet the requirement, we’ll count it as one change needed to make it uniform.

3. **Efficiency**: Since the maximum length of `s` could be up to 100,000, we need to ensure that our solution processes the string in a way that keeps it within a linear time complexity, i.e., $$O(n)$$.

Let’s go through some examples to see how this works in practice. 💡

---

### Examples

#### Example 1: Transforming the String

**Input**: `s = "1001"`

- We want the string to be partitioned into sections like `"11|00"` or `"00|11"`.
- **Changes**: By changing `s[1]` to `1` and `s[3]` to `0`, we can achieve `"1100"`.
- **Beautiful Partitioning**: Now, we can split `"1100"` into `"11|00"`, both uniform and of even length.

**Output**: `2`

#### Example 2: Minimum Changes for Uniformity

**Input**: `s = "10"`

- **Changes**: Changing `s[1]` to `1` gives us `"11"`, which is now uniform.
- **Beautiful Partitioning**: `"11"` is already beautiful since it’s uniform and has even length.

**Output**: `1`

#### Example 3: Already Beautiful

**Input**: `s = "0000"`

- **Beautiful Partitioning**: `"0000"` can be divided as `"00|00"`, satisfying the conditions already.
- **Changes**: No changes are needed.

**Output**: `0`

---

## Strategy for the Solution 🧩

To solve this problem efficiently, we need a strategy that ensures each block of two characters in `s` is either `"00"` or `"11"`. Here’s the plan:

1. **Iterate through the String in Pairs**: We’ll loop through `s` in steps of 2. This allows us to work with blocks like `"10"`, `"11"`, or `"01"` directly.
2. **Count Mismatches**:
   - If a block has differing characters, we increment a counter for needed changes.
   - Otherwise, if a block is already uniform, we skip it.
3. **Return Total Changes**: By the end of the loop, our counter will tell us the minimum changes needed to make `s` beautiful.

### Why This Approach Works Optimally

This solution is efficient because:
- It leverages the **block structure** of the problem to check only pairs of characters, keeping the solution in $$O(n)$$ time complexity.
- We don’t need any extra storage beyond a simple counter, so the **space complexity is $$O(1)$$**.

Now that we understand the approach, let’s look at the Python code to implement this solution. 🐍

---

## Solution Code 🔍

Here's the complete Python solution using the approach outlined above.

```python
def minChangesToMakeBeautiful(s: str) -> int:
    n = len(s)
    changes = 0
    
    # Process the string in pairs of two characters
    for i in range(0, n, 2):
        # Check if the characters in the pair are different
        if s[i] != s[i + 1]:
            changes += 1  # Increment changes for each mismatched pair
    
    return changes
```

### How the Code Works

1. **Loop through the String in Pairs**: Using `range(0, n, 2)`, we iterate through `s` in steps of two characters.
2. **Condition Check for Changes**: Inside the loop, we check if `s[i]` and `s[i+1]` differ. If they do, we increment `changes` by one.
3. **Return Result**: After processing all pairs, the value of `changes` represents the minimum changes needed to make the string beautiful.

---

## Complexity Analysis 📊

- **Time Complexity**: $$O(n)$$, where $$n$$ is the length of the string. We’re iterating through each character pair only once.
- **Space Complexity**: $$O(1)$$, since only a constant amount of space is used for the `changes` variable.

---

## Example Walkthrough with Code Execution 🖥️

Let's apply our code to an example and break down each step.

### Example: `s = "101010"`

**Step-by-Step Execution**:

1. **Initial String**: `"101010"`
2. **Pairs to Process**: `("10", "10", "10")`
   
   - **First Pair**: `"10"` → characters differ, so `changes = 1`.
   - **Second Pair**: `"10"` → characters differ, so `changes = 2`.
   - **Third Pair**: `"10"` → characters differ, so `changes = 3`.

3. **Final Count of Changes**: `3`

**Output**: `3`

This result matches our expectation based on the example.

---

## Edge Cases 🌐

Here are some interesting edge cases to consider:

1. **Already Beautiful Strings**:
   - Example: `s = "1111"` or `s = "0000"`
   - Expected Output: `0` changes since these strings already meet the criteria.

2. **Alternating Patterns**:
   - Example: `s = "1010"`
   - Expected Output: `2` since each pair needs a change.

3. **Minimal Length Strings**:
   - Example: `s = "01"`
   - Expected Output: `1` as it takes one change to make it uniform.

4. **Long Uniform Strings**:
   - Example: `s = "11111111"` or `s = "00000000"`
   - Expected Output: `0`, as these strings are already beautiful.

---

## Additional Considerations

### Potential Variations of the Problem 🌀

1. **Odd Lengths**: If the length of `s` were odd, it would not be possible to divide it into uniform pairs without at least one leftover character. The problem’s constraint ensures that `s` has an even length, simplifying our solution.
   
2. **Arbitrary Group Sizes**: If the problem required larger uniform groups, like blocks of four identical characters, we would need to adjust our iteration steps and potentially calculate more changes.

### Real-World Applications 🌍

1. **Data Transmission**: Binary strings in network data often require certain structures to improve readability or consistency. For example, error-correcting codes ensure data integrity by making binary strings uniform in specific patterns.
  
2. **Data Compression**: Making data “beautiful” can help in compression techniques where patterns are replaced with shorter representations. By ensuring uniformity, it may simplify compression algorithms.

---

## Conclusion 🎉

We covered a detailed approach to solving the “Minimum Number of Changes to Make Binary String Beautiful” problem. Here’s a recap of our steps:

1. **Divide the string into pairs** and check each for uniformity.
2. **Count mismatched pairs** as they represent the minimum changes needed.
3. Return the total count, ensuring our solution is both **time and space efficient**.

This approach not only gives an efficient solution but also handles large strings within optimal time limits. By covering examples, edge cases, and detailed code explanations, we hope you’re now confident with this problem.
