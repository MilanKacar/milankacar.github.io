---
layout: post  
title: "#96 📝 2825. Make String a Subsequence Using Cyclic Increments 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Two Pointers, String]
---

When you're handed a couple of strings and a bit of magic in the form of cyclic increments, **can you make one a subsequence of the other?** This problem combines creativity, pointer tricks, and some smart thinking, and it’s bound to test your problem-solving skills. Let’s break it down in this in-depth guide! 🎉

---

## 📝 Problem Statement

You are given two **0-indexed strings**, `str1` and `str2`.

In one operation, you can:

- **Select a set of indices** in `str1`.
- For **each index `i`** in this set, increment `str1[i]` **to the next character cyclically**:
  - `'a'` becomes `'b'`, `'b'` becomes `'c'`, ..., `'z'` becomes `'a'`.

### Your Goal:

Return `True` if it is possible to make `str2` a **subsequence** of `str1` by performing this operation **at most once**, and `False` otherwise.

---

### 🔍 Key Definitions

- A **subsequence** is a new string formed by deleting some (possibly none) characters from the original string, **without changing the relative order of the remaining characters**.

---

### 💡 Examples to Warm Up

#### Example 1:
**Input:**
```plaintext
str1 = "abc", str2 = "ad"
```

**Output:**
```plaintext
True
```

**Explanation:**  
We can increment the character at index 2 of `str1` (`'c'` → `'d'`), making `str1` → `"abd"`.  
Now, `str2 = "ad"` is a subsequence of `"abd"`.

---

#### Example 2:
**Input:**
```plaintext
str1 = "zc", str2 = "ad"
```

**Output:**
```plaintext
True
```

**Explanation:**  
Increment both indices:
- `'z'` → `'a'`.
- `'c'` → `'d'`.  

Result: `str1` becomes `"ad"`, and `str2` = `"ad"` is a subsequence.

---

#### Example 3:
**Input:**
```plaintext
str1 = "ab", str2 = "d"
```

**Output:**
```plaintext
False
```

**Explanation:**  
Even with one cyclic increment operation, `str2` cannot be formed as a subsequence of `str1`.

---

### 🔐 Constraints
- `1 <= str1.length <= 10⁵`
- `1 <= str2.length <= 10⁵`
- `str1` and `str2` consist of only lowercase English letters.

---

## 🌟 Solution Walkthrough  

This problem is **pointer-friendly**! We’ll use the **two-pointer technique** to traverse both strings efficiently. Here’s the thought process:

### 🔧 Observations:
1. **Cyclic Increment:**  
   A character in `str1` can cyclically increment to **exactly one character** (`'z' → 'a'` wraps back).
   
2. **Subsequence Check:**  
   If you’re iterating through `str2` and can match its characters (directly or after cyclic increment) sequentially in `str1`, the task is successful!

3. **Efficiency Requirements:**  
   - The solution must process strings in **linear time**, i.e., `O(n + m)` where `n = len(str1)` and `m = len(str2)`.

---

### 🧠 Plan

The plan for solving this problem focuses on **efficiency and simplicity** using the two-pointer approach. Let’s expand on each part of the plan, diving deeper into the logic and ensuring you understand **why** each step is necessary. 🧐

---

### Step 1: Understand the Role of Subsequence Matching  
To check if `str2` is a subsequence of `str1`, we need to **preserve the relative order** of characters in `str2` as they appear in `str1`.  

This means:  
- We don’t need an **exact match** for all characters.  
- Instead, we need to match the characters of `str2` **sequentially** by scanning through `str1`.  
- If `str2[j]` matches either:
  - `str1[i]` (direct match), or  
  - `str1[i]` after a cyclic increment (transformed match),  
  we move to the next character in `str2`.

---

### Step 2: Use Two Pointers for Sequential Matching  

We use **two pointers** to iterate through `str1` and `str2` simultaneously:  
1. Pointer `i`: Traverses `str1` from left to right.  
2. Pointer `j`: Tracks the position in `str2` that needs to be matched.

---

### Step 3: Match Conditions  

At every step, check whether the current character in `str1` can help match the current character in `str2`:  
1. **Direct Match:**  
   If `str1[i] == str2[j]`, we have a match. Move both pointers (`i` and `j`) to continue.  

