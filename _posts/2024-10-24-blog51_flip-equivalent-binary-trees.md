---
layout: post
title: "#51 🌳 951. Flip Equivalent Binary Trees 🧠🚀"
categories: [LeetCode, Programming]
---

Welcome back, coders! 👨‍💻👩‍💻 Today, we're going to dive deep into a fascinating problem that involves binary trees, recursion, and some nifty tree manipulations – specifically, **flip operations**! 🌲🔀🌲 In this post, we'll learn how to check if two binary trees are **flip equivalent**, meaning that by flipping some nodes' children, we can make one tree look like the other. 🌀

Get ready to explore this problem 😄

---

## Problem Statement 📝

Given two binary trees `root1` and `root2`, we want to determine if they are **flip equivalent**. A binary tree is flip equivalent to another if we can make them identical by flipping (swapping the left and right child subtrees) at some nodes. A flip operation can be done any number of times, and it can be performed at any node in the tree.

Our task is to write a function that returns `True` if the two trees are flip equivalent, and `False` otherwise.

### Example 1:

```
Input: root1 = [1,2,3,4,5,6,null,null,null,7,8], 
       root2 = [1,3,2,null,6,4,5,null,null,null,null,8,7]
Output: true
Explanation: We flipped at nodes with values 1, 3, and 5.
```

### Example 2:

```
Input: root1 = [], root2 = []
Output: true
```

### Example 3:

```
Input: root1 = [], root2 = [1]
Output: false
```

---

## 🌟 Basic Solution 🌟

The problem is essentially about comparing the structure of two binary trees while allowing for **flips**. This is a natural candidate for recursion because each node comparison depends on the outcome of its child nodes. Here’s how we can break it down:

1. **Base Case 1:** If both `root1` and `root2` are `None`, we return `True`. This means we've reached the end of both trees, so they are trivially equivalent. 
   
2. **Base Case 2:** If one of the trees is `None` and the other is not, they can't be equivalent, so we return `False`.

3. **Value Comparison:** If the values of the nodes don’t match, return `False`.

4. **Recursive Check on Children:** We need to check if the subtrees of both trees are either:
   - **Equivalent without flipping**, i.e., the left child of `root1` matches the left child of `root2` and the right child of `root1` matches the right child of `root2`.
   - **Equivalent after flipping**, i.e., the left child of `root1` matches the right child of `root2`, and the right child of `root1` matches the left child of `root2`.

If either condition holds, then the trees are flip equivalent!

### Python Code 🐍

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def flip_equiv(root1: TreeNode, root2: TreeNode) -> bool:
    # Base case: both are None
    if not root1 and not root2:
        return True
    # One is None, but the other isn't
    if not root1 or not root2:
        return False
    # Values don't match
    if root1.val != root2.val:
        return False

    # Check if children are equivalent directly or after flipping
    return (flip_equiv(root1.left, root2.left) and flip_equiv(root1.right, root2.right)) or \
           (flip_equiv(root1.left, root2.right) and flip_equiv(root1.right, root2.left))
```

### Explanation:

- The function `flip_equiv` takes two tree roots (`root1` and `root2`) as input.
- It checks the base cases first (both `None`, or one `None` while the other is not).
- If the current nodes have the same value, it recursively checks the subtrees:
  - Either the subtrees are directly equivalent, or they become equivalent after a flip.

---

## ⏱️ Time Complexity of Basic Solution ⏱️

The time complexity of this solution is **O(min(N1, N2))**, where `N1` and `N2` are the number of nodes in the two trees.

- In the worst case, we will need to traverse both trees entirely, comparing each node with its counterpart in the other tree.
- Since we only need to compare corresponding nodes and their children once, the solution is efficient for most cases.

---

## 🌟 Optimized Solution 🌟

Since the basic solution already efficiently solves the problem using recursion, no further optimization is necessary. The recursion explores all possible ways of flipping the child nodes and ensures that each node is visited exactly once.

The algorithm has an early exit mechanism with the base cases that allow us to stop further computation as soon as we know that the trees are not flip equivalent. This makes the recursive approach both optimal and easy to understand.

Since the time complexity remains **O(min(N1, N2))**, we can conclude that the recursive solution is already optimal for this problem.

---

## 🧑‍🏫 Example Walkthrough with Higher Numbers 🧑‍🏫

Let’s walk through an example to understand the solution better. We’ll use a larger example to illustrate how the recursion works in more depth.

### Example:

```
root1 = [10, 20, 30, 40, 50, null, null, 70, 80, 90, 100]
root2 = [10, 30, 20, null, null, 40, 50, 100, 90, 80, 70]
```

### Step-by-Step Walkthrough:

1. **Compare root nodes (`10`)**:
   - Both trees have the root node with value `10`. Since the values are equal, we proceed to compare the children.

2. **Compare left and right children of `10`**:
   - In `root1`, the left child is `20` and the right child is `30`.
   - In `root2`, the left child is `30` and the right child is `20`.
   - The children don’t match directly, so we try flipping: we compare `root1`'s left child (`20`) with `root2`'s right child (`20`), and `root1`'s right child (`30`) with `root2`'s left child (`30`). Both comparisons hold, so we proceed!

3. **Compare subtrees rooted at `20`**:
   - In `root1`, node `20` has left child `40` and right child `50`.
   - In `root2`, node `20` has left child `50` and right child `40`.
   - Again, these don’t match directly, but after flipping, we see that:
     - `root1.left.left` (node `40`) matches `root2.left.right` (node `40`), and
     - `root1.left.right` (node `50`) matches `root2.left.left` (node `50`).
   - Both subtrees are equivalent after flipping, so we move on.

4. **Compare subtrees rooted at `50`**:
   - In `root1`, node `50` has left child `90` and right child `100`.
   - In `root2`, node `50` has left child `100` and right child `90`.
   - These don’t match directly, but after flipping, we see:
     - `root1.left.right.left` (node `90`) matches `root2.left.left.right` (node `90`), and
     - `root1.left.right.right` (node `100`) matches `root2.left.left.left` (node `100`).
   - The subtrees are equivalent after flipping.

At each level of the tree, the recursive approach checks if a flip operation is needed, ensuring that the trees are compared properly.

---

## 🏁 Conclusion 🏁

The **Flip Equivalent Binary Trees** problem demonstrates the power of recursion in solving problems involving hierarchical data structures like trees. By using recursion, we can systematically explore all possible flip operations and determine whether two trees are equivalent.

We learned that:
- The base case checks for `None` values and unequal node values are essential to stop the recursion early.
- The recursive approach efficiently handles the comparison by exploring both direct and flipped possibilities.
- The time complexity of the solution is **O(min(N1, N2))**, making it optimal.

In the end, understanding how to manipulate binary trees and apply recursive thinking will make you a stronger problem solver in many areas of computer science! 💡🌳
