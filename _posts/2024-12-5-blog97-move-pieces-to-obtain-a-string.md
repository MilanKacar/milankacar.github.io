---
layout: post  
title: "#97 🧩 2337. Move Pieces to Obtain a String 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Two Pointers, String]
---

Transforming one string into another through clever moves is a fascinating challenge. In this problem, we deal with moving characters `L` and `R` on a grid filled with blanks `_` while adhering to strict movement rules. Can we turn the string `start` into `target`? Let’s find out! 🚀

---

### 📜 **Problem Statement**

You are given two strings, `start` and `target`, each of length `n`. Both strings consist of the characters `L`, `R`, and `_`:

- `L` pieces can move **left** into an adjacent blank (`_`).
- `R` pieces can move **right** into an adjacent blank (`_`).
- `_` represents a blank space, and it can be occupied by any piece (`L` or `R`).

Your task is to determine if it is possible to transform the string `start` into the string `target` by moving the pieces any number of times.

---

### 🔍 **Examples**

#### Example 1:
**Input:**  
`start = "_L__R__R_"`  
`target = "L______RR"`  

**Output:**  
`true`  

**Explanation:**  
1. Move the first `L` one step left: `_L__R__R_` → `L___R__R_`.  
2. Move the last `R` one step right: `L___R__R_` → `L___R___R`.  
3. Move the middle `R` three steps right: `L___R___R` → `L______RR`.  

We can achieve `target`, so we return `true`.

---

#### Example 2:
**Input:**  
`start = "R_L_"`  
`target = "__LR"`  

**Output:**  
`false`  

**Explanation:**  
1. Move the `R` one step right: `R_L_` → `_RL_`.  
2. The `L` cannot move right into `R`, so we can't match `target`.  

---

#### Example 3:
**Input:**  
`start = "_R"`  
`target = "R_"`  

**Output:**  
`false`  

**Explanation:**  
The `R` can only move to the right, so it cannot match the position in `target`.

---

### 🛠️ **Constraints**

- `n == start.length == target.length`
- \(1 \leq n \leq 10^5\)
- `start` and `target` consist only of `L`, `R`, and `_`.

---

### 💡 **Key Observations**

1. **Matching Positions:**  
   The relative order of the pieces `L` and `R` must remain the same in both strings. We cannot "swap" the positions of these characters during any moves.

2. **Blank Spaces (`_`):**  
   Blanks are flexible placeholders. They allow pieces to move but do not affect the order of pieces.

3. **L and R Rules:**  
   - An `L` can only move left, so it **cannot move right** or end up to the right of its original position.
   - Similarly, an `R` can only move right and cannot end up to the left of its original position.

4. **Ignoring `_` Positions:**  
   Since `_` doesn't determine the order of `L` and `R`, we can filter them out from both strings when comparing.

---

### ✨ **Plan**

To solve the problem efficiently and correctly, we need a structured plan that breaks down the requirements into logical steps. Here's an in-depth explanation of each step:

---

#### **Step 1: Filter Relevant Characters**
The strings `start` and `target` contain the characters `L`, `R`, and `_`. The `_` represents blank spaces, which act as placeholders for movements but do not affect the order or identity of the pieces.  

Our first task is to remove all `_` characters from both strings, focusing only on the sequence of `L` and `R` pieces.  
For example:  
- `start = "_L__R__R_"` → Filter → `start_pieces = ['L', 'R', 'R']`
- `target = "L______RR"` → Filter → `target_pieces = ['L', 'R', 'R']`

If the two filtered sequences (`start_pieces` and `target_pieces`) are not identical in terms of characters and their order, it is **impossible** to transform `start` into `target`, and we can immediately return `false`.

---

#### **Step 2: Record Character Positions**
While the filtered sequences of characters (`L` and `R`) determine the order, their positions in the original strings determine whether the transformation is possible under the movement rules.  

To retain the positional information, we pair each character with its index.  
For example:  
- `start = "_L__R__R_"` → `start_positions = [('L', 1), ('R', 4), ('R', 7)]`  
- `target = "L______RR"` → `target_positions = [('L', 0), ('R', 6), ('R', 7)]`

---

#### **Step 3: Validate Movement Rules**
Next, we compare the corresponding characters and their positions in `start_positions` and `target_positions`. The movement rules are as follows:  
1. **For `L` Pieces:**  
   - `L` can only move **left** or stay in the same position.
   - If an `L` appears at a higher index in `start_positions` compared to `target_positions`, the transformation is invalid.  

2. **For `R` Pieces:**  
   - `R` can only move **right** or stay in the same position.
   - If an `R` appears at a lower index in `start_positions` compared to `target_positions`, the transformation is invalid.  

