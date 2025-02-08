---
layout: post  
title: "#115 📦 2349. Design a Number Container System 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Hash Table, Design, Heap (Priority Queue), Ordered Set]
---

Imagine you have a system where you can **store numbers at specific indices** and **quickly retrieve the smallest index** containing a particular number. Sounds easy, right? 🤔 But the challenge lies in making it **efficient** and **scalable** since the index and number range can go up to **10⁹**, and we can have **100,000 operations**!  

In this post, we'll break down this problem step by step:  

✅ Understanding the **problem statement** 📜  
✅ **Building a naive solution** 🛠️  
✅ **Optimizing it** for efficiency ⚡  
✅ **Time complexity analysis** ⏳  
✅ **Example walkthrough** with large numbers 🔢  
✅ **Edge cases** to consider 🚧  
✅ **Conclusion** 🎯  

Let's get started! 🚀  

---

## 📝 Problem Statement  
We need to implement a **Number Container System** that supports two operations:  

1️⃣ `change(index, number)`: Stores or updates the **number** at a given **index**. If a number already exists at the index, it is replaced.  

2️⃣ `find(number)`: Returns the **smallest index** containing the given **number**. If the number is not found, return **-1**.  

### 🔹 Example  
**Input:**  
```python
["NumberContainers", "find", "change", "change", "change", "change", "find", "change", "find"]
[[], [10], [2, 10], [1, 10], [3, 10], [5, 10], [10], [1, 20], [10]]
```  

**Output:**  
```python
[None, -1, None, None, None, None, 1, None, 2]
```  

**Explanation:**  
1. `find(10) -> -1` (No numbers stored yet 🚫)  
2. `change(2, 10)`, `change(1, 10)`, `change(3, 10)`, `change(5, 10)` (Stores `10` at indices **2, 1, 3, 5**)  
3. `find(10) -> 1` (The smallest index with `10` is **1**)  
4. `change(1, 20)` (Replaces `10` at index **1** with `20`)  
5. `find(10) -> 2` (The smallest index with `10` is now **2**)  

---

## 🛠️ Basic Solution (Brute Force)  
### 🔍 Idea  
We'll use a **dictionary (hash map)** to store **index → number** mappings. To find the **smallest index** for a given number, we scan all entries in the dictionary.  

### 🔹 Implementation  
```python
class NumberContainers:
    def __init__(self):
        self.indexToNum = {}

    def change(self, index: int, number: int) -> None:
        self.indexToNum[index] = number

    def find(self, number: int) -> int:
        min_index = float('inf')
        for idx, num in self.indexToNum.items():
            if num == number:
                min_index = min(min_index, idx)
        return min_index if min_index != float('inf') else -1
```  

### ⏳ Time Complexity  
- `change(index, number)`: **O(1)** ✅ (Dictionary update)  
- `find(number)`: **O(N)** ❌ (We scan all indices)  

This approach **fails** for large inputs, where `find()` runs in **O(N)**, making it **too slow**! 🚨  

---

## ⚡ Optimized Solution (Using Min Heap)  
### 🔍 Idea  
Instead of scanning all indices, we **maintain a min-heap** (priority queue) for each number to **quickly retrieve the smallest index**.  

### 🔹 Approach  
1️⃣ **Use two dictionaries**:  
   - `indexToNum`: Maps each **index** to the **number** stored there.  
   - `numToIndices`: Maps each **number** to a **min-heap** of indices where it appears.  

2️⃣ **`change(index, number)`**:  
   - Update `indexToNum`.  
   - Add `index` to the **min-heap** for `number`.  

3️⃣ **`find(number)`**:  
   - Retrieve the **smallest index** from the min-heap.  
   - If it points to a different number (due to updates), remove it and check again.  

### 🔹 Implementation  
```python
import heapq

class NumberContainers:
    def __init__(self):
        self.indexToNum = {}  # Maps index -> number
        self.numToIndices = {}  # Maps number -> min heap of indices

    def change(self, index: int, number: int) -> None:
        self.indexToNum[index] = number
        if number not in self.numToIndices:
            self.numToIndices[number] = []
        heapq.heappush(self.numToIndices[number], index)

    def find(self, number: int) -> int:
        if number not in self.numToIndices:
            return -1
        pq = self.numToIndices[number]
        while pq:
            currIndex = pq[0]
            if self.indexToNum[currIndex] != number:
                heapq.heappop(pq)  # Remove outdated index
            else:
                return currIndex
        return -1
```  

### ⏳ Time Complexity  
- `change(index, number)`: **O(log N)** (Heap push) ✅  
- `find(number)`: **O(log N)** (Heap operations) ✅  

This is much **faster** than the brute force **O(N)** solution! 🚀  

---

## 🔍 Example Walkthrough (Large Numbers)  
### 🔹 Input  
```python
nc = NumberContainers()
nc.change(1000000000, 99)  # Index 1B -> 99
nc.change(999999999, 99)   # Index 999M -> 99
nc.change(500000000, 99)   # Index 500M -> 99
nc.find(99)  # Expected: 500M (Smallest index)
nc.change(500000000, 100)  # Replace 99 with 100 at 500M
nc.find(99)  # Expected: 999M (Smallest index left with 99)
```  

### 🔹 Execution  
```
change(1000000000, 99) ✅
change(999999999, 99) ✅
change(500000000, 99) ✅
find(99) -> 500000000 ✅
change(500000000, 100) ✅
find(99) -> 999999999 ✅
```  

---

## 🚧 Edge Cases  
✅ **Large index values** (Test with `10⁹` values)  
✅ **Updating an existing index** (`change()` should replace values correctly)  
✅ **Removing the smallest index** (Ensure `find()` updates correctly)  
✅ **Empty system** (`find()` should return `-1`)  

---

## 🎯 Conclusion  
🎯 The **naive approach (O(N))** was **too slow** for large inputs.  
⚡ The **optimized heap-based approach (O(log N))** is much faster.  
🛠️ We used **two dictionaries + min-heap** to efficiently track indices.  

This problem teaches **hash maps**, **heaps**, and **efficient data lookups**—essential for **competitive programming** and **system design**! 🚀  
