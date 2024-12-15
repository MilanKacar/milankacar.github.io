---
layout: post  
title: "#103 📈 1792. Maximum Average Pass Ratio 🚀🧠"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [Array, Greedy, Heap (Priority Queue)]
---


## 🚀 Maximizing the Average Pass Ratio 🚀

### Short Description

Imagine you're in charge of a school with several classes preparing for final exams. You have a group of extra brilliant students ready to boost any class's pass rate. The challenge? Assign them strategically to maximize the average pass ratio. Let's dive into this problem and solve it using a mix of greedy and heap-based techniques! 🕵️✨

---

### Problem Statement

You are given a 2D integer array `classes`, where:
- `classes[i] = [passi, totali]` represents a class where:
  - `passi` is the number of students currently passing the exam.
  - `totali` is the total number of students in the class.

Additionally, you are provided with an integer `extraStudents`, which represents the number of additional brilliant students you can assign to any class. Each extra student is guaranteed to pass the exam in the class they are assigned to.

The **pass ratio** of a class is defined as:

$$
\text{Pass Ratio} = \frac{\text{passi}}{\text{totali}}
$$

The **average pass ratio** is the sum of the pass ratios of all classes divided by the total number of classes.

Your task is to assign the `extraStudents` to classes such that the average pass ratio across all classes is maximized.

#### Constraints:
- $$1 \leq \text{classes.length} \leq 10^5$$
- $$1 \leq \text{passi} \leq \text{totali} \leq 10^5$$
- $$1 \leq \text{extraStudents} \leq 10^5$$

#### Example 1:

**Input:**
```
classes = [[1, 2], [3, 5], [2, 2]]
extraStudents = 2
```
**Output:**
```
0.78333
```
**Explanation:**
Assign the two extra students to the first class. The average pass ratio will be:
$$
\text{Average Pass Ratio} = \frac{\frac{3}{4} + \frac{3}{5} + \frac{2}{2}}{3} = 0.78333
$$

#### Example 2:

**Input:**
```
classes = [[2, 4], [3, 9], [4, 5], [2, 10]]
extraStudents = 4
```
**Output:**
```
0.53485
```

---

### Insights and Strategy 🤯

Here’s how we can expand the **Insights and Strategy** section with more depth and actionable ideas:

---

### Insights and Strategy 🤯

#### Key Observations:
1. **Diminishing Returns Principle**:
   - Adding a student to a class improves its pass ratio, but subsequent additions yield smaller incremental gains. This is due to the mathematical behavior of fractions: the numerator grows linearly, while the denominator grows more significantly as it accounts for the total size.

2. **Maximizing Marginal Gain**:
   - The optimal strategy for maximizing the average pass ratio involves prioritizing classes where adding a student results in the largest improvement in pass ratio. This is mathematically captured by:
     $$
     \Delta = \frac{\text{passi} + 1}{\text{totali} + 1} - \frac{\text{passi}}{\text{totali}}
     $$

3. **Heap Utility**:
   - Using a max-heap (priority queue) allows us to efficiently keep track of the class with the maximum potential improvement. This ensures that every extra student is allocated where they create the most value.

#### Why Greedy Works:
The greedy approach ensures that we maximize the overall average pass ratio by focusing on local optima—adding students to the most impactful class at each step. Since the goal is to maximize a global average, this method aligns well with the problem's diminishing returns characteristic.

---

#### Steps to Craft the Strategy:
1. **Calculate Initial Marginal Gains**:
   - Compute the improvement in pass ratio for every class if one student is added.
   - Prioritize classes based on these improvements using a max-heap.

2. **Allocate Extra Students**:
   - Iteratively assign each extra student to the class with the highest potential improvement.
   - Recalculate the marginal gain for the updated class and reinsert it into the heap.

3. **Reevaluate and Optimize**:
   - After all extra students are allocated, recalculate the final average pass ratio.

---

#### Decision Points:
1. **Heap Initialization**:
   - Each class’s potential improvement is calculated only once initially, ensuring $$O(n \log n)$$ complexity.

2. **Dynamic Updates**:
   - After allocating a student, we only update the marginal gain for the modified class, maintaining heap efficiency.

3. **Final Averaging**:
   - Once all extra students are allocated, the pass ratios are directly summed, avoiding additional computational overhead.

---

