# Alien Dictionary
## https://leetcode.com/problems/alien-dictionary

There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of non-empty words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.
```
Example 1:

Input:
[
  "wrt",
  "wrf",
  "er",
  "ett",
  "rftt"
]

Output: "wertf"

Example 2:

Input:
[
  "z",
  "x"
]

Output: "zx"

Example 3:

Input:
[
  "z",
  "x",
  "z"
] 

Output: "" 
Explanation: The order is invalid, so return "".
```

**Note:**
1. You may assume all letters are in lowercase.
2. If the order is invalid, return an empty string.
3. There may be multiple valid order of letters, return any one of them is fine.

## Approach :
Before we try to solvs this problem, lets first understand one very important thing.

**The letters within a single word don't tell us anything about the relative order.** 
For example, the presence of the word `train` in the list does not tell us that the letter t is before the letter r in the dictionary.


# Implementation :
