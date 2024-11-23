---
layout: post  
title: "#60 🏁 2463. Minimum Total Distance Traveled 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Hard
tags: [Array, Dynamic Programming, Sorting]
---

Imagine you’re managing a repair line where broken robots 🛠️ keep moving along a straight road to reach their assigned factories 🏭. The goal? To minimize the total distance that all the robots need to travel to get repaired! This problem is challenging, but by breaking it down, you can use Dynamic Programming (DP) to achieve an optimal solution. Let’s get into it! 🧑‍💻👇

## 📜 Problem Statement

There are several **robots** and **factories** positioned along a one-dimensional **X-axis**. Each robot begins in a unique position, as does each factory. All robots are broken and need repair. You are given:

- An array `robot`, where each element `robot[i]` represents the position of the `i-th` robot.
- A 2D array `factory`, where each element `factory[j] = [positionj, limitj]` denotes the position of the `j-th` factory and the maximum number of robots it can repair, `limitj`.

Your goal is to **minimize the total distance** that all robots travel to reach a factory. The following conditions apply:

- Robots can be assigned to any factory as long as the factory hasn’t reached its repair limit.
- Robots can move in either positive or negative directions.
- If a factory has reached its limit, the robots continue moving as if the factory does not exist.
- The input guarantees that all robots can eventually be repaired.

The objective is to **return the minimum total distance traveled** by all robots to be repaired.

---

## 🧩 Example 1

```plaintext
Input: robot = [0, 4, 6], factory = [[2, 2], [6, 2]]
Output: 4
Explanation:
1. The robot at position 0 moves to the factory at position 2. Distance: |2 - 0| = 2.
2. The robot at position 4 moves to the same factory at position 2. Distance: |2 - 4| = 2.
3. The robot at position 6 is already at factory position 6, so distance = 0.

Total minimum distance = 4.
```

---

## 🧩 Example 2

```plaintext
Input: robot = [1, -1], factory = [[-2, 1], [2, 1]]
Output: 2
Explanation:
1. The robot at position 1 moves to the factory at position 2. Distance: |2 - 1| = 1.
2. The robot at position -1 moves to the factory at position -2. Distance: |(-2) - (-1)| = 1.

Total minimum distance = 2.
```

---

## 🚀 Approach to Solution

### Step-by-Step Breakdown

1. **Sort Both Arrays by Position**:
   - Sorting both arrays (`robot` and `factory`) by position allows us to think in terms of "closest" options for each robot, minimizing the distance they travel.

2. **Dynamic Programming (DP) with Caching**:
   - Use dynamic programming (DP) to break down the problem: we define `dp(i, j, k)` as the minimum distance to repair robots from index `i` to the end, using factories from index `j` to the end, where the current factory at `j` has repaired `k` robots.

3. **Defining Recursive Transitions**:
   - We can either:
     - **Skip a factory** (go to the next factory without assigning the current robot).
     - **Use a factory** to repair a robot, if the factory still has capacity.
   
4. **Combining Results Using Minimum**:
   - For each robot-factory pair, we choose the option that minimizes the distance traveled.

### 🚧 Edge Cases to Consider

1. **Exact Fit of Robots to Factories**: When the number of robots exactly matches the sum of all factory limits.
2. **Robots Positioned Closely Together**: Testing scenarios where robots are clustered near one or more factories.
3. **High Limits for Factories**: Cases where some factories have very high limits, allowing them to repair most or all robots.
4. **Large Gaps Between Positions**: Factories and robots placed at large intervals to test maximum travel costs.

---

## 🔑 Solution Code

Let’s implement this using Python. We’ll start by sorting `robot` and `factory` lists and then create our recursive DP function with memoization.

### Optimized Code Solution

```python
import functools
import math

def minimum_total_distance(robot, factory):
    robot.sort()
    factory.sort()

    @functools.lru_cache(None)
    def dp(i, j, k):
        """
        Returns the minimum distance to fix robots[i..n) using factories[j..n),
        where factory[j] has already fixed 'k' robots.
        """
        # Base case: All robots are fixed
        if i == len(robot):
            return 0
        
        # Base case: All factories are exhausted
        if j == len(factory):
            return math.inf

        # Option 1: Skip current factory and try next one
        skip_factory = dp(i, j + 1, 0)

        # Option 2: Use current factory if it has capacity
        position, limit = factory[j]
        use_factory = dp(i + 1, j, k + 1) + abs(robot[i] - position) if limit > k else math.inf

        # Return the minimum distance of either skipping or using the factory
        return min(skip_factory, use_factory)

    return dp(0, 0, 0)
```

---

## 📈 Time Complexity Analysis

### Explanation of Complexity:

1. **Sorting Step**: Sorting both `robot` and `factory` arrays takes \(O(n \log n + m \log m)\), where \(n\) is the length of `robot` and \(m\) is the length of `factory`.
2. **Recursive DP Calculation**: The DP function has up to \(O(n \times m)\) recursive calls, each storing up to `limit[j]` values due to the caching of intermediate results.

The final complexity is **O(n \times m \times max(limit))**, making this approach efficient enough for the input constraints.

---

## 🔍 Example Walkthrough with Larger Numbers

Imagine we have `robot = [100, 300, 500, 700]` and `factory = [[200, 2], [600, 2]]`. Here's how it works:

1. **Sort Robots and Factories**:
   - Sorted `robot`: `[100, 300, 500, 700]`
   - Sorted `factory`: `[[200, 2], [600, 2]]`

2. **DP Computation**:
   - Starting with the first robot at position `100`, we evaluate both factory options.
   - Robot at `100` travels to the closest factory, `200`, traveling a distance of `100`.
   - Robot at `300` travels to factory `200` as well, making the additional distance `100`.
   - We then continue this logic, ensuring that we don't exceed each factory's repair limit while minimizing total travel.

3. **Result**:
   - In this scenario, the DP recursion calculates a minimum travel distance considering all robots while honoring the factory limits.

---

## 🧪 Edge Cases Explored

1. **One Robot and Multiple Factories**:
   - `robot = [0]`, `factory = [[-100, 1], [100, 1]]`
   - Expected output: Choose the closest factory, minimizing distance to `100`.

2. **All Robots Equidistant from One Factory**:
   - Robots equidistant from a central factory with high capacity to test balanced scenarios.

3. **Sparse Factories, Densely Packed Robots**:
   - If robots are clustered in one area and factories are spread out, our DP approach efficiently chooses nearest factories within limits.

---

## ✨ Conclusion

This approach leverages sorting, dynamic programming, and memoization to solve a complex pathfinding and assignment problem in minimal time. By breaking down the steps, we minimize each robot's travel based on available capacity. This solution scales effectively to handle the upper bounds of input constraints and is optimal for scenarios requiring resource allocation with travel cost minimization.
