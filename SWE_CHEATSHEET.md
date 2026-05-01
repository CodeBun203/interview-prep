# SWE Interview Cheat Sheet — Pattern Bible

> Language: Python (patterns are language-agnostic). Every section covers: what it is, when to use it, the template, complexity, and variations.

---

## Table of Contents

1. [Two Pointers](#1-two-pointers)
2. [Sliding Window](#2-sliding-window)
3. [Hash Maps and Sets](#3-hash-maps-and-sets)
4. [Binary Search](#4-binary-search)
5. [Linked Lists](#5-linked-lists)
6. [Trees and Binary Tree Traversal](#6-trees-and-binary-tree-traversal)
7. [Recursion and the Call Stack](#7-recursion-and-the-call-stack)
8. [DFS — Depth-First Search](#8-dfs--depth-first-search)
9. [BFS — Breadth-First Search](#9-bfs--breadth-first-search)
10. [Graphs](#10-graphs)
11. [Dynamic Programming](#11-dynamic-programming)
12. [Backtracking](#12-backtracking)
13. [Heaps and Priority Queues](#13-heaps-and-priority-queues)
14. [Monotonic Stack and Queue](#14-monotonic-stack-and-queue)
15. [Sorting](#15-sorting)
16. [Union-Find (Disjoint Set)](#16-union-find-disjoint-set)
17. [Tries](#17-tries)
18. [Greedy](#18-greedy)
19. [Bit Manipulation](#19-bit-manipulation)
20. [String Patterns](#20-string-patterns)
21. [Complexity Reference](#21-complexity-reference)
22. [Pattern Recognition Guide](#22-pattern-recognition-guide)

---

## 1. Two Pointers

### What it is
Two indices moving through a data structure. The movement strategy is what varies — they can move toward each other, in the same direction at different speeds, or one can lead the other.

### When to use
- Sorted array, find pair with target sum/difference
- Remove duplicates in-place
- Palindrome check
- Container with most water
- Any problem where you'd naively use nested loops but the data is sorted

### Trigger words
"sorted array", "pair", "in-place", "no extra space", "two numbers that sum to"

---

### Variant 1: Opposite Ends (Converging)

Start one pointer at the left, one at the right. Move them toward each other based on a condition.

```python
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return [left, right]
        elif s < target:
            left += 1   # need larger sum, move left pointer right
        else:
            right -= 1  # need smaller sum, move right pointer left
    return []
```

**Complexity:** O(n) time, O(1) space

**Why it works:** The array is sorted. If the sum is too small, the only way to increase it is to advance the left pointer. If too large, retreat the right pointer. You never miss a valid pair because you would have seen it before eliminating it.

---

### Variant 2: Same Direction (Fast/Slow — Floyd's Cycle Detection)

Both pointers start at the head. Fast moves 2 steps, slow moves 1. If there's a cycle, they meet inside it.

```python
def has_cycle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False

def find_cycle_start(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    else:
        return None  # no cycle
    # Phase 2: reset one pointer to head, advance both by 1
    slow = head
    while slow != fast:
        slow = slow.next
        fast = slow.next  # BUG in original: should be fast.next
    # Actually:
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    return slow  # cycle start
```

**Why phase 2 works:** The math proves that after the meeting point, the distance from head to cycle start equals the distance from meeting point to cycle start. Reset one pointer to head, advance both by 1 — they meet exactly at the cycle start.

---

### Variant 3: Same Direction (Remove Duplicates / Write Pointer)

`slow` marks the "write position" for valid elements. `fast` scans ahead.

```python
def remove_duplicates(nums):
    if not nums:
        return 0
    slow = 0
    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:
            slow += 1
            nums[slow] = nums[fast]
    return slow + 1  # length of deduplicated array
```

**Complexity:** O(n) time, O(1) space

---

### Variant 4: Triple Pointers (3Sum)

Fix one element, then run two-pointer on the remaining sorted subarray.

```python
def three_sum(nums):
    nums.sort()
    result = []
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue  # skip duplicates for the fixed element
        left, right = i + 1, len(nums) - 1
        while left < right:
            s = nums[i] + nums[left] + nums[right]
            if s == 0:
                result.append([nums[i], nums[left], nums[right]])
                while left < right and nums[left] == nums[left + 1]:
                    left += 1   # skip duplicates
                while left < right and nums[right] == nums[right - 1]:
                    right -= 1  # skip duplicates
                left += 1
                right -= 1
            elif s < 0:
                left += 1
            else:
                right -= 1
    return result
```

**Complexity:** O(n²) time, O(1) space (excluding output)

---

## 2. Sliding Window

### What it is
A subarray or substring of variable or fixed length that "slides" across the input. Instead of recomputing the window from scratch each step, you add the new right element and remove the old left element.

### When to use
- Longest/shortest subarray/substring satisfying some condition
- Maximum/minimum sum subarray of size k
- Any problem asking about contiguous elements

### Trigger words
"subarray", "substring", "contiguous", "window", "at most k", "longest", "shortest"

---

### Variant 1: Fixed Size Window

```python
def max_sum_subarray_k(nums, k):
    window_sum = sum(nums[:k])
    max_sum = window_sum
    for i in range(k, len(nums)):
        window_sum += nums[i] - nums[i - k]  # add right, remove left
        max_sum = max(max_sum, window_sum)
    return max_sum
```

**Complexity:** O(n) time, O(1) space

---

### Variant 2: Variable Size Window (Expand/Contract)

Expand right pointer freely. Contract left pointer when the window violates the constraint.

```python
def longest_substring_without_repeating(s):
    char_set = set()
    left = 0
    max_len = 0
    for right in range(len(s)):
        while s[right] in char_set:       # window invalid: shrink from left
            char_set.remove(s[left])
            left += 1
        char_set.add(s[right])            # window valid: expand right
        max_len = max(max_len, right - left + 1)
    return max_len
```

**Complexity:** O(n) time, O(k) space where k is the character set size

---

### Variant 3: Variable Size with Frequency Map

When you need to track character counts in the window (e.g., anagram problems).

```python
from collections import defaultdict

def min_window_substring(s, t):
    need = defaultdict(int)
    for c in t:
        need[c] += 1
    missing = len(t)  # total characters still needed
    left = 0
    start, end = 0, float('inf')

    for right, c in enumerate(s):
        if need[c] > 0:
            missing -= 1
        need[c] -= 1

        if missing == 0:  # valid window found
            # shrink from left while still valid
            while need[s[left]] < 0:
                need[s[left]] += 1
                left += 1
            if right - left < end - start:
                start, end = left, right
            # break the window to find the next one
            need[s[left]] += 1
            missing += 1
            left += 1

    return s[start:end + 1] if end != float('inf') else ""
```

**Complexity:** O(|s| + |t|) time, O(|t|) space

---

### The Mental Model

```
right pointer: always moves forward, never back
left pointer: only moves forward when the window is invalid

window size = right - left + 1
window valid condition = problem-specific
```

---

## 3. Hash Maps and Sets

### What it is
A hash map (dict in Python) maps keys to values in O(1) average time. A hash set stores unique keys in O(1) average time.

### When to use
- Count frequencies
- Track "have I seen this before?"
- Two-sum pattern (complement lookup)
- Grouping elements by a property
- Caching/memoization

### Trigger words
"count", "frequency", "duplicate", "unique", "pair", "complement", "group by", "seen before"

---

### Pattern 1: Two Sum (Complement Lookup)

```python
def two_sum(nums, target):
    seen = {}  # value -> index
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []
```

**Why:** For each element, instead of scanning the rest of the array for its complement (O(n²)), look it up in the map in O(1).

**Complexity:** O(n) time, O(n) space

---

### Pattern 2: Frequency Count

```python
from collections import Counter

def is_anagram(s, t):
    return Counter(s) == Counter(t)

def top_k_frequent(nums, k):
    count = Counter(nums)
    # bucket sort by frequency: index = frequency, value = list of nums
    bucket = [[] for _ in range(len(nums) + 1)]
    for num, freq in count.items():
        bucket[freq].append(num)
    result = []
    for i in range(len(bucket) - 1, 0, -1):
        result.extend(bucket[i])
        if len(result) >= k:
            return result[:k]
```

---

### Pattern 3: Prefix Sum with Hash Map

Find the number of subarrays with sum equal to k.

```python
def subarray_sum_equals_k(nums, k):
    count = 0
    prefix_sum = 0
    seen = {0: 1}  # prefix_sum -> number of times seen
    for num in nums:
        prefix_sum += num
        # if (prefix_sum - k) was seen before, there's a subarray summing to k
        count += seen.get(prefix_sum - k, 0)
        seen[prefix_sum] = seen.get(prefix_sum, 0) + 1
    return count
```

**Why:** prefix_sum[j] - prefix_sum[i] = k means the subarray [i+1..j] sums to k. For each j, we want to know how many i's satisfy prefix_sum[i] = prefix_sum[j] - k.

---

### Pattern 4: Grouping

```python
from collections import defaultdict

def group_anagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = tuple(sorted(s))  # canonical form
        groups[key].append(s)
    return list(groups.values())
```

---

### Set Operations

```python
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

a | b   # union:        {1, 2, 3, 4, 5, 6}
a & b   # intersection: {3, 4}
a - b   # difference:   {1, 2}
a ^ b   # symmetric difference: {1, 2, 5, 6}

# Membership check is O(1):
3 in a  # True
```

---

## 4. Binary Search

### What it is
Repeatedly halve the search space by comparing the middle element to the target. Requires the search space to be sorted or monotonic (i.e., there's a clear "left half is wrong, right half might be right" decision).

### When to use
- Sorted array, find element or insertion point
- "Find the minimum X such that condition(X) is true"
- Rotated sorted arrays
- Any search where the answer space is ordered

### Trigger words
"sorted", "find minimum/maximum", "search in O(log n)", "rotated array", "peak element"

---

### Template 1: Classic — Find Exact Target

```python
def binary_search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2  # avoids integer overflow (matters in other languages)
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1  # not found
```

---

### Template 2: Find Leftmost Position (Lower Bound)

Find the first index where `nums[i] >= target`.

```python
def lower_bound(nums, target):
    left, right = 0, len(nums)  # right is len, not len-1
    while left < right:          # strict less-than
        mid = (left + right) // 2
        if nums[mid] < target:
            left = mid + 1
        else:
            right = mid          # do NOT do mid - 1
    return left  # insertion point; nums[left] >= target
```

---

### Template 3: Search on Answer Space

The most powerful binary search pattern. When the answer is a number in a range and you can write a function `feasible(mid)` that returns True/False.

```python
# Example: minimum speed to eat all bananas in h hours
def min_eating_speed(piles, h):
    import math

    def feasible(speed):
        return sum(math.ceil(p / speed) for p in piles) <= h

    left, right = 1, max(piles)
    while left < right:
        mid = (left + right) // 2
        if feasible(mid):
            right = mid       # mid might be the answer, don't exclude it
        else:
            left = mid + 1    # mid is too slow, exclude it
    return left
```

**The mental model:**
- If `feasible(mid)` is True and you're minimizing: `right = mid` (could still go lower)
- If `feasible(mid)` is False and you're minimizing: `left = mid + 1` (too small, eliminate)
- Reverse for maximizing

---

### Template 4: Rotated Sorted Array

```python
def search_rotated(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = (left + right) // 2
        if nums[mid] == target:
            return mid
        # Determine which half is sorted
        if nums[left] <= nums[mid]:  # left half is sorted
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:  # right half is sorted
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1
```

**Complexity:** O(log n) time, O(1) space for all variants

---

## 5. Linked Lists

### Structure

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

### When to use pointers vs. extra structures
- **O(1) space required:** use pointer manipulation (fast/slow, dummy node, reversal)
- **O(n) space allowed:** copy to array, solve, rebuild

---

### Pattern 1: Dummy Node

Attach a dummy head before the real head. Eliminates edge cases when the head itself might be removed or replaced.

```python
def remove_nth_from_end(head, n):
    dummy = ListNode(0)
    dummy.next = head
    fast = slow = dummy
    # advance fast by n+1 steps
    for _ in range(n + 1):
        fast = fast.next
    # move both until fast hits end
    while fast:
        fast = fast.next
        slow = slow.next
    slow.next = slow.next.next  # remove the node
    return dummy.next
```

---

### Pattern 2: In-Place Reversal

```python
def reverse_list(head):
    prev, curr = None, head
    while curr:
        next_node = curr.next   # save next
        curr.next = prev        # reverse the pointer
        prev = curr             # advance prev
        curr = next_node        # advance curr
    return prev  # prev is now the new head

def reverse_between(head, left, right):
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    # walk to the node just before position left
    for _ in range(left - 1):
        prev = prev.next
    curr = prev.next
    # reverse from left to right
    for _ in range(right - left):
        next_node = curr.next
        curr.next = next_node.next
        next_node.next = prev.next
        prev.next = next_node
    return dummy.next
```

---

### Pattern 3: Merge Two Sorted Lists

```python
def merge_two_lists(l1, l2):
    dummy = ListNode(0)
    curr = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    curr.next = l1 or l2  # attach remaining
    return dummy.next
```

---

### Pattern 4: Find Middle (Fast/Slow)

```python
def find_middle(head):
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # for even-length list, returns second middle
```

Use this to split a list in half for merge sort, palindrome check, etc.

---

### Pattern 5: Palindrome Check

```python
def is_palindrome(head):
    # Find middle
    slow, fast = head, head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    # Reverse second half
    prev, curr = None, slow
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    # Compare
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    return True
```

---

## 6. Trees and Binary Tree Traversal

### Structure

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

### The Three DFS Orders

```
        1
       / \
      2   3
     / \
    4   5
```

| Order | Sequence | When to Use |
|-------|----------|------------|
| Inorder (L → Root → R) | 4, 2, 5, 1, 3 | BST operations — gives sorted output |
| Preorder (Root → L → R) | 1, 2, 4, 5, 3 | Serialize/copy a tree |
| Postorder (L → R → Root) | 4, 5, 2, 3, 1 | Delete a tree, evaluate expression tree |

---

### Recursive Traversals

```python
def inorder(root):
    if not root:
        return []
    return inorder(root.left) + [root.val] + inorder(root.right)

def preorder(root):
    if not root:
        return []
    return [root.val] + preorder(root.left) + preorder(root.right)

def postorder(root):
    if not root:
        return []
    return postorder(root.left) + postorder(root.right) + [root.val]
```

---

### Iterative Inorder (using explicit stack)

Important for when recursion depth is constrained or when you need to pause traversal.

```python
def inorder_iterative(root):
    result, stack = [], []
    curr = root
    while curr or stack:
        while curr:           # go as far left as possible
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()    # process node
        result.append(curr.val)
        curr = curr.right     # move to right subtree
    return result
```

---

### Level-Order (BFS on a tree)

```python
from collections import deque

def level_order(root):
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```

---

### Common Tree Patterns

**Height of tree:**
```python
def height(root):
    if not root:
        return 0
    return 1 + max(height(root.left), height(root.right))
```

**Diameter (longest path between any two nodes):**
```python
def diameter(root):
    max_diameter = [0]
    def depth(node):
        if not node:
            return 0
        left = depth(node.left)
        right = depth(node.right)
        max_diameter[0] = max(max_diameter[0], left + right)
        return 1 + max(left, right)
    depth(root)
    return max_diameter[0]
```

**Lowest Common Ancestor:**
```python
def lca(root, p, q):
    if not root or root == p or root == q:
        return root
    left = lca(root.left, p, q)
    right = lca(root.right, p, q)
    if left and right:
        return root   # p and q are in different subtrees
    return left or right
```

**Validate BST:**
```python
def is_valid_bst(root, min_val=float('-inf'), max_val=float('inf')):
    if not root:
        return True
    if root.val <= min_val or root.val >= max_val:
        return False
    return (is_valid_bst(root.left, min_val, root.val) and
            is_valid_bst(root.right, root.val, max_val))
```

**Complexity:** O(n) time, O(h) space where h is tree height (O(log n) balanced, O(n) worst case)

---

## 7. Recursion and the Call Stack

### What it is
A function that calls itself with a smaller version of the problem. Every recursive solution has:
1. **Base case** — the problem is small enough to solve directly
2. **Recursive case** — break the problem into smaller subproblems and combine results

### When to use
- Problem has natural self-similar substructure (trees, graphs, divide and conquer)
- "Generate all possibilities" (backtracking)
- Mathematical induction fits the problem

### The three mental models

**1. Trust the recursion (the leap of faith)**
When writing a recursive function, assume it already works for smaller inputs. Only think about: "given that `f(smaller)` works, how do I use it to solve `f(n)`?"

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    # Trust that these calls correctly sort each half
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)  # just focus on this step
```

**2. The call stack as a to-do list**
Each recursive call pushes a frame onto the stack. The frames unwind in LIFO order when base cases are hit.

```python
def factorial(n):
    # Stack frames: factorial(5) → factorial(4) → factorial(3) → factorial(2) → factorial(1)
    # Unwind:       5*24          ←  4*6           ←  3*2          ←  2*1          ← 1
    if n <= 1:
        return 1
    return n * factorial(n - 1)
```

**3. Return values vs. side effects**
Some recursions pass results back up the call stack (return values). Others accumulate into a shared object (side effects). Know which you're using.

```python
# Return value style (functional, cleaner)
def path_sum(root, target):
    if not root:
        return False
    if not root.left and not root.right:
        return root.val == target
    return (path_sum(root.left, target - root.val) or
            path_sum(root.right, target - root.val))

# Side-effect style (mutable accumulator)
def all_paths(root, target, path=[], result=[]):
    if not root:
        return
    path.append(root.val)
    if not root.left and not root.right and root.val == target:
        result.append(list(path))   # copy path
    all_paths(root.left, target - root.val, path, result)
    all_paths(root.right, target - root.val, path, result)
    path.pop()   # UNDO the choice — this is backtracking
```

---

### Memoization (Top-Down DP)

When the same subproblem appears multiple times in the recursion tree, cache the result.

```python
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n < 2:
        return n
    return fib(n - 1) + fib(n - 2)
```

Without memoization: O(2^n). With memoization: O(n). The recursion tree collapses from exponential to linear because each unique subproblem is computed only once.

---

## 8. DFS — Depth-First Search

### What it is
Explore as deep as possible along each branch before backtracking. Implemented with a stack — either the call stack (recursion) or an explicit stack.

### When to use
- Explore all paths (find if a path exists, find all paths)
- Detect cycles
- Topological sort
- Connected components
- Any "explore until you find something" problem

---

### DFS on a Graph (Iterative)

```python
def dfs_iterative(graph, start):
    visited = set()
    stack = [start]
    while stack:
        node = stack.pop()
        if node in visited:
            continue
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                stack.append(neighbor)
    return visited
```

---

### DFS on a Graph (Recursive)

```python
def dfs_recursive(graph, node, visited=None):
    if visited is None:
        visited = set()
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs_recursive(graph, neighbor, visited)
    return visited
```

---

### DFS on a 2D Grid

The grid itself is the graph. Each cell is a node; neighbors are the 4 (or 8) adjacent cells.

```python
def num_islands(grid):
    if not grid:
        return 0
    rows, cols = len(grid), len(grid[0])
    count = 0

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '#'  # mark visited by mutating — restore later if needed
        dfs(r + 1, c)
        dfs(r - 1, c)
        dfs(r, c + 1)
        dfs(r, c - 1)

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                dfs(r, c)
                count += 1
    return count
```

**Complexity:** O(rows × cols) time, O(rows × cols) space (call stack)

---

### Preorder vs Postorder Work in DFS

```python
def dfs(node):
    # PREORDER: work happens before recursing
    process(node)
    for child in node.children:
        dfs(child)

def dfs(node):
    for child in node.children:
        dfs(child)
    # POSTORDER: work happens after all children return
    process(node)
```

Use **preorder** when you need parent info to process a node (path tracking, top-down).
Use **postorder** when you need children's results to process a node (height, subtree sums).

---

## 9. BFS — Breadth-First Search

### What it is
Explore all neighbors at the current depth before going deeper. Implemented with a queue (FIFO). Naturally finds the **shortest path** in an unweighted graph.

### When to use
- Shortest path in unweighted graph or grid
- Level-order traversal
- "Minimum number of steps/moves/operations"
- Spreading processes (infection, fire, word ladder)

### Trigger words
"minimum steps", "shortest path", "minimum moves", "level by level", "nearest"

---

### BFS Template

```python
from collections import deque

def bfs(graph, start, end):
    queue = deque([(start, 0)])  # (node, distance)
    visited = {start}
    while queue:
        node, dist = queue.popleft()
        if node == end:
            return dist
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    return -1  # not reachable
```

---

### BFS on a Grid (Shortest Path)

```python
def shortest_path_grid(grid, start, end):
    rows, cols = len(grid), len(grid[0])
    directions = [(0,1),(0,-1),(1,0),(-1,0)]
    queue = deque([(start[0], start[1], 0)])
    visited = {start}
    while queue:
        r, c, dist = queue.popleft()
        if (r, c) == end:
            return dist
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and (nr, nc) not in visited and grid[nr][nc] != '#':
                visited.add((nr, nc))
                queue.append((nr, nc, dist + 1))
    return -1
```

---

### Multi-Source BFS

Start BFS from multiple sources simultaneously. Use when you want the distance from any source to each cell (e.g., "distance to nearest gate", "01 matrix").

```python
def nearest_gate(rooms):
    rows, cols = len(rooms), len(rooms[0])
    GATE = 0
    queue = deque()
    # Seed all gates at once
    for r in range(rows):
        for c in range(cols):
            if rooms[r][c] == GATE:
                queue.append((r, c))
    directions = [(0,1),(0,-1),(1,0),(-1,0)]
    while queue:
        r, c = queue.popleft()
        for dr, dc in directions:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and rooms[nr][nc] == float('inf'):
                rooms[nr][nc] = rooms[r][c] + 1
                queue.append((nr, nc))
```

---

### BFS vs DFS Decision

| Question | Use |
|----------|-----|
| Does a path exist? | Either (DFS simpler) |
| Shortest path (unweighted)? | BFS |
| All possible paths? | DFS |
| Detect cycle? | Either |
| Topological sort? | DFS (or BFS with Kahn's) |
| Connected components? | Either |

---

## 10. Graphs

### Representations

```python
# Adjacency list (most common in interviews)
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 3],
    3: [1, 2]
}

# Build from edge list
from collections import defaultdict

def build_graph(n, edges, directed=False):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        if not directed:
            graph[v].append(u)
    return graph

# Adjacency matrix (dense graphs, O(1) edge lookup)
matrix = [[0] * n for _ in range(n)]
matrix[u][v] = 1
```

---

### Topological Sort (DFS-based)

Valid only for DAGs (directed acyclic graphs). Gives an ordering of nodes such that all edges point forward.

```python
def topological_sort(n, edges):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)

    visited = set()
    temp = set()  # currently in recursion stack (cycle detection)
    order = []

    def dfs(node):
        if node in temp:
            raise ValueError("Cycle detected")
        if node in visited:
            return
        temp.add(node)
        for neighbor in graph[node]:
            dfs(neighbor)
        temp.remove(node)
        visited.add(node)
        order.append(node)  # postorder: add after all descendants

    for node in range(n):
        if node not in visited:
            dfs(node)

    return order[::-1]  # reverse postorder = topological order
```

---

### Topological Sort (BFS / Kahn's Algorithm)

```python
from collections import deque

def topo_sort_kahn(n, edges):
    indegree = [0] * n
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        indegree[v] += 1

    queue = deque([i for i in range(n) if indegree[i] == 0])
    order = []
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    if len(order) != n:
        return []  # cycle exists
    return order
```

**When to use Kahn's vs DFS:** Kahn's naturally detects cycles (if output length < n). DFS is easier to implement for pure topological sorting.

---

### Dijkstra's Algorithm (Shortest Path in Weighted Graph)

```python
import heapq

def dijkstra(n, edges, start):
    graph = defaultdict(list)
    for u, v, w in edges:
        graph[u].append((v, w))
        graph[v].append((u, w))

    dist = [float('inf')] * n
    dist[start] = 0
    heap = [(0, start)]  # (distance, node)

    while heap:
        d, node = heapq.heappop(heap)
        if d > dist[node]:
            continue  # stale entry, skip
        for neighbor, weight in graph[node]:
            new_dist = dist[node] + weight
            if new_dist < dist[neighbor]:
                dist[neighbor] = new_dist
                heapq.heappush(heap, (new_dist, neighbor))

    return dist
```

**Complexity:** O((V + E) log V) time with a binary heap

**Why it works:** Always process the nearest unvisited node next. A shorter path to an already-processed node is impossible because we always pick the minimum.

---

### Cycle Detection in Directed Graph

```python
def has_cycle_directed(n, edges):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)

    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * n

    def dfs(node):
        color[node] = GRAY  # in current path
        for neighbor in graph[node]:
            if color[neighbor] == GRAY:
                return True   # back edge = cycle
            if color[neighbor] == WHITE:
                if dfs(neighbor):
                    return True
        color[node] = BLACK  # fully explored
        return False

    return any(dfs(i) for i in range(n) if color[i] == WHITE)
```

---

## 11. Dynamic Programming

### What it is
Break a problem into overlapping subproblems. Solve each subproblem once and store the result. Two approaches:
- **Top-down (memoization):** Recursive with caching
- **Bottom-up (tabulation):** Iterative, fill a table from base cases up

### When to use
- Optimization problem (min/max something)
- Counting problems (how many ways)
- Existence problems (can we achieve X)
- Subproblems overlap (same inputs appear multiple times)

### Trigger words
"minimum cost", "maximum profit", "number of ways", "can you reach", "longest subsequence"

### The DP Framework
1. Define the state: what does `dp[i]` (or `dp[i][j]`) represent?
2. Write the recurrence: how does `dp[i]` relate to previous states?
3. Identify base cases
4. Determine traversal order (make sure subproblems are solved before they're needed)

---

### 1D DP: Climbing Stairs / Fibonacci Pattern

```python
def climb_stairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1] = 1
    dp[2] = 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# Space optimized (only need last 2 values):
def climb_stairs_optimized(n):
    a, b = 1, 2
    for _ in range(n - 2):
        a, b = b, a + b
    return b if n >= 2 else n
```

---

### 1D DP: Kadane's Algorithm (Maximum Subarray)

```python
def max_subarray(nums):
    max_sum = curr_sum = nums[0]
    for num in nums[1:]:
        curr_sum = max(num, curr_sum + num)  # restart or extend?
        max_sum = max(max_sum, curr_sum)
    return max_sum
```

**State:** `curr_sum` = max subarray ending at current index
**Recurrence:** Either start a new subarray at this element, or extend the previous one

---

### 1D DP: 0/1 Knapsack

```python
def knapsack(weights, values, capacity):
    n = len(weights)
    dp = [0] * (capacity + 1)
    for i in range(n):
        # traverse backwards to avoid using item i twice
        for w in range(capacity, weights[i] - 1, -1):
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    return dp[capacity]
```

**State:** `dp[w]` = max value with capacity w
**Traversal:** Right to left prevents using the same item twice

---

### 2D DP: Longest Common Subsequence

```python
def lcs(s1, s2):
    m, n = len(s1), len(s2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if s1[i-1] == s2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]
```

---

### 2D DP: Edit Distance

```python
def edit_distance(word1, word2):
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    # Base cases: transforming to/from empty string
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],    # delete from word1
                    dp[i][j-1],    # insert into word1
                    dp[i-1][j-1]   # replace
                )
    return dp[m][n]
```

---

### DP on Intervals

When the subproblem is defined by a range [i, j].

```python
# Longest Palindromic Substring
def longest_palindrome(s):
    n = len(s)
    dp = [[False] * n for _ in range(n)]
    start, max_len = 0, 1

    # All single chars are palindromes
    for i in range(n):
        dp[i][i] = True

    # Length 2
    for i in range(n - 1):
        if s[i] == s[i + 1]:
            dp[i][i+1] = True
            start, max_len = i, 2

    # Length 3 and above
    for length in range(3, n + 1):
        for i in range(n - length + 1):
            j = i + length - 1
            if s[i] == s[j] and dp[i+1][j-1]:
                dp[i][j] = True
                if length > max_len:
                    start, max_len = i, length

    return s[start:start + max_len]
```

---

## 12. Backtracking

### What it is
DFS with an "undo" step. At each decision point, choose an option, recurse, then undo the choice before trying the next option. Exhaustively explores all possibilities.

### When to use
- Generate all subsets, permutations, or combinations
- N-queens, Sudoku solver
- Word search on a grid
- Any "find all solutions" problem

### Template

```python
def backtrack(state, choices, result):
    if is_complete(state):
        result.append(list(state))  # copy, not reference
        return
    for choice in choices:
        if is_valid(state, choice):
            state.append(choice)        # CHOOSE
            backtrack(state, remaining_choices, result)
            state.pop()                 # UNDO (backtrack)
```

---

### Subsets

```python
def subsets(nums):
    result = []
    def backtrack(start, path):
        result.append(list(path))
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    backtrack(0, [])
    return result
```

---

### Permutations

```python
def permutations(nums):
    result = []
    def backtrack(path, remaining):
        if not remaining:
            result.append(list(path))
            return
        for i in range(len(remaining)):
            path.append(remaining[i])
            backtrack(path, remaining[:i] + remaining[i+1:])
            path.pop()
    backtrack([], nums)
    return result
```

---

### Combinations (k of n)

```python
def combinations(n, k):
    result = []
    def backtrack(start, path):
        if len(path) == k:
            result.append(list(path))
            return
        for i in range(start, n + 1):
            path.append(i)
            backtrack(i + 1, path)
            path.pop()
    backtrack(1, [])
    return result
```

---

### Pruning

Pruning eliminates branches that cannot produce valid solutions. Always prune as early as possible.

```python
def combination_sum(candidates, target):
    candidates.sort()  # sort to enable pruning
    result = []
    def backtrack(start, path, remaining):
        if remaining == 0:
            result.append(list(path))
            return
        for i in range(start, len(candidates)):
            if candidates[i] > remaining:
                break  # PRUNE: sorted, so all further candidates are also too large
            path.append(candidates[i])
            backtrack(i, path, remaining - candidates[i])  # i not i+1: can reuse
            path.pop()
    backtrack(0, [], target)
    return result
```

---

## 13. Heaps and Priority Queues

### What it is
A binary heap that always gives you the min (min-heap) or max (max-heap) element in O(1), with O(log n) push and pop.

Python's `heapq` module is a **min-heap**. For a max-heap, negate the values.

### When to use
- K largest/smallest elements
- Continuously adding elements and querying the current min/max
- Merge K sorted lists
- Dijkstra's algorithm
- Scheduling problems

### Trigger words
"k largest", "k smallest", "kth element", "top k", "continuously process in priority order"

---

### Basic Operations

```python
import heapq

heap = []
heapq.heappush(heap, 3)
heapq.heappush(heap, 1)
heapq.heappush(heap, 2)

heapq.heappop(heap)  # returns 1 (minimum)
heap[0]              # peek at minimum without removing

# Max-heap: negate values
heapq.heappush(heap, -5)
-heapq.heappop(heap)  # returns 5

# Build heap from list in O(n)
nums = [3, 1, 4, 1, 5]
heapq.heapify(nums)
```

---

### K Largest Elements

```python
def k_largest(nums, k):
    # Use a min-heap of size k
    # If new element is larger than heap min, replace it
    heap = nums[:k]
    heapq.heapify(heap)
    for num in nums[k:]:
        if num > heap[0]:
            heapq.heapreplace(heap, num)  # faster than pop + push
    return sorted(heap, reverse=True)
```

**Why min-heap for k largest:** The min-heap keeps the k largest seen so far. Any element smaller than the current minimum cannot be in the top k.

---

### Kth Largest in Stream

```python
class KthLargest:
    def __init__(self, k, nums):
        self.k = k
        self.heap = []
        for num in nums:
            self.add(num)

    def add(self, val):
        heapq.heappush(self.heap, val)
        if len(self.heap) > self.k:
            heapq.heappop(self.heap)
        return self.heap[0]
```

---

### Merge K Sorted Lists

```python
def merge_k_sorted(lists):
    heap = []
    for i, lst in enumerate(lists):
        if lst:
            heapq.heappush(heap, (lst[0], i, 0))  # (value, list_idx, element_idx)

    dummy = ListNode(0)
    curr = dummy
    while heap:
        val, i, j = heapq.heappop(heap)
        curr.next = ListNode(val)
        curr = curr.next
        if j + 1 < len(lists[i]):
            heapq.heappush(heap, (lists[i][j+1], i, j+1))
    return dummy.next
```

**Complexity:** O(N log k) where N is total nodes, k is number of lists

---

### Two Heaps (Median of Stream)

Maintain a max-heap for the lower half and a min-heap for the upper half. The medians are at the tops.

```python
class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap (negate values)
        self.large = []  # min-heap

    def add_num(self, num):
        heapq.heappush(self.small, -num)
        # Balance: ensure every element in small <= every element in large
        if self.small and self.large and -self.small[0] > self.large[0]:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        # Ensure sizes differ by at most 1
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def find_median(self):
        if len(self.small) > len(self.large):
            return -self.small[0]
        return (-self.small[0] + self.large[0]) / 2
```

---

## 14. Monotonic Stack and Queue

### What it is
A stack or deque maintained in sorted (monotonically increasing or decreasing) order. When a new element violates the order, pop elements until the order is restored.

### When to use
- "Next greater element" / "next smaller element"
- Largest rectangle in histogram
- Sliding window maximum
- Temperature problems ("days until warmer temperature")

### Trigger words
"next greater", "next smaller", "previous greater", "span", "rectangle", "window maximum"

---

### Monotonic Stack: Next Greater Element

```python
def next_greater_element(nums):
    result = [-1] * len(nums)
    stack = []  # stores indices, stack is monotonically decreasing in value

    for i, num in enumerate(nums):
        while stack and nums[stack[-1]] < num:
            idx = stack.pop()
            result[idx] = num   # current num is the next greater for idx
        stack.append(i)

    return result
```

**How it works:** The stack holds indices of elements that haven't found their "next greater" yet. When a larger element arrives, it resolves all smaller elements in the stack.

---

### Largest Rectangle in Histogram

```python
def largest_rectangle(heights):
    stack = []  # monotonically increasing: stores (index, height)
    max_area = 0
    for i, h in enumerate(heights):
        start = i
        while stack and stack[-1][1] > h:
            idx, height = stack.pop()
            max_area = max(max_area, height * (i - idx))
            start = idx  # can extend this bar's width backwards
        stack.append((start, h))
    # Process remaining bars (they extend to the end)
    for idx, height in stack:
        max_area = max(max_area, height * (len(heights) - idx))
    return max_area
```

---

### Monotonic Deque: Sliding Window Maximum

```python
from collections import deque

def max_sliding_window(nums, k):
    dq = deque()  # stores indices, front = max of current window
    result = []
    for i, num in enumerate(nums):
        # Remove indices outside window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        # Maintain decreasing order: remove smaller elements
        while dq and nums[dq[-1]] < num:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result
```

**Complexity:** O(n) time — each element is added and removed at most once

---

## 15. Sorting

### When each algorithm is appropriate

| Algorithm | Best For | Time | Space | Stable? |
|-----------|---------|------|-------|---------|
| Merge sort | Linked lists, guaranteed O(n log n), stable sort required | O(n log n) | O(n) | Yes |
| Quick sort | Arrays, average case performance, in-place | O(n log n) avg | O(log n) | No |
| Heap sort | Guaranteed O(n log n) with O(1) space | O(n log n) | O(1) | No |
| Counting sort | Integer keys in a small range | O(n + k) | O(k) | Yes |
| Radix sort | Large integers or strings | O(nk) | O(n + k) | Yes |
| Bucket sort | Uniformly distributed floats in [0, 1) | O(n) avg | O(n) | Yes |
| Python's Timsort | Everything in practice (it's the default) | O(n log n) | O(n) | Yes |

---

### Merge Sort

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```

**Why stable:** Equal elements from the left subarray always come before equal elements from the right because of the `<=` condition.

---

### Quick Sort

```python
def quick_sort(arr, low=0, high=None):
    if high is None:
        high = len(arr) - 1
    if low < high:
        pivot_idx = partition(arr, low, high)
        quick_sort(arr, low, pivot_idx - 1)
        quick_sort(arr, pivot_idx + 1, high)

def partition(arr, low, high):
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i+1], arr[high] = arr[high], arr[i+1]
    return i + 1
```

**Worst case O(n²):** Occurs when the pivot is always the min or max (sorted input). Mitigated by random pivot selection.

---

### Counting Sort

```python
def counting_sort(arr, max_val):
    count = [0] * (max_val + 1)
    for num in arr:
        count[num] += 1
    result = []
    for val, freq in enumerate(count):
        result.extend([val] * freq)
    return result
```

---

### Custom Sort Key

```python
# Sort by length, then alphabetically
words.sort(key=lambda w: (len(w), w))

# Sort intervals by start time
intervals.sort(key=lambda x: x[0])

# Sort objects by multiple fields
people.sort(key=lambda p: (-p.age, p.name))  # desc age, then asc name

# Using functools.cmp_to_key for complex comparisons
from functools import cmp_to_key
def compare(a, b):
    if str(a) + str(b) > str(b) + str(a):
        return -1
    return 1
nums.sort(key=cmp_to_key(compare))  # Largest number problem
```

---

## 16. Union-Find (Disjoint Set)

### What it is
A data structure that efficiently tracks which elements belong to the same group (connected component). Supports two operations: `find` (which group?) and `union` (merge two groups).

### When to use
- Detect cycle in undirected graph
- Number of connected components
- Kruskal's MST algorithm
- Dynamic connectivity

---

### Implementation with Path Compression + Union by Rank

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.components = n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False  # already in same component
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px               # union by rank
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        self.components -= 1
        return True

    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

**Complexity:** Nearly O(1) per operation (amortized O(α(n)) where α is the inverse Ackermann function — effectively constant)

**Path compression:** When finding the root of x, make every node on the path point directly to the root. Flattens the tree.

**Union by rank:** Always attach the shorter tree under the taller one. Keeps trees balanced.

---

## 17. Tries

### What it is
A tree where each node represents a character. Words share prefixes. Used for efficient string lookups and prefix searches.

### When to use
- Autocomplete
- Spell checking
- IP routing (longest prefix match)
- Word search / Boggle
- Any "starts with" prefix problem

---

### Implementation

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True

    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end

    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

**Complexity:** O(L) per operation where L is word length. O(total characters in all words) space.

---

## 18. Greedy

### What it is
Make the locally optimal choice at each step. No backtracking. Works when a local optimum leads to a global optimum.

### When to use
- Interval scheduling (greedy by end time)
- Huffman coding
- Fractional knapsack (not 0/1)
- Jump game

### How to recognize
Greedy works when the problem has the **greedy choice property**: a locally optimal choice is always part of a globally optimal solution. Prove this by showing that swapping any greedy choice for a non-greedy one cannot improve the result.

---

### Interval Scheduling (Non-overlapping intervals)

```python
def erase_overlap_intervals(intervals):
    intervals.sort(key=lambda x: x[1])  # sort by END time — this is the key insight
    count = 0
    end = float('-inf')
    for start, finish in intervals:
        if start >= end:
            end = finish   # take this interval
        else:
            count += 1     # remove (skip) this interval
    return count
```

**Why sort by end:** By always choosing the interval that ends earliest, you leave the most room for future intervals. Sorting by start does not work.

---

### Jump Game

```python
def can_jump(nums):
    max_reach = 0
    for i, jump in enumerate(nums):
        if i > max_reach:
            return False  # can't reach this position
        max_reach = max(max_reach, i + jump)
    return True
```

---

## 19. Bit Manipulation

### Core operations

```python
n & 1          # check if n is odd (last bit)
n >> 1         # divide by 2 (right shift)
n << 1         # multiply by 2 (left shift)
n & (n - 1)    # clear the lowest set bit (used to count bits)
n & (-n)       # isolate the lowest set bit
n ^ n          # 0 (XOR with itself)
n ^ 0          # n (XOR with 0)
a ^ b ^ a      # b (XOR is commutative and associative)
~n             # bitwise NOT: ~n == -(n+1)
```

---

### Common Patterns

**Count set bits (Hamming weight):**
```python
def count_bits(n):
    count = 0
    while n:
        n &= n - 1   # clear lowest set bit each iteration
        count += 1
    return count
```

**Check if power of 2:**
```python
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0
# Powers of 2 have exactly one set bit. n & (n-1) clears it, leaving 0.
```

**Find single number (every other number appears twice):**
```python
def single_number(nums):
    result = 0
    for num in nums:
        result ^= num  # pairs cancel: a ^ a = 0
    return result
```

**Get, set, clear, toggle a specific bit:**
```python
def get_bit(num, i):    return (num >> i) & 1
def set_bit(num, i):    return num | (1 << i)
def clear_bit(num, i):  return num & ~(1 << i)
def toggle_bit(num, i): return num ^ (1 << i)
```

---

## 20. String Patterns

### KMP (Knuth-Morris-Pratt) — Pattern Matching

Find all occurrences of a pattern in a text in O(n + m) time.

```python
def kmp_search(text, pattern):
    # Build failure function: lps[i] = length of longest proper prefix of pattern[0..i] that is also a suffix
    def build_lps(pattern):
        lps = [0] * len(pattern)
        length = 0
        i = 1
        while i < len(pattern):
            if pattern[i] == pattern[length]:
                length += 1
                lps[i] = length
                i += 1
            elif length:
                length = lps[length - 1]  # don't increment i
            else:
                lps[i] = 0
                i += 1
        return lps

    lps = build_lps(pattern)
    results = []
    i = j = 0  # i = text index, j = pattern index
    while i < len(text):
        if text[i] == pattern[j]:
            i += 1
            j += 1
        if j == len(pattern):
            results.append(i - j)   # match found
            j = lps[j - 1]
        elif i < len(text) and text[i] != pattern[j]:
            if j:
                j = lps[j - 1]
            else:
                i += 1
    return results
```

---

### Rabin-Karp — Rolling Hash

Compute a hash of the current window. Slide it forward by updating in O(1). Only compare characters when hashes match.

```python
def rabin_karp(text, pattern):
    n, m = len(text), len(pattern)
    if m > n:
        return []
    BASE, MOD = 31, 10**9 + 7
    power = pow(BASE, m - 1, MOD)

    def char_val(c):
        return ord(c) - ord('a') + 1

    # Compute initial hashes
    pattern_hash = 0
    window_hash = 0
    for i in range(m):
        pattern_hash = (pattern_hash * BASE + char_val(pattern[i])) % MOD
        window_hash  = (window_hash  * BASE + char_val(text[i]))    % MOD

    results = []
    for i in range(n - m + 1):
        if window_hash == pattern_hash and text[i:i+m] == pattern:
            results.append(i)
        if i + m < n:
            window_hash = (window_hash - char_val(text[i]) * power) % MOD
            window_hash = (window_hash * BASE + char_val(text[i + m])) % MOD
    return results
```

---

### Useful String Tricks

```python
# Reverse a string
s[::-1]

# Check palindrome
s == s[::-1]

# All permutations check (anagram)
from collections import Counter
Counter(s1) == Counter(s2)

# Split and join
words = "hello world".split()       # ['hello', 'world']
sentence = " ".join(words)          # 'hello world'

# String to list of chars (for mutation)
chars = list(s)
chars[0] = 'X'
s = "".join(chars)

# ord and chr for ASCII math
ord('a')   # 97
chr(97)    # 'a'
ord('A')   # 65
ord(c) - ord('a')  # 0-indexed position in alphabet

# Check character type
c.isalpha()
c.isdigit()
c.isalnum()
c.lower()
c.upper()
```

---

## 21. Complexity Reference

### Time Complexities

| Operation | Data Structure | Complexity |
|-----------|---------------|------------|
| Access by index | Array | O(1) |
| Search (unsorted) | Array | O(n) |
| Search (sorted) | Array + binary search | O(log n) |
| Insert/delete (end) | Array / list | O(1) amortized |
| Insert/delete (middle) | Array | O(n) |
| Insert/delete | Linked list (with pointer) | O(1) |
| Insert/delete/search | Hash map / set | O(1) average |
| Insert/delete/search | BST (balanced) | O(log n) |
| Push/pop | Stack | O(1) |
| Enqueue/dequeue | Queue (deque) | O(1) |
| Push/pop | Heap | O(log n) |
| Peek | Heap | O(1) |
| Build | Heap from list | O(n) |
| Insert/search | Trie | O(L) |
| Find/union | Union-Find | O(α(n)) ≈ O(1) |

### Space Complexities

| Pattern | Space |
|---------|-------|
| Iterative two pointers / sliding window | O(1) |
| Recursion depth d | O(d) call stack |
| BFS with n nodes | O(n) queue |
| DFS with n nodes | O(n) visited set |
| DP 1D | O(n) → often O(1) with rolling |
| DP 2D on m×n | O(m×n) → often O(n) with rolling |
| Trie with n words of avg length L | O(n × L) |

### Sorting complexities

| Algorithm | Best | Average | Worst | Space | Stable |
|-----------|------|---------|-------|-------|--------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes |
| Timsort | O(n) | O(n log n) | O(n log n) | O(n) | Yes |

---

## 22. Pattern Recognition Guide

Use this to identify the right approach when reading a problem.

### Input type signals

| Input | Consider |
|-------|---------|
| Sorted array | Binary search, two pointers |
| Unsorted array, find pair | Hash map |
| Array, contiguous subarray | Sliding window, Kadane's, prefix sum |
| Linked list | Fast/slow pointers, dummy node, in-place reversal |
| Binary tree | DFS (recursive), BFS (level-order) |
| General graph | BFS (shortest path), DFS (connectivity, cycles) |
| Grid (2D array) | BFS/DFS treating cells as graph nodes |
| String matching | Sliding window + hash, KMP |
| Sorted + rotated | Modified binary search |
| Intervals | Sort by start or end, sweep line |
| Integers in small range | Counting sort, bucket |
| Stream of data | Heap (running median, top k) |

### Output type signals

| Output | Consider |
|--------|---------|
| True/False (does X exist?) | DFS/BFS, binary search, two pointers |
| Count of ways | DP (bottom-up), backtracking with memo |
| Min/max value | DP, greedy, binary search on answer |
| All solutions/permutations | Backtracking |
| Shortest path | BFS (unweighted), Dijkstra (weighted) |
| K largest/smallest | Heap |
| Grouped/connected elements | Union-Find, BFS/DFS |
| Ordered elements with constraints | Topological sort |

### Constraint signals

| Constraint | Implication |
|-----------|------------|
| n ≤ 20 | O(2^n) or O(n!) acceptable — backtracking |
| n ≤ 500 | O(n²) acceptable |
| n ≤ 10,000 | O(n log n) acceptable |
| n ≤ 10^6 | O(n) or O(n log n) required |
| n ≤ 10^9 | O(log n) required — binary search |
| "In-place" or "O(1) space" | Two pointers, in-place reversal, bit tricks |
| Values in [0, 1) | Bucket sort |
| Integer keys ≤ 10^5 | Counting sort viable |

### The 5-minute triage for any problem

1. **What are the inputs and outputs exactly?** Write an example.
2. **What are the constraints?** (n size → time complexity target)
3. **Does the input have special structure?** (sorted, unique, small range)
4. **What's the brute force?** State it, then ask: "what makes it slow?"
5. **What pattern eliminates the slowness?** (scan table above)

If stuck after 5 minutes: start with brute force, implement it, then optimize with the pattern that addresses the bottleneck.

---

*This document covers the patterns that appear in 90%+ of SWE interview problems. Depth of understanding matters more than breadth — know each pattern well enough to implement it from scratch.*