2. **Cyclic Increment Match:**  
   If cyclically incrementing `str1[i]` produces `str2[j]`, treat it as a match.  
   - Calculate the cyclic increment transformation:
     ```plaintext
     next_char = chr((ord(str1[i]) - ord('a') + 1) % 26 + ord('a'))
     ```
   - If `next_char == str2[j]`, it’s a match. Move both pointers.  

3. **No Match:**  
   If neither condition is satisfied, skip `str1[i]` by incrementing `i` only. This lets us search for potential matches further along in `str1`.

---

### Step 4: Completion Criteria  

Once we’ve processed all characters in `str2`, the task is complete:  
- If pointer `j` reaches the end of `str2` (`j == len(str2)`), return `True`. This means all characters in `str2` were successfully matched in `str1`.  
- If pointer `i` reaches the end of `str1` before `j`, return `False`. This means not all characters in `str2` could be matched.

---

### Step 5: Edge Case Handling  

The two-pointer method ensures we efficiently handle most scenarios, but we must consider a few **special cases**:

1. **Small Strings:**  
   - Handle cases where `str1` or `str2` are very short (e.g., length 1).  
   - Ensure the logic works when there's just one character to match or transform.

2. **Empty `str2`:**  
   - If `str2` is empty, it is trivially a subsequence of any string. Return `True`.

3. **Identical Strings:**  
   - If `str1 == str2`, no operation is needed. Return `True`.

4. **No Possible Transformations:**  
   - If `str2` contains characters that `str1` can never produce, even after a single cyclic increment, return `False`.

5. **Upper Constraints:**  
   - With `len(str1)` and `len(str2)` both reaching `10⁵`, the algorithm must remain efficient. The linear complexity (`O(n + m)`) ensures we don’t hit performance bottlenecks.

---

This expanded plan provides a clear roadmap to solving the problem effectively. By leveraging these insights and the two-pointer technique, you can confidently handle any input, no matter how complex! 🎯

---

### 🐍 Python Code

Let’s break the solution into **logical sections** so you can understand every part of the code and how it works. The goal is not just to solve the problem but to write clean, modular, and well-documented code! 🧑‍💻✨

---

#### **Code Structure**  

```python
def canMakeSubsequence(str1: str, str2: str) -> bool:
    i, j = 0, 0  # Initialize pointers for str1 and str2
    
    while i < len(str1) and j < len(str2):  # Traverse both strings
        # Direct match
        if str1[i] == str2[j]:
            j += 1  # Move the pointer in str2 since we've matched this character
        
        # Cyclic increment match
        elif chr((ord(str1[i]) - ord('a') + 1) % 26 + ord('a')) == str2[j]:
            j += 1  # Move the pointer in str2 as the transformed character matches

        # Move pointer in str1 in all cases
        i += 1
    
    # If j reaches the end of str2, all characters were matched
    return j == len(str2)
```

---

### 🛠 Functionality of Each Part  

#### 1️⃣ **Function Signature**  
```python
def canMakeSubsequence(str1: str, str2: str) -> bool:
```
- Takes two input strings `str1` and `str2`.  
- Returns a Boolean (`True` or `False`) indicating whether `str2` can be made a subsequence of `str1` using the described operations.

---

#### 2️⃣ **Initialization of Pointers**  
```python
i, j = 0, 0
```
- `i`: Pointer for `str1`, tracking the current character in `str1` we’re evaluating.  
- `j`: Pointer for `str2`, tracking the character we need to match.  

These pointers help simulate the two-string traversal efficiently.

---

#### 3️⃣ **While Loop for Traversal**  
```python
while i < len(str1) and j < len(str2):
```
- The loop continues as long as there are characters left in `str1` and `str2` to process.  
- Once one of the pointers (`i` or `j`) exceeds its respective string length, the loop stops.

---

#### 4️⃣ **Direct Match Check**  
```python
if str1[i] == str2[j]:
    j += 1  # Move pointer in str2 as the current character matches
```
- If the character at `str1[i]` matches the character at `str2[j]`, it’s a valid match.  
- Move the `j` pointer to check the next character in `str2`.

---

#### 5️⃣ **Cyclic Increment Match Check**  
```python
elif chr((ord(str1[i]) - ord('a') + 1) % 26 + ord('a')) == str2[j]:
    j += 1  # Move pointer in str2 since the transformed character matches
```

