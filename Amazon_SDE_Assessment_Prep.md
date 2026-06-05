# Amazon SDE Assessment Prep — Amazon Leo
## Software Development Engineer (SDE-I)

---

## The Three Parts and What They Actually Test

**Part 1: Coding Challenge (100 min)**
Two problems. The first is a standard algorithm question. The second is a repo-based question where you work inside an existing codebase with an AI assistant available. This tests whether you can read, understand, and modify real code, not just write algorithms from scratch.

**Part 2: Work Simulation (45 min)**
Scenario-based decisions. Amazon will show you situations an SDE faces and ask you to choose between options or rank approaches. This tests your alignment with Amazon's Leadership Principles, not your coding ability.

**Part 3: Work Style Surveys (15 min)**
Two personality and work-style surveys. Amazon uses these to assess cultural fit. There are no trick answers but there are wrong ones.

---

## Part 1: Coding Challenge

### Language Choice

Use **Python 3**. It is your primary language, you think in it fastest, and the standard library covers almost everything you will need in a timed assessment.

For the repo question, choose **Python in Django framework**. Django follows a Model-View-Template pattern:
- Models define database structure
- Views handle request logic
- URLs route requests to views

You do not need to be a Django expert. Focus on reading and modifying existing code rather than writing from scratch.

---

### Problem 1: Algorithm Patterns to Know

Amazon SDE-I assessments lean toward medium difficulty LeetCode problems. Master these five patterns:

---

#### Pattern 1: Hash Maps
Using a dictionary to reduce O(n^2) solutions to O(n). If you find yourself writing a nested loop to check "does X exist in this list," a hash map is almost always the answer.

```python
# Example: Two Sum
def two_sum(nums, target):
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
```

Problems to practice: Two Sum, Group Anagrams, Top K Frequent Elements

---

#### Pattern 2: Two Pointers
Two index variables moving through an array, typically from both ends or at different speeds. Useful for sorted arrays, palindrome checks, and finding pairs.

```python
# Example: Valid Palindrome
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True
```

Problems to practice: Valid Palindrome, Container With Most Water, 3Sum

---

#### Pattern 3: Sliding Window
Maintain a window of elements and slide it across the input. Useful for "maximum/minimum subarray of size K" or "longest substring without X."

```python
# Example: Longest Substring Without Repeating Characters
def length_of_longest_substring(s):
    seen = {}
    left = 0
    max_len = 0
    for right, char in enumerate(s):
        if char in seen and seen[char] >= left:
            left = seen[char] + 1
        seen[char] = right
        max_len = max(max_len, right - left + 1)
    return max_len
```

Problems to practice: Longest Substring Without Repeating Characters, Maximum Average Subarray, Minimum Window Substring

---

#### Pattern 4: BFS and DFS
Graph and tree traversal.
- **BFS** uses a queue and explores level by level. Best for shortest path problems.
- **DFS** uses recursion or a stack and goes deep before backtracking. Best for exhaustive search.

```python
from collections import deque

# BFS: Level order traversal
def bfs(root):
    if not root:
        return []
    result, queue = [], deque([root])
    while queue:
        node = queue.popleft()
        result.append(node.val)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return result

# DFS: Number of Islands
def num_islands(grid):
    if not grid:
        return 0
    count = 0
    def dfs(r, c):
        if r < 0 or r >= len(grid) or c < 0 or c >= len(grid[0]) or grid[r][c] == '0':
            return
        grid[r][c] = '0'
        dfs(r+1, c); dfs(r-1, c); dfs(r, c+1); dfs(r, c-1)
    for r in range(len(grid)):
        for c in range(len(grid[0])):
            if grid[r][c] == '1':
                dfs(r, c)
                count += 1
    return count
```

Problems to practice: Number of Islands, Binary Tree Level Order Traversal, Course Schedule

---

#### Pattern 5: Dynamic Programming (Basic)
Break a problem into overlapping subproblems and store results to avoid recomputation.

```python
# Example: Climbing Stairs
def climb_stairs(n):
    if n <= 2:
        return n
    dp = [0] * (n + 1)
    dp[1], dp[2] = 1, 2
    for i in range(3, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# Example: House Robber
def rob(nums):
    prev2, prev1 = 0, 0
    for num in nums:
        prev2, prev1 = prev1, max(prev1, prev2 + num)
    return prev1
```

Problems to practice: Climbing Stairs, House Robber, Coin Change

---

### Practice Problem List

Do these on LeetCode in Python 3 without looking at solutions first:

