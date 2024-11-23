---
layout: post  
title: "#84 🔄 1861. Rotating the Box 🧠🚀"  
categories: [LeetCode, Programming]
tags: [array, coding]
---

Are you ready to tackle a **fun matrix problem** that combines the effects of **gravity** and **rotation**? This problem is a mix of geometry and simulation, guaranteed to get your problem-solving gears turning! Imagine stones falling through empty spaces, obstacles blocking their path, and the whole box spinning 90° clockwise. Let’s dive into this visual and engaging challenge! 🚀  

---

## Problem Statement 📜  

You are given an `m x n` matrix called **`box`**, representing the **side-view of a box**. Each cell in the box can contain one of the following:  
- `"#"`: A **stone** 💎. Stones fall under the effect of gravity.  
- `"*"`: A **stationary obstacle** 🧱. Obstacles block falling stones.  
- `"."`: An **empty space** 🌌. Stones can fall through these spaces.  

After all the stones have "fallen," the box is **rotated 90° clockwise**. Your task is to return the new configuration of the box after this transformation.  

---

### Rules of the Game 🛠️  
1. Stones fall vertically within their rows until blocked by obstacles, other stones, or the bottom of the box.  
2. Obstacles remain fixed and unaffected by gravity.  
3. The entire box is rotated **90° clockwise**, so rows become columns!  
4. The input constraints ensure no stone is "floating" when the box is rotated.  

---

## Example Scenarios 🌟  

Let’s explore a few examples to better understand the problem.  

### Example 1:  

#### Input:  
```
box = [["#", ".", "#"]]
```  

#### Output:  
```
[
 [ "." ],
 [ "#" ],
 [ "#" ]
]
```  

#### Explanation:  
1. The initial box looks like this:  
   ```
   [ ["#", ".", "#"] ]
   ```  
2. Since there are no obstacles or stones below, each stone stays in place after applying gravity.  
3. After a 90° clockwise rotation, we get:  
   ```
   [
    [ "." ],
    [ "#" ],
    [ "#" ]
   ]
   ```

---

### Example 2:  

#### Input:  
```
box = [
    ["#", ".", "*", "."],
    ["#", "#", "*", "."]
]
```  

#### Output:  
```
[
 [ "#", "." ],
 [ "#", "#" ],
 [ "*", "*" ],
 [ ".", "." ]
]
```  

#### Explanation:  
1. The initial box looks like this:  
   ```
   [
    ["#", ".", "*", "."],
    ["#", "#", "*", "."]
   ]
   ```  
2. Apply gravity: Stones in each row fall to the rightmost available position:  
   ```
   [
    ["#", ".", "*", "."],
    ["#", "#", "*", "."]
   ]
   ```  
3. Rotate the box 90° clockwise:  
   ```
   [
    [ "#", "." ],
    [ "#", "#" ],
    [ "*", "*" ],
    [ ".", "." ]
   ]
   ```  

---

### Example 3: Larger Input  

#### Input:  
```
box = [
    ["#", "#", "*", ".", "*", "."],
    ["#", "#", "#", "*", ".", "."],
    ["#", "#", "#", ".", "#", "."]
]
```  

#### Output:  
```
[
 [ ".", "#", "#" ],
 [ ".", "#", "#" ],
 [ "#", "#", "*" ],
 [ "#", "*", "." ],
 [ "#", ".", "*" ],
 [ "#", ".", "." ]
]
```  

---

## Edge Cases 🧩  

When solving problems, always consider edge cases!  

### Case 1: Empty Box  
#### Input:  
```
box = [[".", ".", "."]]
```  
#### Output:  
```
[
 [ "." ],
 [ "." ],
 [ "." ]
]
```  

### Case 2: All Stones  
#### Input:  
```
box = [["#", "#", "#"]]
```  
#### Output:  
```
[
 [ "#" ],
 [ "#" ],
 [ "#" ]
]
```  

### Case 3: Single Column  
#### Input:  
```
box = [
    ["#"],
    ["*"],
    ["."]
]
```  
#### Output:  
```
[ ["#", "*", "."] ]
```  

---

## Step-by-Step Solution 🛠️  

### 1️⃣ Basic Idea: Gravity + Rotation  

#### Step 1: Simulating Gravity 🎢  
In this step, we simulate stones falling downward in each row. Starting from the **rightmost column**, we:  
1. Track the position of the **last empty space**.  
2. When encountering a stone (`#`), move it to the **last empty space**, then update the empty space's position.  
3. Reset the pointer when encountering an obstacle (`*`).  

#### Step 2: Rotating the Matrix 🔄  
For a 90° clockwise rotation, the cell `(i, j)` in the rotated box corresponds to the cell `(m - 1 - j, i)` in the original box.  

---

## Python Implementation 🐍  

Here’s the full solution:  

```python
def rotateTheBox(box):
    m, n = len(box), len(box[0])  # Dimensions of the box
    
    # Step 1: Apply gravity to each row
    for row in box:
        empty = len(row) - 1  # Track the last empty space in the row
        for col in range(len(row) - 1, -1, -1):
            if row[col] == "#":
                row[col], row[empty] = ".", "#"  # Move stone to the empty space
                empty -= 1
            elif row[col] == "*":
                empty = col - 1  # Reset pointer at obstacles
    
    # Step 2: Rotate the box 90 degrees clockwise
    rotated_box = [[None] * m for _ in range(n)]
    for i in range(m):
        for j in range(n):
            rotated_box[j][m - 1 - i] = box[i][j]
    
    return rotated_box
```

---

## Time Complexity ⏱️  

The time complexity of this solution is **O(m * n)**:  
- **Simulating gravity**: Each cell is visited once, so it takes **O(m * n)**.  
- **Rotating the box**: Again, each cell is visited once, resulting in **O(m * n)**.  

**Overall Time Complexity**: **O(m * n)**.  
**Space Complexity**: **O(m * n)** due to the rotated box storage.

---

## Detailed Example Walkthrough 🖼️  

Let’s revisit **Example 3** and dive deeper into the steps.  

#### Input:  
```
box = [
    ["#", "#", "*", ".", "*", "."],
    ["#", "#", "#", "*", ".", "."],
    ["#", "#", "#", ".", "#", "."]
]
```  

### Step 1: Apply Gravity 🎢  
**Row 1**:  
- Start from the rightmost cell:  
  - Move the stone at position 0 to position 1.  
  - Encounter obstacle `*` at position 2, reset pointer.  

**Result after gravity**:  
```
[
 ["#", "#", "*", ".", "*", "."],
 ["#", "#", "#", "*", ".", "."],
 ["#", "#", "#", ".", "#", "."]
]
```  

### Step 2: Rotate the Box 🔄  
We apply the rotation rule for each cell. The output is:  
```
[
 [ ".", "#", "#" ],
 [ ".", "#", "#" ],
 [ "#", "#", "*" ],
 [ "#", "*", "." ],
 [ "#", ".", "*" ],
 [ "#", ".", "." ]
]
```

---

## Conclusion 🎉  

This problem combines **matrix transformations** and **simulation** in a unique way. By breaking it into two clear steps—simulating gravity and rotating the matrix—we can efficiently solve the problem.  

### Key Takeaways:  
1. Always break down complex problems into smaller, manageable steps.  
2. Use mathematical formulas for transformations like rotations.  
3. Optimize by avoiding redundant operations.  

Keep practicing, and you’ll master these techniques in no time! Happy coding! 🎈
