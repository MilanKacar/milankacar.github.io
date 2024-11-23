---
layout: post  
title: "#44 670. Maximum Swap 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Math, Greedy]
---



Ever wonder how swapping two digits can make a huge difference? Here's how to do it efficiently! 

### Problem Statement
You are given an integer `num`. You can swap two digits at most once to get the maximum valued number. Your goal is to find the highest number possible by making at most one swap.

### Examples

#### Example 1
**Input:** `num = 2736`  
**Output:** `7236`  
**Explanation:** By swapping `2` and `7`, you get `7236`, the largest possible value.

#### Example 2
**Input:** `num = 9973`  
**Output:** `9973`  
**Explanation:** No swap is necessary since the number is already the largest possible.

### Constraints:
- `0 <= num <= 10^8`  
- You can perform at most **one** swap of two digits.

---

### Short Introduction 💡
In this problem, we are tasked with maximizing a number by swapping any two digits, but we are limited to a **single swap**. The challenge lies in finding the optimal swap that gives the highest value. To solve it efficiently, we must ensure that the swap improves the value while minimizing unnecessary steps.

---

### Basic Solution 💡
The brute-force approach is to generate all possible numbers by swapping every pair of digits and then picking the maximum number. This works but is inefficient since it involves a lot of unnecessary swaps. Let's break down the brute-force approach.

#### Approach:
1. Convert the number to a string or list of digits.
2. For every digit, try swapping it with every other digit and calculate the resulting number.
3. Keep track of the maximum number obtained from all possible swaps.

#### Python Code 💻

```python
def maximumSwapBruteForce(num: int) -> int:
    num_str = list(str(num))
    max_num = num
    n = len(num_str)
    
    for i in range(n):
        for j in range(i + 1, n):
            # Swap digits at position i and j
            num_str[i], num_str[j] = num_str[j], num_str[i]
            # Convert back to integer and compare with max_num
            new_num = int("".join(num_str))
            max_num = max(max_num, new_num)
            # Swap back to restore original
            num_str[i], num_str[j] = num_str[j], num_str[i]
    
    return max_num
```

#### Time Complexity ⏳
- This brute-force solution has a time complexity of **O(n²)**, where `n` is the number of digits. This is because we are swapping every possible pair of digits, which results in a quadratic number of operations.

---

### Optimized Solution ⚡
Now, let's optimize this. Instead of trying every possible swap, we can narrow down the decision to one clever swap by observing the digits and their positions. The idea is to:
1. Traverse the digits and keep track of the **last occurrence** of each digit (from `0` to `9`).
2. For each digit, look for a larger digit to the right that can be swapped to maximize the number.
3. If a swap can be made, it’s guaranteed to give the maximum number.

#### Steps:
- Convert the number into a list of digits.
- Use a dictionary to store the last position of each digit (so we can easily look up and swap with the highest possible digit).
- For each digit, check if there is a larger digit that appears later in the number.
- Perform the swap if it improves the value of the number, and return the result.

#### Python Code 💻

```python
def maximumSwap(num: int) -> int:
    num_str = list(str(num))  # Convert num to a list of digits
    last = {int(digit): i for i, digit in enumerate(num_str)}  # Store the last occurrence of each digit
    
    # Traverse each digit
    for i, digit in enumerate(num_str):
        # Try to find a larger digit from 9 to current digit+1 that occurs later
        for d in range(9, int(digit), -1):
            if last.get(d, -1) > i:
                # Swap the digits
                num_str[i], num_str[last[d]] = num_str[last[d]], num_str[i]
                return int("".join(num_str))  # Return the maximum number obtained
    
    return num  # If no swap was made, return the original number
```

#### Time Complexity ⏳
- The time complexity of this solution is **O(n)**, where `n` is the number of digits in the number. We perform a single scan of the digits to build the `last` dictionary, and another scan to determine if and where to swap. Thus, it’s linear in time and much faster than the brute-force solution.

---

### Example Walkthrough 🧩

Let’s walk through an example step by step to understand how the optimized solution works.

#### Input: `2736`
1. Convert `2736` into a list: `['2', '7', '3', '6']`.
2. Track the last occurrence of each digit: `{2: 0, 7: 1, 3: 2, 6: 3}`.
3. Start from the first digit (`2`):
   - Can we find a larger digit to the right? Yes, `7` is larger and occurs at position 1.
   - Swap `2` and `7`.
4. The number becomes `['7', '2', '3', '6']`, or `7236`.

#### Result: `7236`

---

### Conclusion 📝

In summary, the brute-force approach to this problem is simple but inefficient with a time complexity of **O(n²)**. The optimized approach, by using a dictionary to track the last occurrence of digits, reduces the time complexity to **O(n)**. This ensures that we solve the problem efficiently, even for larger numbers, while still finding the maximum possible value with just one swap.

The optimized solution provides a clear improvement and shows the power of analyzing patterns in the input rather than blindly trying every possibility.