**Cyclic Increment Logic Explained:**  
- Convert the character `str1[i]` to its ASCII code using `ord`.  
- Calculate the next character cyclically:
  ```python
  (ord(str1[i]) - ord('a') + 1) % 26
  ```
  This ensures that after `'z'`, it wraps back to `'a'`.  
- Convert the resulting value back to a character using `chr`.  
- Compare the transformed character with `str2[j]`.

**Example:**  
- If `str1[i] = 'z'` and `str2[j] = 'a'`:  
  ```python
  (ord('z') - ord('a') + 1) % 26 + ord('a') == ord('a')  # True
  ```

---

#### 6️⃣ **Move Pointer in `str1`**  
```python
i += 1
```
- Regardless of whether there was a match or not, move the pointer `i` in `str1` to evaluate the next character.

---

#### 7️⃣ **Final Check for Completion**  
```python
return j == len(str2)
```
- If the pointer `j` has reached the end of `str2`, all characters in `str2` have been matched successfully.  
- Otherwise, return `False`.

---

### 🖋️ Expanded Example Walkthrough  

Let’s debug the code with a larger example:

**Input:**  
```python
str1 = "bcdefghijklmnopqrstuvwxyza"
str2 = "bad"
```

**Step-by-Step Execution:**  
1. **Initialization:**  
   - `i = 0`, `j = 0`  
   - `str1 = "bcdefghijklmnopqrstuvwxyza"`, `str2 = "bad"`

2. **Iteration 1:**  
   - `str1[0] = 'b'`, `str2[0] = 'b'`  
   - **Direct Match:** Move `j = 1`, `i = 1`

3. **Iteration 2:**  
   - `str1[1] = 'c'`, `str2[1] = 'a'`  
   - **Cyclic Increment:** `'c' → 'a'` matches `str2[1]`.  
   - Move `j = 2`, `i = 2`

4. **Iteration 3:**  
   - `str1[2] = 'd'`, `str2[2] = 'd'`  
   - **Direct Match:** Move `j = 3`, `i = 3`

5. **End:**  
   - `j = len(str2)`.  
   - Output: `True`.

---

### 🔥 Modular Approach for Reusability  

If you want to reuse the cyclic increment functionality in other contexts, modularize it:

```python
def get_next_char(ch: str) -> str:
    """Returns the next character cyclically."""
    return chr((ord(ch) - ord('a') + 1) % 26 + ord('a'))
```

**Use in Main Code:**  
```python
elif get_next_char(str1[i]) == str2[j]:
```

This improves readability and makes the code cleaner. 🚀

---

### 📏 Edge Case Coverage  

Here’s how the code handles edge cases:  
1. **Empty `str2`:**  
   ```python
   str1 = "abc", str2 = ""
   ```
   - Output: `True` (any string is a subsequence of an empty string).  

2. **Identical Strings:**  
   ```python
   str1 = "abc", str2 = "abc"
   ```
   - Output: `True` (no transformation required).  

3. **Impossible Transformations:**  
   ```python
   str1 = "abc", str2 = "xyz"
   ```
   - Output: `False` (characters in `str2` can’t be produced by transformations).

---

### ⏱️ Time Complexity

- **Traversing `str1` and `str2`:**  
  Each pointer moves at most once through its respective string.  
  - **Time Complexity:** `O(n + m)`

- **Space Complexity:**  
  Only variables `i`, `j`, `n`, and `m` are used.  
  - **Space Complexity:** `O(1)`

---

## 🔥 Edge Cases

1. **Small Strings:**  
   - `str1 = "a"`, `str2 = "z"`: False, as no cyclic increment makes `'a'` → `'z'` in one step.
   
2. **Large Strings:**  
   - Strings at the upper constraint (`10⁵` characters) should still work efficiently due to the linear complexity.

3. **Empty `str2`:**  
   - If `str2 = ""`, output is `True` (an empty string is always a subsequence).

4. **Identical Strings:**  
   - `str1 = "abc"`, `str2 = "abc"`: True, as no operation is required.

---

### 💡 Conclusion

This problem showcases the elegance of combining cyclic transformations with the two-pointer technique. **Efficient traversal and smart matching** ensure we solve the problem in optimal time.

- Use cyclic increments wisely to simulate character transitions.
- Maintain pointers for efficient subsequence matching.

In the end, this approach demonstrates the power of logical reasoning combined with efficient coding! 🚀