For example:  
- Start: `start_positions = [('L', 1), ('R', 4), ('R', 7)]`  
- Target: `target_positions = [('L', 0), ('R', 6), ('R', 7)]`  
  - `'L', 1` → `'L', 0`: ✅ Valid, as `L` moves left.  
  - `'R', 4` → `'R', 6`: ✅ Valid, as `R` moves right.  
  - `'R', 7` → `'R', 7`: ✅ Valid, no movement required.

---

#### **Step 4: Early Termination**
To make the solution more efficient, we implement early termination checks:  
- **Mismatched Sequences:** If the filtered sequences of `L` and `R` are not identical between `start` and `target`, return `false` immediately.  
- **Invalid Movement:** As we iterate over the positions, if any piece violates its movement rule (`L` moves right or `R` moves left), we can stop and return `false`.  

---

#### **Step 5: Edge Cases**
Before finalizing the implementation, consider these edge cases:
1. **No Pieces (`L` or `R`) in the Strings:**  
   If both `start` and `target` consist only of `_`, the result is always `true` because no movement is needed.

2. **Unequal Length of Filtered Sequences:**  
   If the count of `L` and `R` pieces differs between `start` and `target`, it is impossible to transform the strings.

3. **Single Character Scenarios:**  
   Handle edge cases where `start` or `target` contains only one `L` or `R`.  
   For example:  
   - `start = "_L_"`, `target = "L__"` → Valid (L moves left).  
   - `start = "_L_"`, `target = "__L"` → Invalid (L cannot move right).

---

#### **Step 6: Iterate and Compare**
After filtering and validating, iterate over the corresponding pairs of characters and their positions from `start_positions` and `target_positions`. Validate the movement of each character based on its rules (`L` left, `R` right). If all pairs satisfy the movement constraints, return `true`. Otherwise, return `false`.

This step-by-step approach ensures that we handle all scenarios while maintaining efficiency. By breaking the problem into smaller tasks, we simplify the logic and make the solution scalable for large inputs.

---

### 🧠 **Solution**

#### Python Code

```python
def canTransform(start: str, target: str) -> bool:
    # Remove blanks to compare character order
    start_pieces = [(char, i) for i, char in enumerate(start) if char != '_']
    target_pieces = [(char, i) for i, char in enumerate(target) if char != '_']
    
    # Step 1: Ensure the pieces match
    if len(start_pieces) != len(target_pieces):
        return False
    
    for (start_char, start_pos), (target_char, target_pos) in zip(start_pieces, target_pieces):
        # Pieces must have the same character and satisfy movement rules
        if start_char != target_char:
            return False
        if start_char == 'L' and start_pos < target_pos:
            return False  # L can only move left
        if start_char == 'R' and start_pos > target_pos:
            return False  # R can only move right
    
    return True
```

---

### ⏱ **Time Complexity**

- **Filtering Pieces:** \(O(n)\), where \(n\) is the length of the string.
- **Comparing Pieces:** \(O(n)\) in the worst case when all characters are non-blanks.

Overall complexity: **\(O(n)\)**.  
Space complexity: **\(O(n)\)** for storing non-blank positions.

---

### 🛠️ **Example Walkthrough**

Let’s go step-by-step with the example:

**Input:**  
`start = "R__L__L_"`  
`target = "__RLL___"`

**Step 1: Extract Pieces**  
- `start_pieces = [('R', 0), ('L', 3), ('L', 6)]`  
- `target_pieces = [('R', 2), ('L', 3), ('L', 4)]`  

**Step 2: Validate Character Order**  
- Both sequences contain `['R', 'L', 'L']`. ✅  

**Step 3: Validate Movement Rules**  
- `('R', 0)` → `('R', 2)`: ✅ (Right movement is allowed.)  
- `('L', 3)` → `('L', 3)`: ✅ (No movement needed.)  
- `('L', 6)` → `('L', 4)`: ❌ (Cannot move `L` to the right.)  

**Output:**  
`false`

---

### 🧐 **Edge Cases**

1. **All Blanks:**  
   `start = "_____", target = "_____"`  
   **Output:** `true`  

2. **Empty Movements:**  
   `start = "L__R", target = "L__R"`  
   **Output:** `true`  

3. **Impossible Transformation:**  
   `start = "L_R", target = "R_L"`  
   **Output:** `false`  

4. **Mismatched Characters:**  
   `start = "L_R", target = "R_R"`  
   **Output:** `false`  

---

### 🏆 **Conclusion**

This problem emphasizes **character order** and **movement constraints**. By filtering out blanks and verifying movement feasibility, we efficiently determine the transformation's possibility. The linear complexity ensures scalability for larger inputs. Happy coding! 😊
