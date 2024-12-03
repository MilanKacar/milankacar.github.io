---
layout: post  
title: "#95 📝 2109. Adding Spaces to a String 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Two Pointers, String, Simulation]
---

In this post, we’ll dive into **LeetCode Problem #2109: Adding Spaces to a String**. This problem involves manipulating strings based on a set of indices, making it a fantastic opportunity to brush up on string slicing and iteration. We’ll explore the problem step-by-step, tackle it with an optimized solution, and showcase an example walkthrough using higher numbers for clarity. Let’s get started! 🌟

---

## 💡 Problem Statement

You are given a string `s` and an array of integers `spaces`, where each integer represents an index in the string `s`. Your task is to **insert a single space at each index in the `spaces` array**. Return the resulting string after adding all the spaces.

### 🔖 Constraints:
- `1 <= s.length <= 3 * 10⁵`
- `1 <= spaces.length <= 3 * 10⁵`
- `0 <= spaces[i] < s.length`
- All values in `spaces` are **strictly increasing**.

---

## 🌟 Example

**Input:**

```python
s = "LeetCodeHelpsYouLearn"
spaces = [8, 13, 18]
```

**Output:**

```
"LeetCode Helps You Learn"
```

**Explanation:**
- Insert a space at index `8`: `"LeetCode HelpsYouLearn"`
- Insert a space at index `13`: `"LeetCode Helps YouLearn"`
- Insert a space at index `18`: `"LeetCode Helps You Learn"`

---

## 🛠️ Basic Solution 💻

Let’s dive deeper into the **basic solution** for solving the problem. This section will explore the underlying mechanics, alternative approaches, and potential pitfalls of this solution, providing a thorough understanding. 

---

### 🔍 Step-by-Step Breakdown

1. **Initialization**:
   - The function starts by initializing two variables:
     - `index` to `0`: This serves as a pointer to track the current position in the string `s`.
     - `result` as an empty list: This will store the substrings of `s` between consecutive indices in `spaces`.

2. **Iterating Through `spaces`**:
   - For each index in the `spaces` array, the string is sliced from the `index` pointer to the current space index:
     - For example, if `s = "LeetCodeHelpsYouLearn"` and `spaces = [8, 13, 18]`, in the first iteration, `s[0:8]` results in `"LeetCode"`.
   - The sliced substring is added to `result`, and the `index` pointer is updated to the current space index.

3. **Appending the Remaining Substring**:
   - After all indices in `spaces` have been processed, the substring from the last index in `spaces` to the end of the string is appended to `result`.
   - For instance, in the example above, the final substring `s[18:]` results in `"Learn"`.

4. **Joining Substrings**:
   - The `join()` method concatenates all substrings in `result` with spaces in between, forming the desired output.

---

### 🔑 Why This Works

1. **String Slicing**:
   - Python string slicing is efficient because it operates on the original string without creating a new copy for each slice.
   - This ensures that slicing operations are performed in \(O(1)\) time for each slice.

2. **List and Join Operations**:
   - Appending substrings to a list (`result`) and joining them at the end ensures that string concatenation, which can be costly if done repeatedly in a loop, is performed only once.

3. **Strictly Increasing Indices**:
   - The problem guarantees that indices in `spaces` are strictly increasing. This simplifies the slicing process, as we don’t need to handle overlapping or unordered indices.

---

### 🛑 Common Pitfalls

1. **Incorrect Slicing**:
   - If you forget to update the `index` pointer after slicing, the program will repeatedly slice from the same starting point, producing incorrect results.
   - Example:
     ```python
     s = "LeetCodeHelpsYouLearn"
     spaces = [8, 13, 18]
     # Forgetting to update `index` would result in:
     result = [s[0:8], s[0:13], s[0:18]]
     ```

2. **Off-by-One Errors**:
   - It’s crucial to ensure that slices are properly aligned. For example, if you slice from `index+1` instead of `index`, the substring would skip one character.

3. **Handling Empty Strings or Arrays**:
   - Ensure that edge cases like `s = ""` or `spaces = []` are handled gracefully without throwing errors.

---

### 🚀 Optimization Considerations

The basic solution is already optimal, but let’s explore why it’s efficient compared to alternative approaches:

#### **Alternative Approach: Direct String Insertion**
A naive approach would involve directly inserting spaces into the string `s`:

```python
for space in spaces:
    s = s[:space] + " " + s[space:]
```

- **Drawbacks**:
  - Each insertion creates a new string, resulting in \(O(n^2)\) time complexity for multiple insertions.
  - For large inputs, this approach becomes infeasible due to high memory usage and slower execution.

