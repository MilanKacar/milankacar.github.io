---
layout: post
title: "#12 Tackling DataLemur SQL Challenges for Data Science Interviews 🧠💡"
categories: LeetCode
---

## 🚀 Designing an All O(1) Data Structure with Python 🐍

Hello, fellow coders! 👋 Today, we’re diving into a powerful data structure challenge: implementing an **All O(1) Data Structure**. The goal is to support operations to manage and query key counts efficiently, all in constant time **O(1)**. Let's break it down step by step! 🧠

### Problem Statement 📜

We need to implement a class `AllOne` that can efficiently perform the following operations:

1. **`inc(key)`**: Increments the count of a key. If the key doesn't exist, add it with count 1.
2. **`dec(key)`**: Decrements the count of a key. If the count becomes 0, remove it from the structure.
3. **`getMaxKey()`**: Returns any key with the maximum count. If no keys exist, return an empty string `""`.
4. **`getMinKey()`**: Returns any key with the minimum count. If no keys exist, return an empty string `""`.

Each operation must run in **O(1)** time, meaning it should be optimized for maximum performance!

### Example Walkthrough 📝

```python
all_one = AllOne()
all_one.inc("hello")     # 'hello' count is now 1
all_one.inc("hello")     # 'hello' count is now 2
print(all_one.getMaxKey())  # Output: "hello" (max count key)
print(all_one.getMinKey())  # Output: "hello" (min count key)
all_one.inc("leet")      # 'leet' count is now 1
print(all_one.getMaxKey())  # Output: "hello" (still max count)
print(all_one.getMinKey())  # Output: "leet" (now min count)
```

### 🛠 Basic Approach: Dictionary & List (Inefficient) 🧑‍💻

The first approach you might think of is using a **dictionary** to track the counts and a **list** to get the min and max keys. Here's the basic idea:

1. **Increment** and **decrement** operations can be easily performed using a dictionary.
2. However, finding the **min** and **max** key requires scanning through all keys in the dictionary, leading to an **O(n)** operation in the worst case, where **n** is the number of keys.

```python
class AllOne:

    def __init__(self):
        self.key_count = {}  # To store the count of each key

    def inc(self, key: str) -> None:
        # Increment the count of the key
        if key in self.key_count:
            self.key_count[key] += 1
        else:
            self.key_count[key] = 1

    def dec(self, key: str) -> None:
        # Decrement the count of the key and remove if count is 0
        if key in self.key_count:
            self.key_count[key] -= 1
            if self.key_count[key] == 0:
                del self.key_count[key]

    def getMaxKey(self) -> str:
        # Return the key with the maximum count
        if not self.key_count:
            return ""
        return max(self.key_count, key=self.key_count.get)

    def getMinKey(self) -> str:
        # Return the key with the minimum count
        if not self.key_count:
            return ""
        return min(self.key_count, key=self.key_count.get)

```

#### Time Complexity ⏳

- **Increment** and **decrement**: O(1) using the dictionary.
- **Get min/max key**: O(n) since we have to scan the dictionary to find the min/max count.

Thus, this approach **fails** to meet the problem's requirement for constant-time operations. We need a more efficient solution!

### 🔥 Optimized Approach: HashMaps with Set 🎯

To meet the O(1) requirement, we can use two hashmaps and a clever trick:

- **`key_count`**: A dictionary to store the count for each key.
- **`count_keys`**: A dictionary to store a set of keys for each count.
- We maintain two variables to keep track of the current **min_count** and **max_count**.

By organizing our data this way, we can increment, decrement, and fetch the min/max keys in **constant time**.

### Optimized Python Code 🐍

```python
class AllOne:

    def __init__(self):
        self.key_count = {}  # Map key to its count
        self.count_keys = {}  # Map count to the set of keys with that count
        self.min_count = None  # Track the current minimum count
        self.max_count = None  # Track the current maximum count

    def inc(self, key: str) -> None:
        current_count = self.key_count.get(key, 0)
        new_count = current_count + 1
        
        # Update key_count for the key
        self.key_count[key] = new_count
        
        # Remove the key from the old count set if it existed
        if current_count > 0:
            self.count_keys[current_count].remove(key)
            if not self.count_keys[current_count]:
                del self.count_keys[current_count]
        
        # Add the key to the new count set
        if new_count not in self.count_keys:
            self.count_keys[new_count] = set()
        self.count_keys[new_count].add(key)
        
        # Update min_count and max_count
        if self.min_count is None or new_count == 1:
            self.min_count = 1
        if self.max_count is None or new_count > self.max_count:
            self.max_count = new_count
        if current_count == self.min_count and current_count not in self.count_keys:
            self.min_count = new_count

    def dec(self, key: str) -> None:
        if key not in self.key_count:
            return

        current_count = self.key_count[key]
        new_count = current_count - 1

        # Remove the key from the current count set
        self.count_keys[current_count].remove(key)
        if not self.count_keys[current_count]:
            del self.count_keys[current_count]
        
        if new_count > 0:
            self.key_count[key] = new_count
            if new_count not in self.count_keys:
                self.count_keys[new_count] = set()
            self.count_keys[new_count].add(key)
        else:
            del self.key_count[key]

        # Update min_count and max_count
        if current_count == self.max_count and not self.count_keys.get(self.max_count):
            self.max_count = new_count if new_count > 0 else None
        if current_count == self.min_count and not self.count_keys.get(self.min_count):
            self.min_count = new_count if new_count > 0 else (min(self.count_keys.keys()) if self.count_keys else None)

    def getMaxKey(self) -> str:
        if not self.max_count:
            return ""
        return next(iter(self.count_keys[self.max_count]))

    def getMinKey(self) -> str:
        if not self.min_count:
            return ""
        return next(iter(self.count_keys[self.min_count]))
```

### Detailed Explanation 🧠

1. **Incrementing (`inc`)**:
   - If the key is new, initialize its count at 1.
   - Otherwise, increase its count and update the count-key mapping.
   - Adjust `min_count` and `max_count` accordingly.

2. **Decrementing (`dec`)**:
   - Decrease the key's count or remove it if the count reaches 0.
   - Update `min_count` and `max_count` dynamically.

3. **Fetching Min/Max (`getMaxKey`, `getMinKey`)**:
   - We efficiently retrieve the key with the max or min count by looking at the relevant set of keys from the `count_keys` dictionary.

### Time Complexity ⏳

- **Increment/Decrement**: O(1) – Constant time for adjusting counts and updating the dictionary and set.
- **GetMinKey/GetMaxKey**: O(1) – We directly access the key with the minimum or maximum count.

### Conclusion 🎯

By leveraging **hashmaps** and **sets**, we can achieve **constant-time operations** for all required functions:

1. **Basic Approach**: Using a list for min/max keys results in O(n) complexity, which is inefficient.
2. **Optimized Approach**: Hashmaps allow us to perform all operations in **O(1)** time, making it the ideal solution.

Now you're ready to use this optimized data structure in your projects! Happy coding! 🎉
