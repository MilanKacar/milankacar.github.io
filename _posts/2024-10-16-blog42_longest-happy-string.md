---
layout: post  
title: "#42 2938. Longest Happy String 🧠🚀"
categories: [LeetCode, Programming]
difficulty: Medium
tags: [String, Greedy, Heap (Priority Queue)]
---

In today's problem, we need to create the **longest happy string** using three letters: `'a'`, `'b'`, and `'c'`. A happy string avoids repeating any letter more than twice in a row. The challenge lies in constructing the longest string while respecting this rule, and we'll explore both a **basic approach** and an **optimized approach**!

### Problem Statement 💡

A string is called **happy** if it meets the following conditions:
1. It only contains the letters `'a'`, `'b'`, and `'c'`.
2. It does **not** contain any of the substrings `"aaa"`, `"bbb"`, or `"ccc"`.
3. The string contains at most `a` occurrences of the letter `'a'`, at most `b` occurrences of `'b'`, and at most `c` occurrences of `'c'`.

Given three integers `a`, `b`, and `c`, return the **longest possible happy string**. If there are multiple such strings, return any of them. If it's not possible to create a valid string, return an empty string `""`.

---

### Basic Approach 🤔

#### Key Idea:
The **basic approach** tries to build the string in a **round-robin** fashion, where we repeatedly add the characters `'a'`, `'b'`, and `'c'` while respecting the constraints. However, this approach may not always yield the longest string, as it doesn't always prioritize the most frequent character.

#### Steps:
1. **Round-robin Addition**: Repeatedly add the characters in a cyclic manner: `'a'`, `'b'`, `'c'`.
2. **Check for Consecutive Triples**: After each addition, check if three consecutive characters of the same type are added. If so, skip that character for the next iteration.

#### Python Code 🐍

```python
def longestHappyString_basic(a: int, b: int, c: int) -> str:
    result = []
    
    # Keep track of the remaining count of each character
    counts = {'a': a, 'b': b, 'c': c}
    
    while True:
        added = False
        for char in sorted(counts, key=lambda x: -counts[x]):  # Sort by the count in descending order
            if len(result) >= 2 and result[-1] == result[-2] == char:
                continue  # Skip if adding would create three consecutive characters
            if counts[char] > 0:
                result.append(char)
                counts[char] -= 1
                added = True
                break
        if not added:
            break  # If no characters can be added without violating the condition, stop
    
    return ''.join(result)
```

### Time Complexity ⏱️

- **Time Complexity**: $$O(n)$$, where $$n = a + b + c$$ is the total number of characters.
- **Space Complexity**: $$O(n)$$, for storing the result string.

#### Example Walkthrough 🛣️

Let’s walk through an example using the basic approach:

#### Input: `a = 1, b = 1, c = 7`

1. Start by adding the character `'c'` since it has the most occurrences: `**c**`.
2. Then, add `'b'`, followed by `'a'` in a round-robin fashion: `**cb**`, `**cba**`.
3. Since `'c'` has the most occurrences, add it again: `**cbac**`.
4. Continue alternating characters while avoiding three consecutive same letters.

This will yield a valid string like `"cbacbac"` but **might not be the longest possible string**.

---

### Optimized Greedy Approach 🚀

#### Key Idea:
The **optimized approach** uses a greedy algorithm to always select the character with the highest remaining count, but it checks for the condition where adding the same character might form three consecutive letters. If that happens, it switches to the next most frequent character.

#### Steps:
1. **Greedy Selection**: Always append the character with the most remaining occurrences, unless it forms three consecutive identical characters.
2. **Switch Characters**: If the most frequent character would violate the "no triplet" rule, switch to the next available character.
3. **Continue Until Exhaustion**: Repeat this process until no valid characters can be added.

#### Python Code 🐍

```python
import heapq

def longestHappyString(a: int, b: int, c: int) -> str:
    result = []
    max_heap = []
    
    # Push all characters with their counts into a max heap (negative counts for max behavior)
    for count, char in [(-a, 'a'), (-b, 'b'), (-c, 'c')]:
        if count != 0:  # Only consider non-zero counts
            heapq.heappush(max_heap, (count, char))
    
    while max_heap:
        # Pop the character with the highest remaining count
        count1, char1 = heapq.heappop(max_heap)
        
        # If the last two characters in the result are the same as the current char, switch
        if len(result) >= 2 and result[-1] == result[-2] == char1:
            if not max_heap:
                break  # If no other characters are available, stop
            
            # Use the second most frequent character
            count2, char2 = heapq.heappop(max_heap)
            result.append(char2)
            count2 += 1  # Decrease count (count2 is negative)
            
            if count2:
                heapq.heappush(max_heap, (count2, char2))  # Push it back if there are more remaining
            
            # Push the current char back for later use
            heapq.heappush(max_heap, (count1, char1))
        else:
            # Otherwise, use the current character
            result.append(char1)
            count1 += 1  # Decrease count (count1 is negative)
            
            if count1:
                heapq.heappush(max_heap, (count1, char1))  # Push it back if there are more remaining
    
    return ''.join(result)
```

### Time Complexity ⏱️
- **Time Complexity**: $$O(n \log 3)$$, which simplifies to $$O(n)$$, where $$n = a + b + c$$ is the total number of characters. We only work with three possible characters at any given time, so heap operations are constant-time with respect to character count.
- **Space Complexity**: $$O(n)$$ for the result string and the heap.

---

### Walkthrough 🛣️

Let's walk through the optimized approach step by step:

#### Example: Input `a = 1`, `b = 1`, `c = 7`

1. **Heap Initialization**: 
   - Push the counts of `'a'`, `'b'`, and `'c'` into the max-heap: `[-1, 'a']`, `[-1, 'b']`, and `[-7, 'c']`.

2. **First Iteration**:
   - Pop the character with the highest count, `'c'`, and append it to the result: `**c**`.
   - Since there are more `'c'`s left (count is now `-6`), push `'c'` back into the heap.

3. **Second Iteration**:
   - Pop `'c'` again, add it to the result: `**cc**`.
   - Now, we cannot add another `'c'` because it would form `"ccc"`. So, pop `'a'`, append it: `**cca**`.

4. **Further Iterations**:
   - The process continues, alternating between `'c'` and the next available character to ensure no three consecutive letters are added.
   - Finally, you get a valid string like `"ccaccbcc"`, which is one of the longest happy strings.

---

### Conclusion 🏁

While the basic approach is straightforward, it relies on re-checking the characters in a round-robin manner, and even though it's simple, it could lead to slightly more iterations when characters need to be skipped frequently. The optimized approach is more systematic, ensuring the longest string by always selecting the most frequent character, which can result in fewer total iterations and no unnecessary sorting.

Both approaches have the same theoretical time complexity of `O(n)`, but the optimized approach is more efficient in practice because it minimizes re-checking and ensures maximal progress in each step.

So, while the basic approach is simple, the optimized version is typically faster and more efficient for larger input sizes due to the strategic character selection using the heap.
