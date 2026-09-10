---
title: Hash Tables
subtitle: Patterns and techniques for hash table problems
date: 2026-09-08T12:00:00+05:30
description: Common hash table patterns and problem-solving techniques.
categories:
  - DSA
tags:
  - hash-tables
---

# Hash Tables


| Problem | Pattern | Key Idea |
|---|---|---|
| Contains Duplicate | Arrays & Hashing | iterate if current already in set return True |
| Valid Anagram | Arrays & Hashing | anagram = 2 strings with same char and its count ; return Counter(s) == Counter(t) |
| Two Sum | Arrays & Hashing | need indices, cannot sort so x + y = target -> y = target-x : check y in map |
| Group Anagrams | Arrays & Hashing | all anagrams == sorted form (so key) |
| Top K Frequent Elements | Arrays & Hashing | top k repeating elements / need max, use minheap so we can keep max / allow till heap can become k |
| Encode and Decode Strings | Arrays & Hashing | Encode as: `<length>#<string>`; while decode find the number using `while s[j] != '#':` |
| Product of Array Except Self | Arrays & Hashing | for prefix and suffix array and for every i take from left and right ignore current |
| Valid Sudoku | Arrays & Hashing | add each cell to respective row, col, box set and check for every cell already exists in those areas |
| Longest Consecutive Sequence | Arrays & Hashing | move elements to set and iterate set then Only start counting if this is the beginning of a sequence |

## Contains Duplicate
```python
class Solution:
    def containsDuplicate(self, nums: List[int]) -> bool:
        # any value appears at least twice in the array
        seen = set()
        for ni in nums:
            if ni in seen:
                return True
            seen.add(ni)
        return False
```

## Two Sum
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        # need indices, cannot sort
        N = len(nums)
        lookup = {}

        for i, x in enumerate(nums):
            # x+ y = target -> y = target-x : check y in map
            y = target - x
            if y in lookup and lookup[y] != i:
                return [lookup[y], i]
            lookup[x] = i
        
        return []
```

## Valid Anagram
```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        # anagram = 2 strings with same chars and char count
        return Counter(s) == Counter(t)
```

## Group Anagrams
```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        lookup = defaultdict(list)
        for si in strs:
            # all anagrams == sorted form (so key)
            lookup[tuple(sorted(si))].append(si)
        return list(lookup.values())
```

## Top K Frequent Elements
```python
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        # top k repeating elements
        counter = Counter(nums)

        # need max, use minheap so we can keep max
        minheap = [] #(count, value)

        for i, v in counter.items():
            if len(minheap) < k:# allow till heap can become k
                    heapq.heappush(minheap, (v, i))
            elif minheap[0][0] < v:
                    heapq.heappop(minheap)
                    heapq.heappush(minheap, (v, i))
        return [m[1] for m in minheap]
```
- Sorting takes O(n log n) time, but heap approach takes O(n log k) time.


## Encode and Decode Strings
```python
class Codec:
    def encode(self, strs: List[str]) -> str:
        # Encode as: <length>#<string>
        res_str = ''
        for si in strs:
            res_str += (str(len(si))+'#'+si)
        return res_str

    def decode(self, s: str) -> List[str]:
        res = []
        i = 0
        N = len(s)
        while i < N:
            j = i
            # find the number since it can be multi-digit
            while s[j] != '#':
                j += 1
            length = int(s[i:j])
            res.append(s[j+1:j+1+length])
            i = j + 1 + length
        return res
```

## Product of Array Except Self
```python
class Solution:
    def productExceptSelf(self, nums: List[int]) -> List[int]:

        N = len(nums)
        prefix = [1] * N
        suffix = [1] * N
        # for prefix and suffix arrays
        for i in range(N):
            prefix[i] = nums[i] * (prefix[i-1] if i > 0 else 1)
        for i in range(N-1, -1, -1):
            suffix[i] = nums[i] * (suffix[i+1] if i < N-1 else 1)

        result = [1] * N
        for i in range(N):
            # for every i take from left and right ignore current
            result[i] *= prefix[i-1] if i > 0 else 1
            result[i] *= suffix[i+1] if i < N-1 else 1
        
        return result
```

## Valid Sudoku
```python
class Solution:
    def isValidSudoku(self, board: List[List[str]]) -> bool:
        rows = defaultdict(set)
        cols = defaultdict(set)
        boxes = defaultdict(set)

        for r in range(9):
            for c in range(9):
              # loop working, for each row visit each column
                if board[r][c] == '.':
                    continue
                if (board[r][c] in rows[r] or 
                    board[r][c] in cols[c] or 
                    board[r][c] in boxes[(r//3, c//3)]):
                    return False
                  # whenever visiting a cell add it to respective row, column and box
                rows[r].add(board[r][c])
                cols[c].add(board[r][c])
                boxes[(r//3, c//3)].add(board[r][c])
        return True
/*
This code works because it checks each filled cell exactly once against three hash-sets that track what has already appeared:

rows[r] — all digits already seen in row r
cols[c] — all digits already seen in column c
boxes[(r//3, c//3)] — all digits already seen in the 3x3 sub-box that contains (r, c)
*/
```
## Longest Consecutive Sequence
```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        num_set = set(nums)
        longest = 0
        
        for n in num_set:
            # Only start counting if this is the beginning of a sequence
            if n - 1 not in num_set:
                current_num = n
                current_length = 1
                
                # Count consecutive numbers
                while current_num + 1 in num_set:
                    current_num += 1
                    current_length += 1
                
                longest = max(longest, current_length)
        
        return longest
```