| Problem | Pattern | Difficulty |
|---|---|---|
| Two Sum | Hash Map | Easy |
| Valid Anagram | Hash Map | Easy |
| Group Anagrams | Hash Map | Medium |
| Valid Palindrome | Two Pointers | Easy |
| Container With Most Water | Two Pointers | Medium |
| Longest Substring Without Repeating Characters | Sliding Window | Medium |
| Maximum Subarray (Kadane's Algorithm) | DP | Medium |
| Climbing Stairs | DP | Easy |
| House Robber | DP | Medium |
| Number of Islands | BFS/DFS | Medium |
| Binary Tree Level Order Traversal | BFS | Medium |
| Course Schedule | DFS / Topological Sort | Medium |
| Valid Parentheses | Stack | Easy |
| Merge Intervals | Sorting | Medium |
| Reverse Linked List | Pointers | Easy |

---

### Problem 2: Repo Question Strategy

You are dropped into an existing codebase, given a task (fix a bug or add a feature), and you have an AI coding assistant available.

**What Amazon is testing:**
- Can you read code you did not write and understand it quickly?
- Can you use AI tools effectively without letting them do all the thinking?
- Do you write clean code that fits the existing patterns?
- Do you handle edge cases?

**Step-by-step approach:**
1. Read the task description completely before touching any code
2. Explore the file structure to understand what exists
3. Find the relevant files before editing anything
4. Use the AI assistant to explain unfamiliar code, not to write everything for you
5. Write your solution to fit the existing code style and patterns
6. Test your logic mentally against example inputs and edge cases before submitting
7. Edge cases to always check: empty input, null/None values, single element, boundary conditions

---

### Time Management: Coding Challenge

| Task | Time |
|---|---|
| Read Problem 1 and plan approach | 5 min |
| Code Problem 1 | 35 min |
| Test Problem 1 edge cases | 5 min |
| Read Problem 2 repo and understand codebase | 10 min |
| Implement Problem 2 | 35 min |
| Test Problem 2 changes | 10 min |

If you are stuck after 15 minutes on Problem 1, write a brute force solution that works correctly even if it is slow. A working O(n^2) solution scores better than an incomplete O(n log n) solution.

---

## Part 2: Work Simulation

### Amazon's Leadership Principles

Every scenario in the work simulation maps to one or more of these. Know them before you start.

---

**Customer Obsession**
Start with the customer and work backwards. When a scenario gives you a tradeoff between technical elegance and what the customer actually needs, choose the customer. Always.

**Ownership**
Act like you own the whole system, not just your ticket. If you see a problem outside your scope, you still flag it or fix it. If an answer option is "that's not my team's problem," it is almost certainly wrong.

**Invent and Simplify**
Prefer simple solutions over complex ones. If a scenario offers a clever but complicated approach and a straightforward one that solves the problem, Amazon usually wants the simple one.

**Bias for Action**
Make decisions with incomplete information rather than waiting for perfect clarity. If you can either gather more data for two more weeks or ship something reasonable now and iterate, Amazon leans toward action.

**Dive Deep**
Do not accept surface-level explanations for problems. Dig into metrics and logs rather than guessing. This maps directly to debugging with Prometheus and Grafana.

**Deliver Results**
Ship working software. Being 90% complete and blocked matters less than finding a way to deliver something.

**Earn Trust**
Be honest about mistakes, including your own. If you can either quietly fix a bug you introduced or be transparent about it with the team, transparency is always the right Amazon answer.

**Have Backbone; Disagree and Commit**
If you think a technical decision is wrong, say so clearly. But once the decision is made, commit fully. Do not silently disagree and do a bad job implementing something you objected to.

**Insist on the Highest Standards**
Do not accept mediocre work from yourself or others. If a code review reveals real problems, raise them even if it slows things down.

**Think Big**
Consider whether a solution scales. A fix that works for 100 customers but not 100,000 is not a good fix.

---

### Work Simulation Decision Rules

When choosing between options in a scenario:

- Options that blame others or pass responsibility elsewhere are almost always wrong
- Options that involve hiding information, especially about problems, are always wrong
- Options that prioritize short-term shortcuts over long-term quality are usually wrong
- Options that involve checking in with the customer or end user are usually right
- When in doubt, ask what a principled senior engineer who genuinely cares about the customer would do

---

## Part 3: Work Style Surveys

These surveys ask questions about how you prefer to work. Answer honestly, but understand what Amazon values:

- Taking ownership rather than waiting to be told what to do
- Communicating directly and clearly, even when the news is bad
- Holding yourself to high standards
- Curiosity and willingness to learn new things
- Collaborative but independently motivated

Do not answer in extremes on everything. Real engineers are not "always" one way. The surveys are partly looking for self-awareness.

---

## Amazon Leo Context

Knowing the team helps in the work simulation since scenarios may be framed around satellite network infrastructure.

**What Amazon Leo is:**
Amazon Leo is a low Earth orbit (LEO) satellite constellation, similar to Starlink, designed to deliver broadband internet to underserved areas globally. LEO satellites orbit at roughly 550-1200km altitude (vs. traditional geostationary satellites at 35,000km), which gives much lower latency.

**The software problems Leo faces:**
- Ground station software that communicates with satellites as they pass overhead
- Network routing and handoff as satellites move across the sky at 17,000+ mph
- Reliability systems for connectivity in remote, harsh environments
- Real-time telemetry processing from hundreds of satellites simultaneously
- Systems that must remain functional when connectivity itself is intermittent

**How your experience maps to Leo:**
- Distributed microservices architecture maps to ground station software coordination
- Event-driven pipelines map to telemetry and satellite event processing
- Zero-trust security model maps to satellite network security requirements
- Prometheus and Grafana observability maps to satellite health monitoring
- Building from zero maps to Leo still being in active construction

---

## Your Strongest Talking Points for Leo

If you advance past the assessment to a human interview:

| Your Experience | Leo Relevance |
|---|---|
| 20+ containerized services in production | Distributed ground software systems |
| Event-driven architecture for unreliable external services | Satellite connectivity is inherently intermittent |
| Prometheus/Grafana observability stack | Real-time satellite telemetry monitoring |
| Zero-trust security with OAuth/OIDC | Space infrastructure has serious security requirements |
| Building entire platform from zero | Leo is actively building new systems |
| 70%+ test coverage from greenfield | Quality requirements for safety-critical infrastructure |

---

## Quick Reference: Assessment Day Checklist

- [ ] Use Python 3 for both coding questions
- [ ] Choose Django framework for the repo question
- [ ] Read every problem fully before writing any code
- [ ] Write brute force first if stuck, optimize second
- [ ] Check edge cases: empty input, None, single element, large input
- [ ] Know all 16 Leadership Principles before the work simulation
- [ ] Answer work simulation scenarios as a principled senior engineer would
- [ ] Answer work style surveys honestly, avoid all extremes
- [ ] Set aside 2.5 uninterrupted hours
- [ ] Use Chrome or Edge on a stable connection
- [ ] Single monitor only during the coding challenge
