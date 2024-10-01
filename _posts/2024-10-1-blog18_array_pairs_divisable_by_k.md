---
layout: post
title: "#18 1497. Check If Array Pairs Are Divisible by k 🚀"
categories: [LeetCode, Programming]
---


Hello, problem solvers! 👋 Today, we’re going to tackle a **cool problem** that involves dividing an array into pairs, ensuring the sum of each pair is divisible by a given number `k`. Let’s explore how to solve it efficiently! 🚀

### Problem Statement 📋

Given an array of integers `arr` of even length `n` and an integer `k`, our task is to divide the array into exactly `n / 2` pairs such that the sum of each pair is divisible by `k`.

Return **true** if this is possible, or **false** otherwise.

### Example 🧐

- **Example 1:**
  ```plaintext
  Input: arr = [1, 2, 3, 4, 5, 10, 6, 7, 8, 9], k = 5
  Output: true
  Explanation: Pairs are (1,9), (2,8), (3,7), (4,6), and (5,10). All pairs have sums divisible by 5.
  ```

- **Example 2:**
  ```plaintext
  Input: arr = [1, 2, 3, 4, 5, 6], k = 7
  Output: true
  Explanation: Pairs are (1,6), (2,5), and (3,4). All pairs have sums divisible by 7.
  ```

- **Example 3:**
  ```plaintext
  Input: arr = [1, 2, 3, 4, 5, 6], k = 10
  Output: false
  Explanation: There's no way to divide the array into pairs such that each pair’s sum is divisible by 10.
  ```

### 🛠️ Basic Solution

To solve this problem, we need to check whether all pairs can have sums divisible by `k`. For any number `x`, the sum `x + y` will be divisible by `k` if both `x % k` and `y % k` add up to `k`.

Steps to solve the problem:

1. For each element in the array, compute its remainder when divided by `k`.
2. For each remainder `r`, we need to check if there exists another element whose remainder is `(k - r) % k`.
3. Handle special cases, such as when `r == 0`, where the elements should pair with other elements that also have a remainder of 0.

Let’s implement the basic solution:

```python
from collections import defaultdict

def canArrange(arr, k):
    remainder_count = defaultdict(int)
    
    # Count the frequency of each remainder
    for num in arr:
        remainder = num % k
        remainder_count[remainder] += 1
    
    # Check if we can form valid pairs
    for rem in remainder_count:
        if rem == 0:  # If remainder is 0, pairs must also have remainder 0
            if remainder_count[rem] % 2 != 0:
                return False
        else:
            complement = k - rem  # Pair remainder with complement
            if remainder_count[rem] != remainder_count[complement]:
                return False
                
    return True
```

### Explanation 🧑‍🏫

- **Step 1**: We use a dictionary (`remainder_count`) to store the frequency of each remainder when divided by `k`.
- **Step 2**: For each remainder `r`, we check if there’s a corresponding number in the array whose remainder is `k - r`. If the counts don’t match, return `False`.
- **Step 3**: Handle the case when the remainder is `0`. For these numbers, they can only pair with other numbers that also give a remainder of `0`.

### Time Complexity ⏳

- **Time Complexity**: The solution runs in **O(n)**, where `n` is the size of the array, because we’re looping through the array twice: once to count remainders and once to validate pairs.
- **Space Complexity**: We use **O(k)** space for storing the remainder counts.

### 🏎️ Optimized Python Solution

The solution above is already efficient since it uses a single loop for counting remainders and a constant number of checks to verify the pairs.

Here’s the **optimized solution**:

```python
from collections import defaultdict

def canArrange(arr, k):
    remainder_count = [0] * k  # Use an array to store remainder frequencies
    
    # Step 1: Count remainders
    for num in arr:
        remainder = num % k
        remainder_count[remainder] += 1

    # Step 2: Check pairs
    for rem in range(1, k // 2 + 1):
        if remainder_count[rem] != remainder_count[k - rem]:
            return False
    
    # Special check for remainder 0
    if remainder_count[0] % 2 != 0:
        return False
    
    return True
```

### Explanation of the Optimized Solution 🚀

- Instead of using a dictionary, we use an array `remainder_count` of size `k` to track remainders. This makes accessing and updating remainder counts faster.
- **Step 2**: We only loop through half of the possible remainders since `r` and `k - r` are complementary. We avoid redundant checks.
- **Special Case for Zero**: We handle the special case of pairs where the remainder is `0` in the same way as before.

### Time Complexity ⏱️

- **Time Complexity**: Still **O(n)**, as we’re looping over the array and checking pairs. The optimized solution reduces the constant factor by using an array for remainder counting.
- **Space Complexity**: **O(k)** due to the array `remainder_count`.

### Conclusion 🎯

Both solutions efficiently solve the problem in **O(n)** time. The **optimized solution** uses an array to store remainder counts, making it slightly faster and easier to implement than the dictionary approach. 🎉

### Key Takeaways 📝

- We solve the problem by checking if each number's remainder can pair with a complementary remainder.
- Handling edge cases, like remainder `0`, is crucial.
- The time complexity for both solutions is **O(n)**, which is optimal for this problem.

Hope this helps you understand how to tackle this array-pairing problem! Keep coding and exploring! 🚀
