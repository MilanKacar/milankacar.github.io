---
layout: post
title: "#27 1963. Minimum Number of Swaps to Make the String Balanced 🧠🚀"
categories: [LeetCode, Programming]
---

Balancing brackets is a common problem in programming interviews, and in this challenge, we are tasked with making a string of brackets balanced with the minimum number of swaps. Given that the string has an equal number of opening (`[`) and closing (`]`) brackets, the goal is to determine the least number of swaps required to make it balanced.

---

### Problem Statement 📄

You are given a 0-indexed string `s` of even length `n`. The string contains exactly `n / 2` opening brackets `[` and `n / 2` closing brackets `]`. A string is considered *balanced* if it follows these rules:
1. It is an empty string, or
2. It can be written as `AB` where both `A` and `B` are balanced strings, or
3. It can be written as `[C]` where `C` is a balanced string.

You may swap the brackets at any two indices any number of times. Return the **minimum number of swaps** needed to make `s` balanced.

---

### Example 1 🔢

**Input**: `s = "][]["`  
**Output**: `1`  
**Explanation**:  
Swapping index `0` with index `3` results in the balanced string `"[[]]"`.

---

### Example 2 🔢

**Input**: `s = "]]][[["`  
**Output**: `2`  
**Explanation**:  
- Swap index `0` with index `4`: `[]][][`.
- Swap index `1` with index `5`: `[[][]]`.  
The resulting string is balanced.

---

### Example 3 🔢

**Input**: `s = "[]"`  
**Output**: `0`  
**Explanation**:  
The string is already balanced.

---

### Solution 💡

To solve this problem optimally, we need to account for the *balance* between opening and closing brackets while traversing the string. Whenever we encounter more closing brackets (`]`) than opening ones (`[`), we incur an *imbalance*. The key idea is to track this imbalance and resolve it by counting how many swaps are needed.

The process is as follows:
1. **Imbalance**: Track how many closing brackets exceed the opening ones at any point in the string.
2. **Max Imbalance**: Keep track of the highest imbalance encountered. This helps determine the minimum swaps required.
3. **Final Calculation**: Each swap can resolve two unbalanced brackets, so the number of swaps needed is half of the maximum imbalance (rounded up).

### Python Code 🐍

```python
def minSwaps(s: str) -> int:
    imbalance = 0  # Track the number of unbalanced closing brackets
    max_imbalance = 0  # Track the maximum imbalance encountered
    
    for char in s:
        if char == '[':
            imbalance += 1  # Opening bracket adds balance
        else:
            imbalance -= 1  # Closing bracket reduces balance
        
        # If imbalance goes negative, track the maximum imbalance needed
        max_imbalance = max(max_imbalance, -imbalance)
    
    # Each swap fixes two unbalanced brackets
    return (max_imbalance + 1) // 2
```

---

### Time Complexity ⏳

- **Time Complexity**: `O(n)`, where `n` is the length of the string. We iterate through the string once, keeping track of the imbalance.
- **Space Complexity**: `O(1)`, since we only use a few extra variables.

---

### Example Walkthrough 🔍

For the string `"]]][[[ "`:

- Traverse the string:
  - `]`: imbalance = -1, max imbalance = 1
  - `]`: imbalance = -2, max imbalance = 2
  - `]`: imbalance = -3, max imbalance = 3
  - `[`: imbalance = -2
  - `[`: imbalance = -1
  - `[`: imbalance = 0

The maximum imbalance was `3`, so we need `ceil(3/2) = 2` swaps to balance the string.

---

### Conclusion 🎯

By carefully tracking the imbalance between opening and closing brackets, we can efficiently calculate the minimum number of swaps required to make the string balanced. The key insight is that each swap resolves two unbalanced brackets, allowing us to minimize the total number of swaps. This approach runs in linear time, making it optimal even for large inputs. Keep practicing, and you'll be a pro at balancing brackets! 😎

