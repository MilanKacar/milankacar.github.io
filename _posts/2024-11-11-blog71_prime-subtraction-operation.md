---
layout: post  
title: "#84 🔄 1861. Rotating the Box 🧠🚀"  
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Math, Binary Search, Greedy, Number Theory]
---

Imagine you’re given an array of numbers, and you need to make it strictly increasing by subtracting prime numbers from each element. Sounds like a math puzzle, right? 🤔 Let’s dive into how we can achieve this efficiently using an optimized approach with prime numbers! 🌟

---

### 📝 Problem Statement
Given a 0-indexed integer array `nums` of length `n`, you can perform the following operation multiple times:

1. Pick an index `i` that hasn’t been picked before.
2. Pick a prime number `p` such that `p` is strictly less than `nums[i]`.
3. Subtract `p` from `nums[i]`.

The goal is to make `nums` a **strictly increasing array**. Return `True` if this is possible, otherwise return `False`.

#### A Strictly Increasing Array:
An array is strictly increasing if every element is greater than its previous element.

### 🔍 Examples

#### Example 1
- **Input:** `nums = [4, 9, 6, 10]`
- **Output:** `True`
- **Explanation:**
    1. Select `i = 0`, choose `p = 3`. After subtracting, `nums` becomes `[1, 9, 6, 10]`.
    2. Select `i = 1`, choose `p = 7`. After subtracting, `nums` becomes `[1, 2, 6, 10]`.

    The array is now strictly increasing.

#### Example 2
- **Input:** `nums = [6, 8, 11, 12]`
- **Output:** `True`
- **Explanation:** `nums` is already strictly increasing, so no operations are needed.

#### Example 3
- **Input:** `nums = [5, 8, 3]`
- **Output:** `False`
- **Explanation:** It's impossible to make this array strictly increasing by subtracting primes.

---

### 🔍 Edge Cases
- **Single Element Array:** `nums = [x]` – No operation is required; return `True`.
- **Already Sorted Array:** Arrays like `[2, 5, 7]` – The function should detect this and return `True` without any modifications.
- **All Elements Equal:** `nums = [7, 7, 7]` – It is impossible to make it strictly increasing; return `False`.
- **Large Prime Elements:** Arrays with large values may require selecting specific primes for each number to achieve strict ordering.

---

## 💡 Approach 1: Basic Solution

The naive approach to solving this problem would involve:
1. **Generate All Possible Primes**: Create a list of primes that are strictly less than the largest element in `nums`.
2. **Iterate Through Each Element**: For each element in `nums`, try subtracting prime numbers to make it smaller and check if this leads to a strictly increasing array.

However, this brute-force method would be inefficient for larger arrays, as it requires recalculating primes multiple times and does not optimize for minimal operations.

### 🕰️ Time Complexity of Basic Solution
- **Prime Generation:** If we generate primes up to the maximum of `nums`, this can take `O(N^2)` in the worst case.
- **Checking Strict Order:** Each element may require an operation, resulting in an additional `O(N)` complexity.

This leads to an overall complexity of about **O(N^2)**, which may be too slow for large inputs.

---

## 💡 Optimized Solution: Using Precomputed Primes and Binary Search 🧑‍💻

The optimized solution leverages a few key strategies:
1. **Precompute Primes**: We first generate a list of primes up to the largest element in `nums` using a prime-checking algorithm (like the Sieve of Eratosthenes).
2. **Binary Search for Efficiency**: For each `nums[i]`, we use binary search to find the largest prime `p` such that `nums[i] - p > nums[i - 1]`. This ensures that `nums[i]` remains larger than `nums[i-1]`, maintaining the strictly increasing property.
3. **Early Exit**: If we cannot find a prime to satisfy the condition for any `nums[i]`, we immediately return `False`.

This approach is efficient because:
- **Binary Search**: Finding the appropriate prime for each element can be done in `O(log P)` where `P` is the number of primes, reducing unnecessary checks.
- **Single Pass**: We process each element once, making this solution very efficient.

### 🚀 Optimized Solution Code

```python
from bisect import bisect_right
from typing import List

class Solution:
    def primeSubOperation(self, nums: List[int]) -> bool:
        # Generate a list of primes up to the max element in nums
        primes = []
        for i in range(2, max(nums)):
            for j in primes:
                if i % j == 0:
                    break
            else:
                primes.append(i)
        
        # Traverse the list from the second last element down to the first
        n = len(nums)
        for i in range(n - 2, -1, -1):
            if nums[i] < nums[i + 1]:
                continue
            # Use binary search to find the largest prime less than the difference
            j = bisect_right(primes, nums[i] - nums[i + 1])
            if j == len(primes) or primes[j] >= nums[i]:
                return False
            nums[i] -= primes[j]
        return True
```

---

### 🕰️ Time Complexity Analysis

**Prime Generation**: Generating primes up to `max(nums)` requires `O(N log log N)` using the Sieve of Eratosthenes.

**Binary Search Operations**: For each element in `nums`, binary search on the list of primes takes `O(log P)`, where `P` is the number of primes generated.

**Total Complexity**: The overall time complexity is approximately **O(N log log N)**, which is efficient for the problem constraints.

---

## 🧮 Example Walkthrough with Higher Numbers

Let’s take a closer look with an example that has larger values to understand how the solution operates efficiently.

#### Input: `nums = [20, 30, 25, 40]`

1. **Step 1**: Check if `nums[2] < nums[3]` (i.e., `25 < 40`).
   - Yes, this part of the array is already sorted, so we move to the next element.
   
2. **Step 2**: Check if `nums[1] < nums[2]` (i.e., `30 < 25`).
   - No, we need to subtract a prime from `nums[1]`.
   - **Binary Search**: Find the largest prime `p` such that `nums[1] - p > nums[0]` and `p < nums[1]`.
   - Select `p = 5` (largest prime less than `30 - 25`).
   - Subtract `5`, so `nums` becomes `[20, 25, 25, 40]`.

3. **Step 3**: Check if `nums[0] < nums[1]` (i.e., `20 < 25`).
   - Yes, this part of the array is sorted.

After these adjustments, `nums = [20, 25, 25, 40]` is strictly increasing, so we return `True`.

---

## 🔚 Conclusion

This problem demonstrates a powerful combination of binary search and precomputed primes to solve a constraint-heavy problem efficiently. By leveraging **prime subtraction**, we can convert an unsorted array into a strictly increasing sequence, given that the conditions allow. This approach can be valuable in mathematical programming scenarios, emphasizing optimization through **binary search** and **sieve algorithms**.

With this in mind, **prime subtraction operation** provides a unique way to enforce ordering on arrays without traditional sorting, offering a neat mix of number theory and algorithmic efficiency! 🎉