#### **Why List-Based Approach is Better**:
- By collecting all substrings in a list (`result`) and joining them at the end, we minimize the number of string concatenation operations, keeping the time complexity at \(O(n)\).

---

### 📝 Detailed Walkthrough of the Code

Let’s walk through the code step-by-step using a simple example:

**Input**:
```python
s = "CodingIsFun"
spaces = [6, 8]
```

**Execution**:

1. **Initialization**:
   ```python
   index = 0
   result = []
   ```

2. **First Iteration (space = 6)**:
   - Slice: `s[0:6]` → `"Coding"`
   - Update `result`: `result = ["Coding"]`
   - Update `index`: `index = 6`

3. **Second Iteration (space = 8)**:
   - Slice: `s[6:8]` → `"Is"`
   - Update `result`: `result = ["Coding", "Is"]`
   - Update `index`: `index = 8`

4. **Appending Remaining String**:
   - Slice: `s[8:]` → `"Fun"`
   - Update `result`: `result = ["Coding", "Is", "Fun"]`

5. **Joining Substrings**:
   - Final Output: `" ".join(result)` → `"Coding Is Fun"`

---

### 🔎 Insights

1. **Efficiency in Handling Large Strings**:
   - The list-based approach processes substrings independently, making it well-suited for large inputs where direct insertion would be computationally expensive.

2. **Simplicity and Readability**:
   - The algorithm is straightforward to understand and implement, leveraging Python's powerful slicing and list operations.

3. **Scalability**:
   - Even for the upper constraint (`s.length = 3 * 10⁵`), the algorithm performs efficiently due to its linear time complexity.

---

### 🛠️ Python Code Recap

```python
class Solution:
    def addSpaces(self, s: str, spaces: List[int]) -> str:
        index, result = 0, []
        for space in spaces:
            result.append(s[index:space])
            index = space
        result.append(s[index:])
        return " ".join(result)
```

This method provides a robust and scalable solution to the problem, leveraging Python’s string and list operations effectively.

---

### ⏱️ Time Complexity

- **Slicing Operation:** Each slice operation takes \(O(1)\) as we’re accessing predefined indices.
- **Join Operation:** Joining all substrings takes \(O(n)\), where \(n\) is the length of the string.

Thus, the total time complexity is **O(n)**.

### 🚀 Space Complexity
- **Auxiliary Space:** The `result` list stores all substrings, requiring \(O(n)\) space.
- Therefore, the space complexity is **O(n)**.

---

## 🔥 Optimized Solution 🚀

The basic solution is already optimal in terms of **time and space complexity** since it processes the string and indices efficiently. Let’s demonstrate its robustness with a larger-scale example and discuss edge cases.

---

### 📚 Example Walkthrough with Higher Numbers

Let’s take a longer string and a more extensive `spaces` array:

**Input:**

```python
s = "IHopeYouAreEnjoyingThisChallenge"
spaces = [1, 5, 8, 12, 15, 23, 30]
```

**Execution Steps:**
1. Start with `index = 0` and `result = []`.
2. Iterate over each index in `spaces`:
   - Add the substring `s[0:1]` → `"I"`, update `index = 1`.
   - Add the substring `s[1:5]` → `"Hope"`, update `index = 5`.
   - Add the substring `s[5:8]` → `"You"`, update `index = 8`.
   - Add the substring `s[8:12]` → `"Are"`, update `index = 12`.
   - Add the substring `s[12:15]` → `"Enj"`, update `index = 15`.
   - Add the substring `s[15:23]` → `"oyingThi"`, update `index = 23`.
   - Add the substring `s[23:30]` → `"sChalle"`, update `index = 30`.
3. Append the remaining part of the string (`s[30:]`) → `"nge"`.

**Output:** `"I Hope You Are Enjoying This Challenge"`

---

### 🧩 Edge Cases

1. **Empty Input String**:
   - If `s = ""` and `spaces = []`, return `""`.
2. **Spaces at Every Character**:
   - For `s = "abc"` and `spaces = [1, 2]`, the output is `"a b c"`.
3. **No Spaces to Add**:
   - For `s = "hello"` and `spaces = []`, the output is `"hello"`.
4. **Spaces at the Start or End**:
   - For `s = "python"` and `spaces = [0, 6]`, handle gracefully to avoid slicing errors.

---

## 🛠️ Conclusion 🔍

In this blog post, we explored **LeetCode #2109** and solved it using a single optimal solution. This problem is a fantastic illustration of string manipulation using slicing, making it highly relevant for technical interviews and string-related tasks. 

We hope this walkthrough has been helpful! For more coding insights, keep an eye on our blog. Happy coding! 🌟
