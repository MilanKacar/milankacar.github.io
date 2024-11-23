---
layout: post  
title: "#38 632. Smallest Range Covering Elements from K Lists 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, HashTable, Greedy, Sliding Window, Sorting, Heap (Priority Queue)]
---

In this post, we'll tackle a hard problem from LeetCode: **Smallest Range Covering Elements from K Lists**. The challenge is to find the smallest range that includes at least one number from each of the given `k` lists of sorted integers. This problem is a fantastic way to practice using heaps or a sliding window technique with pointers.

---

### Problem Statement 📝

You are given `k` lists of sorted integers in non-decreasing order. Your goal is to find the smallest range [a, b] that includes at least one number from each of the `k` lists.

We define the range [a, b] as smaller than range [c, d] if:
- `b - a < d - c`, or
- `a < c` when `b - a == d - c`.

#### Example 1:
**Input**:  
`nums = [[4, 10, 15, 24, 26], [0, 9, 12, 20], [5, 18, 22, 30]]`

**Output**:  
`[20, 24]`

**Explanation**:  
- List 1: `[4, 10, 15, 24, 26]` - 24 is within the range [20, 24].
- List 2: `[0, 9, 12, 20]` - 20 is within the range [20, 24].
- List 3: `[5, 18, 22, 30]` - 22 is within the range [20, 24].

#### Example 2:
**Input**:  
`nums = [[1, 2, 3], [1, 2, 3], [1, 2, 3]]`

**Output**:  
`[1, 1]`

---

### Optimized Solution 1: Using `heapq` 💡

This problem can be solved efficiently with a **min-heap** (priority queue). The idea is to maintain a heap of the smallest elements from each list, while also keeping track of the current maximum element. This allows us to continually find the smallest range by pushing the next element from the list that contains the current minimum value.

#### Python Code (Using `heapq`)

```python
import heapq

def smallestRange(nums):
    min_heap = []
    max_val = float('-inf')

    # Insert the first element of each list into the heap
    for i in range(len(nums)):
        heapq.heappush(min_heap, (nums[i][0], i, 0))
        max_val = max(max_val, nums[i][0])

    result_range = [-float('inf'), float('inf')]

    while min_heap:
        min_val, list_idx, elem_idx = heapq.heappop(min_heap)

        # Update result range if current range is smaller
        if max_val - min_val < result_range[1] - result_range[0]:
            result_range = [min_val, max_val]

        # If we reach the end of one list, break the loop
        if elem_idx + 1 == len(nums[list_idx]):
            break

        # Push the next element from the same list into the heap
        next_val = nums[list_idx][elem_idx + 1]
        heapq.heappush(min_heap, (next_val, list_idx, elem_idx + 1))
        max_val = max(max_val, next_val)

    return result_range
```

---

### Walkthrough of Heap-based Solution 🔍

Let’s walk through **Example 1** using this approach:

#### **Example 1**:
```plaintext
nums = [[4, 10, 15, 24, 26], 
        [0, 9, 12, 20], 
        [5, 18, 22, 30]]
```

**Step 1**:  
Initialize the heap with the first element from each list:
- Heap: `[(0, 1, 0), (4, 0, 0), (5, 2, 0)]`
- `max_val = 5` (the largest of 0, 4, and 5).

**Step 2**:  
Pop the smallest element (0), update the range to `[0, 5]`, and push the next element from List 2:
- Heap: `[(4, 0, 0), (5, 2, 0), (9, 1, 1)]`
- `max_val = 9`.

**Step 3**:  
Pop the smallest element (4), update the range to `[4, 9]`, and push the next element from List 1:
- Heap: `[(5, 2, 0), (9, 1, 1), (10, 0, 1)]`
- `max_val = 10`.

**Step 4**:  
Pop the smallest element (5), update the range to `[5, 10]`, and push the next element from List 3:
- Heap: `[(9, 1, 1), (10, 0, 1), (18, 2, 1)]`
- `max_val = 18`.

**Step 5**:  
Pop the smallest element (9), update the range to `[9, 18]`, and push the next element from List 2:
- Heap: `[(10, 0, 1), (18, 2, 1), (12, 1, 2)]`
- `max_val = 18`.

We continue this process until we find the optimal range `[20, 24]`. 

---

### Optimized Solution 2: Without Using `heapq` 💡

Instead of using a heap, we can solve the problem with a **pointers-based approach**. In this method, we manually track pointers for each list, updating the current minimum and maximum values by iterating through the lists.

#### Python Code (Without `heapq`)

```python
def smallestRange(nums):
    pointers = [0] * len(nums)
    result_range = [-float('inf'), float('inf')]

    while True:
        current_min = float('inf')
        current_max = -float('inf')
        min_list_idx = -1

        for i in range(len(nums)):
            val = nums[i][pointers[i]]
            if val < current_min:
                current_min = val
                min_list_idx = i
            current_max = max(current_max, val)

        if current_max - current_min < result_range[1] - result_range[0]:
            result_range = [current_min, current_max]

        pointers[min_list_idx] += 1

        if pointers[min_list_idx] == len(nums[min_list_idx]):
            break
    
    return result_range
```

---

### Walkthrough of Pointers-based Solution 🔍

Let’s walk through **Example 1** using this approach:

#### **Example 1**:
```plaintext
nums = [[4, 10, 15, 24, 26], 
        [0, 9, 12, 20], 
        [5, 18, 22, 30]]
```

**Step 1**:  
Start with pointers at the first element of each list:
- Pointers: `[0, 0, 0]`
- Current values: `[4, 0, 5]`
- `current_min = 0`, `current_max = 5`, range: `[0, 5]`.

**Step 2**:  
Move the pointer in List 2 (since it has the current minimum value):
- Pointers: `[0, 1, 0]`
- Current values: `[4, 9, 5]`
- `current_min = 4`, `current_max = 9`, range: `[4, 9]`.

**Step 3**:  
Move the pointer in List 1:
- Pointers: `[1, 1, 0]`
- Current values: `[10, 9, 5]`
- `current_min = 5`, `current_max = 10`, range: `[5, 10]`.

**Step 4**:  
Move the pointer in List 3:
- Pointers: `[1, 1, 1]`
- Current values: `[10, 9, 18]`
- `current_min = 9`, `current_max = 18`, range: `[9, 18]`.

The process continues until we find the range `[20, 24]`.

---

### Time Complexity ⏳

Both solutions have similar time complexity:
- **Heap-based solution**: **O(n \* log k)**, where `n` is the total number of elements across all lists, and `k` is the number of lists. The heap operations take logarithmic time.
- **Pointers-based solution**: **O(n \* k)**, as we search through all lists in each iteration.

---

### Conclusion ✅

This problem can be tackled using either a **min-heap** or a **pointers-based approach**. Both methods aim to maintain the current minimum and maximum values across all lists to find the smallest range. While the heap-based solution offers faster updates, the pointers-based solution provides a simpler implementation with comparable time complexity.

Let me know which approach you find more intuitive or if you have any further questions! 😊