#### Alternative Considerations:
1. **Brute Force**:
   - Assign students in every possible combination and calculate the resulting average pass ratio. However, this approach is computationally infeasible for large inputs due to exponential complexity.

2. **Dynamic Programming**:
   - Explore states where the number of extra students and their allocations are considered. While this might work for smaller constraints, it quickly becomes unwieldy as the input size grows.

---

### Solution and Walkthrough 🔬

#### Optimized Solution:
We'll use a **heap-based greedy approach** to tackle the problem efficiently.

#### Python Code:

```python
from heapq import heappush, heappop

class Solution:
    def maxAverageRatio(self, classes, extraStudents):
        # Define a function to calculate the improvement in pass ratio
        def improvement(passi, totali):
            return (passi + 1) / (totali + 1) - passi / totali

        # Create a max-heap using negative of improvement for easy sorting
        heap = []
        for passi, totali in classes:
            heappush(heap, (-improvement(passi, totali), passi, totali))

        # Assign extra students
        for _ in range(extraStudents):
            gain, passi, totali = heappop(heap)
            passi += 1
            totali += 1
            heappush(heap, (-improvement(passi, totali), passi, totali))

        # Calculate the final average pass ratio
        total_ratio = sum(passi / totali for _, passi, totali in heap)
        return total_ratio / len(classes)
```

#### Explanation of Code:
1. **Heap Initialization:**
   - For every class, calculate the potential improvement in pass ratio from adding one student.
   - Push the negative of this improvement along with `passi` and `totali` into a heap (to simulate a max-heap).

2. **Assign Extra Students:**
   - Pop the class with the highest improvement from the heap.
   - Add an extra student to that class and recalculate its improvement.
   - Push the updated values back into the heap.

3. **Compute Final Average:**
   - After all students are assigned, calculate the total pass ratio of all classes and return the average.

#### Time Complexity:
- **Heap Initialization:** $$O(n \log n)$$
- **Extra Student Allocation:** $$O(k \log n)$$, where $$k$$ is the number of extra students.
- **Final Calculation:** $$O(n)$$
- **Overall:** $$O((n + k) \log n)$$

#### Space Complexity:
- The heap stores $$n$$ elements: $$O(n)$$.

---

### Example Walkthrough 🎨

#### Input:
```
classes = [[2, 4], [3, 9], [4, 5], [2, 10]]
extraStudents = 4
```

#### Step 1: Initialize the Heap
Calculate the initial improvement for each class and populate the heap:
- Class 1: $$[2, 4]$$ $$\to \Delta = 0.1$$
- Class 2: $$[3, 9]$$ $$\to \Delta = 0.074$$
- Class 3: $$[4, 5]$$ $$\to \Delta = 0.033$$
- Class 4: $$[2, 10]$$ $$\to \Delta = 0.048$$

Heap (sorted by $$-\Delta$$):
```
[(-0.1, 2, 4), (-0.074, 3, 9), (-0.033, 4, 5), (-0.048, 2, 10)]
```

#### Step 2: Assign Extra Students
1. Pop $$[2, 4]$$, add a student: $$[3, 5]$$.
   - Recalculate $$\Delta = 0.066$$.
   - Push back into the heap.
2. Pop $$[3, 9]$$, add a student: $$[4, 10]$$.
   - Recalculate $$\Delta = 0.066$$.
   - Push back into the heap.
3. Repeat until all extra students are allocated.

#### Step 3: Calculate Final Average
Sum up the pass ratios and compute the average:
```
\text{Average Pass Ratio} = \frac{\frac{3}{5} + \frac{4}{10} + \frac{4}{5} + \frac{2}{10}}{4} = 0.53485
```

---

### Edge Cases 🚫
1. **All Pass Ratios Already 1:**
   - Input: `[[1, 1], [2, 2]], extraStudents = 3`
   - Output: `1.0`

2. **All Extra Students in One Class:**
   - Input: `[[1, 10], [9, 10]], extraStudents = 5`
   - Output: Assign all to the first class.

3. **Large Input Sizes:** Ensure the algorithm runs efficiently for $$10^5$$ classes and students.

---

### Conclusion 🙌
This problem is a fantastic example of using greedy strategies with data structures like heaps to optimize results efficiently. The diminishing returns property simplifies decision-making, ensuring every additional student is allocated where they make the most impact. With the heap, this process remains scalable for large inputs. Happy coding! 🎉

