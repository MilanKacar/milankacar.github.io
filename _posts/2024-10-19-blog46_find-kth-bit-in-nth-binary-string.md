---
layout: post
title: "#46 1545. Find Kth Bit in Nth Binary String 🧠🚀"
categories: [LeetCode, Programming]
---

In this problem, we explore a fascinating sequence of binary strings where each new string is built recursively by combining the previous string, a "1", and the reversed and inverted version of the previous string. Our goal is to determine the `k`th bit of the `n`th binary string efficiently, without having to explicitly construct the entire string.

This problem teaches us how recursion and string manipulation can be cleverly combined to solve problems that seem difficult at first glance but can be broken down into manageable parts!

### 📜 Problem Statement
We are given two positive integers `n` and `k`. The binary string `S_n` is defined recursively as follows:

- `S1 = "0"`
- `Si = Si - 1 + "1" + reverse(invert(Si - 1))` for `i > 1`

Where:
- `+` denotes the concatenation of strings.
- `reverse(x)` returns the reversed version of string `x`.
- `invert(x)` inverts the bits in `x` (i.e., `0` becomes `1` and `1` becomes `0`).

Our task is to return the `k`th bit of `S_n`. It is guaranteed that `k` is valid for the given `n`.

#### Examples

**Example 1:**
```
Input: n = 3, k = 1
Output: "0"
Explanation: S3 = "0111001". The 1st bit is "0".
```

**Example 2:**
```
Input: n = 4, k = 11
Output: "1"
Explanation: S4 = "011100110110001". The 11th bit is "1".
```

### 🚶 Step-by-Step Explanation

To better understand the problem, let’s break down how each string in the sequence is built. 

- `S1 = "0"` is the first string. It’s just `"0"`.
  
- To construct `S2`, we take `S1`, add a `"1"`, and then reverse and invert `S1`. So:
  - `reverse(S1) = "0"`, 
  - `invert(S1) = "1"`.
  - Therefore, `S2 = S1 + "1" + "1" = "011"`.

- To construct `S3`, we repeat the same process:
  - `reverse(S2) = "110"`, 
  - `invert(S2) = "001"`.
  - Therefore, `S3 = S2 + "1" + "001" = "0111001"`.

- To construct `S4`, the process is similar:
  - `reverse(S3) = "1001110"`,
  - `invert(S3) = "0110001"`,
  - Therefore, `S4 = S3 + "1" + "0110001" = "011100110110001"`.

We can observe that the string is growing exponentially as `n` increases, with each `S_n` consisting of:
- `S_(n-1)`,
- a `"1"`,
- and the reversed, inverted version of `S_(n-1)`.

The length of `S_n` follows the formula: `Length(S_n) = 2^n - 1`. For example:
- `S1` has length 1,
- `S2` has length 3,
- `S3` has length 7,
- `S4` has length 15,
and so on.

### 🛠️ Basic Solution

A naive solution to this problem would involve generating the entire string `S_n` and then returning the `k`th bit directly. This solution is simple to implement but highly inefficient due to the exponential growth in the size of the strings.

#### Python Code (Basic Solution)

```python
class Solution:
    def findKthBit(self, n: int, k: int) -> str:
        def invert(s: str) -> str:
            return ''.join('1' if ch == '0' else '0' for ch in s)

        def reverse(s: str) -> str:
            return s[::-1]

        def generate_sn(n: int) -> str:
            if n == 1:
                return "0"
            prev = generate_sn(n - 1)
            return prev + "1" + reverse(invert(prev))
        
        sn = generate_sn(n)
        return sn[k - 1]
```

### ⏳ Time Complexity of Basic Solution

The time complexity of this basic approach is **O(2^n)**, which comes from the fact that generating `S_n` requires building an exponentially larger string at each step. This becomes impractical as `n` increases, especially for large values close to the problem’s constraints (`n = 20`).

Thus, this basic solution is inefficient for larger inputs and would result in **time limit exceeded (TLE)** errors in competitive programming environments.

### ⚡ Optimized Solution

The key insight here is that we don’t need to generate the entire string! By leveraging the recursive structure of the string, we can break the problem into smaller subproblems and solve it more efficiently.

#### Key Observations:
1. The middle bit of any string `S_n` is always `"1"`.
2. Bits before the middle are identical to the bits in `S_(n-1)`.
3. Bits after the middle are the reversed and inverted version of `S_(n-1)`.

Using these insights, we can recursively determine the value of the `k`th bit:
- If `k` is in the first half of the string, the answer is the same as the `k`th bit of `S_(n-1)`.
- If `k` is the middle bit, it is always `"1"`.
- If `k` is in the second half, it corresponds to the inverted, reversed part, and we need to check the corresponding bit in `S_(n-1)`.

#### Python Code (Optimized Solution)

```python
class Solution:
    def findKthBit(self, n: int, k: int) -> str:
        if n == 1:
            return "0"
        length = (1 << n) - 1  # Length of S_n (2^n - 1)
        mid = length // 2 + 1  # Middle position of S_n

        if k == mid:
            return "1"  # Middle bit is always 1
        elif k < mid:
            return self.findKthBit(n - 1, k)  # First half, same as S_(n-1)
        else:
            return '0' if self.findKthBit(n - 1, length - k + 1) == '1' else '1'
```

### ⏳ Time Complexity of Optimized Solution

In this optimized solution, we are reducing the problem size by half at each recursive step. The time complexity is now **O(n)** because we only make one recursive call at each level of `n`, and the problem size reduces at each step.

This allows the solution to handle the upper limit of `n = 20` efficiently.

### 🧮 Example Walkthrough

Let’s walk through **Example 2** step by step (`n = 4`, `k = 11`):

We need to find the 11th bit in `S4`, which is `011100110110001`.

1. The total length of `S4` is `15`. The middle position is `8`. Since `k = 11` is greater than `8`, we know the 11th bit lies in the **second half** of `S4`. This means it corresponds to the **reversed and inverted part of `S3`**.

2. To find the exact position in `S3`, we use the formula `(length - k + 1)`, where `length = 15`. So:
   - `15 - 11 + 1 = 5`
   - The 11th bit in `S4` corresponds to the **5th bit in `S3`**.

3. Now, we check the 5th bit in `S3` (`0111001`):
   - The 5th bit of `S3` is `"0"`. 

4. Since the second half of `S4` is the **inverted** version of `S3`, we invert this bit:
   - `"0"` becomes `"1"`.

Thus, the 11th bit of `S4` is `"1"`.

### 🏁 Conclusion

This problem showcases how a seemingly complex string manipulation task can be simplified using recursion and careful analysis of the problem structure. By leveraging symmetry, we reduced the problem from an exponential complexity to a manageable linear time complexity.

We explored both a basic solution, which builds the string step by step, and an optimized solution that efficiently computes the result without generating the entire string. This optimized approach highlights the power of recursion and the importance of recognizing patterns in problem-solving.
