---
layout: post  
title: "#73 🚚📦 2064. Minimized Maximum of Products Distributed to Any Store 🧠🚀"  
categories: [LeetCode, Programming]  
---


_Distributing products to stores efficiently is no easy task, especially when aiming to keep each store's load as balanced as possible! In this problem, we’re challenged with minimizing the maximum products any one store gets. By carefully using a combination of binary search and validation techniques, we can solve this seemingly complex problem with ease!_

### Problem Statement 📝

You have `n` specialty retail stores and `m` types of products, each with a specific quantity. Given an array `quantities` where `quantities[i]` represents the number of products of the ith type, your task is to distribute all products to the retail stores, following these rules:

1. Each store can receive at most one product type.
2. Each store may receive any amount of that type (from 0 up to the total quantity of that type).
3. After distribution, let `x` be the maximum products any store has. Your goal is to minimize `x`.

The output is the smallest possible value of `x`.

---

### Examples with Detailed Walkthroughs

#### Example 1:

- **Input**: `n = 6`, `quantities = [11, 6]`
- **Output**: `3`

**Explanation**:
1. Distribute `11` products of type `0` across `4` stores as `[2, 3, 3, 3]`.
2. Distribute `6` products of type `1` across `2` stores as `[3, 3]`.
3. Here, the maximum products in any one store is `3`.

---

#### Example 2:

- **Input**: `n = 7`, `quantities = [15, 10, 10]`
- **Output**: `5`

**Explanation**:
1. Distribute `15` products of type `0` to `3` stores: `[5, 5, 5]`.
2. Distribute `10` products of type `1` to `2` stores: `[5, 5]`.
3. Distribute `10` products of type `2` to `2` stores: `[5, 5]`.
4. The maximum products in any one store is `5`.

---

### Edge Cases 🚧

1. **Single Store** (`n = 1`): If there’s only one store and large quantities, the output should match the largest quantity value.
2. **Single Product Type** (`m = 1`): Distribute all quantities to `n` stores evenly.
3. **Equal Stores and Quantities** (`n = m`): Ideal distribution is one product per store.
4. **High Quantity with Many Stores**: Check if distribution still maintains the minimized maximum load across stores.

---

### Solution Outline 🛠️

The core of this solution revolves around **binary search** combined with a validation helper function. Let’s break it down step-by-step.

1. **Binary Search on the Maximum Load (`x`)**:
   - Define a feasible range for `x` starting from `1` up to the maximum quantity among all types.
   - Use binary search to find the smallest feasible `x`.

2. **Validation Function (`canDistribute`)**:
   - For a given `x`, determine if all products can be distributed without any store holding more than `x` items.

#### Basic Solution 💡

Using binary search to find the minimized maximum load `x` requires `O(m * log(max(quantities)))` time, where:
- `m` is the length of `quantities`.
- `log(max(quantities))` accounts for the binary search depth.

---

### Optimized Solution 🚀

The binary search approach already serves as the optimized solution since it combines binary search with the `canDistribute` helper function. This function efficiently checks if a given maximum load `x` can handle the product distribution across stores.

#### Solution Code

```python
def minimized_maximum(n, quantities):
    # Helper function to check if a given max load x is feasible
    def can_distribute(x):
        stores_needed = 0
        for quantity in quantities:
            # Calculate number of stores required for this product type at load x
            stores_needed += (quantity + x - 1) // x  # This is equivalent to ceiling division
            if stores_needed > n:  # If we need more stores than available
                return False
        return True
    
    # Binary search for the smallest possible maximum load x
    left, right = 1, max(quantities)
    while left < right:
        mid = (left + right) // 2
        if can_distribute(mid):
            right = mid  # mid might be the answer, search lower
        else:
            left = mid + 1  # mid is too small, search higher
    return left
```

---

### Explanation 🔍

1. **Binary Search Setup**: Start with `left = 1` (smallest possible load) and `right = max(quantities)` (highest possible load if only one store).
2. **Middle Point Evaluation**: For each midpoint `mid`, use `can_distribute(mid)` to check if it’s possible to distribute all products such that no store holds more than `mid` items.
3. **Adjust Search Range**:
   - If `can_distribute(mid)` returns `True`, then `mid` is a valid maximum, so set `right = mid`.
   - Otherwise, set `left = mid + 1`, making `mid` too small for a valid distribution.

4. **Return**: Once `left` meets `right`, `left` is the minimized maximum load we’re seeking. 

---

### Complexity Analysis 📊

- **Time Complexity**: `O(m * log(max(quantities)))`, where `m` is the number of product types. This results from binary search (`log(max(quantities))`) with each validation check (`m`).
- **Space Complexity**: `O(1)`, since we use constant space outside of input data.

This solution efficiently finds the smallest possible `x` while meeting all conditions for distributing products to stores! Let me know if you’d like more examples or additional details on any part.

### Example Walkthrough 🔍

#### Example 2: `n = 7`, `quantities = [15, 10, 10]`

1. **Binary Search Initialization**:
   - Set `left = 1` and `right = max(quantities) = 15`.

2. **First Iteration**:
   - `mid = (1 + 15) // 2 = 8`
   - **Validate `x = 8`**:
     - **Type 0**: `15` products, needs `ceil(15/8) = 2` stores.
     - **Type 1**: `10` products, needs `ceil(10/8) = 2` stores.
     - **Type 2**: `10` products, needs `ceil(10/8) = 2` stores.
     - Total stores required: `2 + 2 + 2 = 6`, which is <= `n = 7`.
   - **Result**: We can distribute with `x = 8`, so adjust `right = 8`.

3. **Second Iteration**:
   - `mid = (1 + 8) // 2 = 4`
   - **Validate `x = 4`**:
     - **Type 0**: `15` products, needs `ceil(15/4) = 4` stores.
     - **Type 1**: `10` products, needs `ceil(10/4) = 3` stores.
     - **Type 2**: `10` products, needs `ceil(10/4) = 3` stores.
     - Total stores required: `4 + 3 + 3 = 10`, which is > `n = 7`.
   - **Result**: Distribution fails with `x = 4`, so adjust `left = 5`.

4. **Third Iteration**:
   - `mid = (5 + 8) // 2 = 6`
   - **Validate `x = 6`**:
     - **Type 0**: `15` products, needs `ceil(15/6) = 3` stores.
     - **Type 1**: `10` products, needs `ceil(10/6) = 2` stores.
     - **Type 2**: `10` products, needs `ceil(10/6) = 2` stores.
     - Total stores required: `3 + 2 + 2 = 7`, which matches `n = 7`.
   - **Result**: We can distribute with `x = 6`, so adjust `right = 6`.

5. **Final Iteration**:
   - `mid = (5 + 6) // 2 = 5`
   - **Validate `x = 5`**:
     - **Type 0**: `15` products, needs `ceil(15/5) = 3` stores.
     - **Type 1**: `10` products, needs `ceil(10/5) = 2` stores.
     - **Type 2**: `10` products, needs `ceil(10/5) = 2` stores.
     - Total stores required: `3 + 2 + 2 = 7`, matching `n = 7`.
   - **Result**: We can distribute with `x = 5`, so adjust `right = 5`.

### Conclusion 🎯

With binary search and a helper function, we efficiently find the minimized maximum `x` for distributing products. This approach reduces complex distributions into manageable steps, providing an elegant solution to a challenging problem!

**Key Takeaways**:
1. **Binary Search** is essential in narrowing the solution range.
2. **Helper Functions** simplify complex conditions for verification.

This problem shows how to apply binary search beyond basic numeric search problems, turning what would otherwise be a challenging distribution task into an efficient algorithmic solution.
