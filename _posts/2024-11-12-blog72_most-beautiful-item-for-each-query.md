---
layout: post  
title: "#72 💎 2070. Most Beautiful Item for Each Query 🧠🚀"  
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Binary Search, Sorting]
---

Imagine you're in a store filled with beautiful items, each with a price and a beauty score. You have a limited budget for each shopping trip, and you want to get the most beautiful item that your money can buy! This problem is all about finding the maximum beauty within a price constraint for each query. Let’s explore how we can efficiently solve this with Python!

### 📝 Problem Statement

Given:
- `items`: A 2D list where `items[i] = [price_i, beauty_i]` represents the price and beauty of an item.
- `queries`: A list where each `queries[j]` represents a budget. For each query, find the maximum beauty of an item with a price less than or equal to `queries[j]`. If no item meets the budget, the answer for that query is `0`.

Return an array `answer`, where `answer[j]` is the solution for `queries[j]`.

### 📊 Example

**Input**:  
```python
items = [[1, 2], [3, 2], [2, 4], [5, 6], [3, 5]]
queries = [1, 2, 3, 4, 5, 6]
```

**Output**:  
```python
[2, 4, 5, 5, 6, 6]
```

**Explanation**:
- For `queries[0] = 1`, the max beauty among items with `price <= 1` is `2`.
- For `queries[1] = 2`, items with `price <= 2` have max beauty `4`.
- For `queries[2] = 3`, items with `price <= 3` have max beauty `5`.
- And so on...

---

### 🧐 Approach

We'll explore two solutions:
1. **Naive Solution**: Checking each item for each query.
2. **Optimized Solution**: Using sorting and prefix maximums for efficiency.

### 💡 Basic Solution (Brute Force) 💡

#### Steps
For each query:
1. Traverse the `items` list.
2. For each item that fits within the budget, track the maximum beauty.
3. Append the result to the answer list.

#### 🕰️ Time Complexity

- **Worst-case Time Complexity**: $$O(n \times m)$$, where $$n$$ is the length of `queries` and $$m$$ is the length of `items`.
- This is inefficient for large inputs as it involves re-checking each item for every query.

#### 🔥 Code Implementation (Brute Force)

```python
def mostBeautifulItemBruteForce(items, queries):
    answer = []
    for q in queries:
        max_beauty = 0
        for price, beauty in items:
            if price <= q:
                max_beauty = max(max_beauty, beauty)
        answer.append(max_beauty)
    return answer
```

### ⚙️ Optimized Solution ⚙️

To improve efficiency, let's leverage sorting and prefix maximums.

#### Steps

1. **Sort** both `items` by price and `queries` while keeping track of their original indices.
2. **Build a max beauty prefix** for `items`, where `prefix[i]` stores the maximum beauty from the start up to `items[i]`.
3. For each sorted query:
   - Use a pointer to move through the items within the price limit of the current query.
   - Store the maximum beauty from `prefix` that can be bought with the query budget.
4. **Restore** the original order of queries.

#### 🕰️ Time Complexity

- **Optimized Time Complexity**: $$O(m \log m + n \log n)$$, where $$m$$ and $$n$$ are the lengths of `items` and `queries`, respectively.
- This is feasible for large input sizes due to reduced re-checks by leveraging sorted data.

---

#### 🚀 Code Implementation (Optimized Solution)

```python
def mostBeautifulItemOptimized(items, queries):
    # Step 1: Sort items by price
    items.sort()
    # Step 2: Sort queries while keeping track of their indices
    sorted_queries = sorted((q, i) for i, q in enumerate(queries))
    
    # Step 3: Prepare answer array and prefix variables
    answer = [0] * len(queries)
    max_beauty, j = 0, 0
    
    # Step 4: Traverse queries in sorted order
    for q_value, q_index in sorted_queries:
        # Expand max beauty up to the current query price
        while j < len(items) and items[j][0] <= q_value:
            max_beauty = max(max_beauty, items[j][1])
            j += 1
        # Record the answer for this query's index
        answer[q_index] = max_beauty
    
    return answer
```

### 🔍 Example Walkthrough

Using the **optimized solution** with `items = [[1,2],[3,2],[2,4],[5,6],[3,5]]` and `queries = [1,2,3,4,5,6]`.

1. **Sort `items` by price**:
   ```python
   items = [[1,2], [2,4], [3,2], [3,5], [5,6]]
   ```

2. **Sort `queries` with indices**:
   ```python
   sorted_queries = [(1,0), (2,1), (3,2), (4,3), (5,4), (6,5)]
   ```

3. **Traverse `sorted_queries` and use `items` up to each query**:
   - For `q = 1`: `items` up to `price 1` gives max beauty `2`.
   - For `q = 2`: max beauty up to `price 2` is `4`.
   - For `q = 3`: max beauty up to `price 3` is `5`.
   - And so on...

The result is `[2, 4, 5, 5, 6, 6]`.

---

### 🧩 Edge Cases

1. **No items within budget**: If `queries` has a value smaller than the lowest item price, return `0` for those queries.
2. **All items have the same price and beauty**: Should correctly return the maximum beauty for all queries.
3. **Single item**: Should handle cases where `items` has only one element.
4. **Single query**: Should handle cases with only one query.

### 🎯 Conclusion

In this post, we covered an efficient approach to answer multiple queries on finding the most beautiful item within budget constraints. By sorting both `items` and `queries`, and using a prefix maximum for beauty, we avoid redundant comparisons, achieving much faster performance. This problem highlights how sorting and prefix arrays can greatly reduce time complexity in range-query problems.
