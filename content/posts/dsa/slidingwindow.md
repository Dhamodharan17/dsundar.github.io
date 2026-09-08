---
title: Sliding Window
subtitle: A technique for solving array/string problems efficiently
date: 2026-09-08T12:00:00+05:30
description: An introduction to sliding window technique and how it keeps systems reliable in the presence of failures.
categories:
  - DSA
tags:
  - sliding-window
---

Here is the content converted to clean Hugo-compatible Markdown, preserving all text and code blocks.

---

# Sliding Window

Simple observation – consecutive windows overlap significantly

No need to re-compute everything, shares k-1 elements with previous one

Account only for elements leaving and coming(undo to k-1)

Expand (right += 1) until condition is violated & shrink (left += 1) until condition is restored.

## Why Sliding Window works ?

Sliding window works best

when condition on window changes in one consistent direction(monotone) as window grows or shrinks.

Shrinking window can move from invalid state to valid or vice versa

**When to use?** Subarray/substring problems, longest/shortest/minimum/maximum/expand and shrink mental model

**When not to use?** Non contigious elements/no monotone property e.g. -ve numbers/elements can be rearranged/track all subarray not just one.

## Template

```text
left = right = 0 

while right < N: 

    while voilated: 

        left += 1 or move to index 

    right += 1 

```

### Finding Maximum

```text
While condition_voilated: 

Shrink() 

Result = max(Result, window_size) 

```

### Finding Minimum

```text
While condition_satisified: 

Result = min(Result, window_size) # since all small windows are answer 

Shrink() 

```

### Exactly K Problem

`Exactly(k) = atmost(k) - atmost(k-1)`

## Choosing Window State

Expand and shrink is common for all problem but what data structure we are going to use to track the state and update it matters.

| Need | Store |
| --- | --- |
| Sum/product | Running Sum |
| Count frequency/Strings/Index Needed | HashMap / Counter |
| Character frequency | Counter / int[26] |
| Need max/min element inside window | Monotonic Queue(deque) |
| Need index positions | HashMap |
| Need duplicates check | HashSet |
| Need order of elements | Deque |
| Binary problems | Bits |
| median | 2 Heaps Pattern |

> "Heaps don't track window expiration naturally; monotonic queue maintains the window's max/min candidates and removes expired elements efficiently."

### Formula

`Count += (right-left+1)`

> "number of possible starting points for subarrays ending at current right".
> All subarrays will include right

`n * (n+1) / 2`

> total number of subarrays in an array of size n.

### Longest Substring Without Repeating Characters
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        N = len(s)
        lookup = {}

        l = r = maxi = 0

        while r < N:
            cur = s[r]
            if cur in lookup and lookup[cur] >= l:
                l = lookup[cur]+1
            lookup[cur] = r
            maxi = max(maxi, r-l+1)
            r += 1
        return maxi

```
### Longest Repeating Character Replacement
```python
class Solution:
    def characterReplacement(self, s: str, k: int) -> int:
        
        # task : get the longest string
        # track the smallest char to replace ABBBBA (Replacing A is smart choice)

        N = len(s)
        lookup = defaultdict(int)
        l = r = maxi = ans = 0

        while r < N:
            cur = s[r]
            lookup[cur] += 1
            maxi = max(maxi, lookup[cur])

            # smallest = window len - maxi
            while (r-l+1)-maxi > k:
                lookup[s[l]] -= 1
                l += 1
            ans = max(ans, r-l+1)
            r += 1
        return ans
```
### Sliding Window Maximum
```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:

        N = len(nums)
        res = []
        l = r = maxi = 0
        q = deque()

        while r < N:
            
            # remove older windows
            while q and q[0] < l:
                q.popleft()

            cur = nums[r]
            # need max in every window so remove all small
            while q and nums[q[-1]] <= cur:
                q.pop()
            q.append(r)

            if r - l + 1 == k:
                res.append(nums[q[0]])
                l += 1 # move window
            r += 1
        return res
```