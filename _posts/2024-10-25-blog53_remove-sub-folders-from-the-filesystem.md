---
layout: post
title: "#53 📂✨ 1233. Remove Sub-Folders from the Filesystem 🧠🚀"
categories: [LeetCode, Programming]
---

Managing files can be a hassle, especially when we want to clean up our directory structure! This problem is all about identifying and removing redundant sub-folders in a list. Let’s explore the problem and find out how to tackle it efficiently. 😊

---

### Problem Statement 💼

We’re given a list of folders as `folder`, and our task is to remove all sub-folders from this list. A **sub-folder** of `folder[j]` is any folder that starts with `folder[j]` and has an additional sub-path following it.

**Examples:**

1. `/a/b` is a sub-folder of `/a`
2. `/c/d/e` is a sub-folder of `/c/d`
3. `/a` and `/b/c` are not sub-folders of each other

After removing all sub-folders, we return the list of folders in any order.

#### Constraints 📋

- `1 <= folder.length <= 4 * 10^4`
- `2 <= folder[i].length <= 100`
- All `folder[i]` paths start with `'/'` and contain only lowercase English letters.
- Each folder name is unique.

---

### Examples 🌟

#### Example 1

**Input**: `folder = ["/a", "/a/b", "/c/d", "/c/d/e", "/c/f"]`

**Output**: `["/a", "/c/d", "/c/f"]`

**Explanation**: Folders `"/a/b"` and `"/c/d/e"` are sub-folders of `"/a"` and `"/c/d"` respectively, so we remove them.

#### Example 2

**Input**: `folder = ["/a", "/a/b/c", "/a/b/d"]`

**Output**: `["/a"]`

**Explanation**: Both `"/a/b/c"` and `"/a/b/d"` are sub-folders of `"/a"`, so we keep only `"/a"`.

---

### Solution Approaches 🔍

Our goal is to identify and remove sub-folders, and this requires efficiently sorting and traversing the folder paths to spot and filter out these sub-folders.

### Initial Approach 🛠️ (Using Sorting)

1. **Sort the Folders Alphabetically**: Sorting the list alphabetically lets us group sub-folders right next to their parent folders. For example, sorting `["/a", "/a/b", "/c/d", "/c/f"]` places `"/a/b"` directly after `"/a"`.
  
2. **Use a List to Store Results**: Starting with an empty result list, we iterate over each folder in the sorted list:
    - For each folder, if it’s not a sub-folder of the previous folder in our results, we add it to the results.
    - We check if a folder is a sub-folder by ensuring the current path starts with the last added folder path plus a `'/'`.

#### Code Implementation 🖥️

```python
def removeSubfolders(folder):
    # Step 1: Sort folders alphabetically
    folder.sort()
    result = []

    # Step 2: Iterate and add only non-sub-folders
    for f in folder:
        # If result is empty or current folder isn't a sub-folder of the last added folder
        if not result or not f.startswith(result[-1] + '/'):
            result.append(f)

    return result
```

#### Complexity Analysis ⏱️

- **Time Complexity**: Sorting takes \(O(N \log N)\), and iterating through the list takes \(O(N)\), making it \(O(N \log N)\) in total.
- **Space Complexity**: The result list requires \(O(N)\) additional space.

---

### Optimized Solution 🚀 (Using a Trie)

While the sorting approach is solid, a **Trie (prefix tree)** can help us manage the folder hierarchy directly and avoid sub-folder checks by design. Here’s how:

1. **Build a Trie**: Each level in the Trie represents a level in the folder path. If we come across a folder that’s already in the Trie, we skip adding sub-folders under that folder.

2. **Add Folders Efficiently**: For each folder, traverse down the Trie path:
   - If we find an existing path that’s a complete folder path (i.e., not a parent node), we stop adding sub-paths.
   - If we reach the end of the folder without encountering a complete folder path, we mark it and avoid further sub-paths under it.

3. **Extract Results from the Trie**: Once the Trie is built, traverse it to gather only the folders, ignoring sub-folders by structure.

#### Code Implementation 🖥️

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_folder = False

def add_to_trie(root, path):
    current = root
    for part in path.split('/')[1:]:
        if part not in current.children:
            current.children[part] = TrieNode()
        current = current.children[part]
        if current.is_folder:
            return False
    current.is_folder = True
    current.children = {}  # clear any potential subfolders
    return True

def removeSubfolders(folder):
    root = TrieNode()
    result = []

    for f in sorted(folder):
        if add_to_trie(root, f):
            result.append(f)

    return result
```

#### Complexity Analysis ⏱️

- **Time Complexity**: Building and querying the Trie takes \(O(N \cdot M)\), where \(M\) is the maximum length of the folder path.
- **Space Complexity**: The Trie requires \(O(N \cdot M)\) space for storing all unique folder paths.

---

### Walkthrough Example 🏃

Let’s go through Example 1 with both approaches:

**Input**: `folder = ["/a", "/a/b", "/c/d", "/c/d/e", "/c/f"]`

#### Using Sorting Approach

1. **Sorted List**: `["/a", "/a/b", "/c/d", "/c/d/e", "/c/f"]`
2. **Result List**:
   - Add `"/a"` (no parent folder conflict)
   - Skip `"/a/b"` (it’s a sub-folder of `"/a"`)
   - Add `"/c/d"` (no parent folder conflict)
   - Skip `"/c/d/e"` (it’s a sub-folder of `"/c/d"`)
   - Add `"/c/f"` (no parent folder conflict)

**Final Output**: `["/a", "/c/d", "/c/f"]`

#### Using Trie Approach

1. **Trie Construction**:
   - Add `"/a"` to the Trie.
   - Skip `"/a/b"` (it’s a sub-folder of `"/a"`)
   - Add `"/c/d"` to the Trie.
   - Skip `"/c/d/e"` (it’s a sub-folder of `"/c/d"`)
   - Add `"/c/f"` to the Trie.

2. **Traverse Trie for Results**: Collecting nodes from the Trie gives `["/a", "/c/d", "/c/f"]`.

---

### Conclusion 🎉

Both methods achieve the desired outcome by efficiently eliminating sub-folders, and each has its unique advantages. The sorting-based solution is intuitive and straightforward, making it an excellent choice for simpler cases. The Trie approach, though more complex, shines with its precise handling of folder hierarchy, offering potential advantages in managing deep or complex directory structures.
