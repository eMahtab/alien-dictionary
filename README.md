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
So to find the relative order of letters in the dictionary we must compare two words.

# Implementation :
```java
class Solution {
    public String alienOrder(String[] words) {
        int[] indegree = new int[26];
        Map<Character, Set<Character>> graph = new HashMap<>();
        buildGraph(graph, words, indegree);
        return bfs(graph, indegree);
    }
    
    private String bfs(Map<Character, Set<Character>> graph, int[] indegree) {
        StringBuilder sb = new StringBuilder();
        int totalChars = graph.size();
        Queue<Character> q = new LinkedList<>();
        for(char c : graph.keySet()) {
            if(indegree[c - 'a'] == 0) {
                sb.append(c);
                q.offer(c);
            }
        }
        while(!q.isEmpty()) {
            char current = q.poll();
            if(graph.get(current).size() == 0)
                continue;
            for(char neighbor : graph.get(current)) {
                indegree[neighbor - 'a']--;
                if(indegree[neighbor - 'a'] == 0) {
                    q.offer(neighbor);
                    sb.append(neighbor);
                }
            }
        }
        return sb.length() == totalChars ? sb.toString() : "";
    }
    
    private void buildGraph(Map<Character, Set<Character>> graph, String[] words, int[] indegree) {
        for(String word : words) {
            for(char ch : word.toCharArray())
                graph.putIfAbsent(ch, new HashSet<>());
        }
        
        for(int i = 1; i < words.length; i++) {
            String first = words[i-1];
            String second = words[i];
            // Check If second word is a prefix of first word, If thats the case its not a valid alien dictionary
            if(first.startsWith(second) && first.length() > second.length()) {
                graph.clear();
                return;
            }
            int minLength = Math.min(first.length(), second.length());
            
            for(int j = 0; j < minLength; j++) {
                if(first.charAt(j) != second.charAt(j)) {
                    char out = first.charAt(j);
                    char in = second.charAt(j);
                    if(!graph.get(out).contains(in)) {
                        graph.get(out).add(in);
                        indegree[in - 'a']++;
                    }
                    break;    
                }
            }
        }
    }
}
```


# References :
https://www.youtube.com/watch?v=LA0X_N-dEsg
