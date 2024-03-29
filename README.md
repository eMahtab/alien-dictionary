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
Before we try to solve this problem, lets first understand one very important thing.

**The letters within a single word don't tell us anything about the relative order.** 
For example, the presence of the word `train` in the list does not tell us that the letter t is before the letter r in the dictionary.
So to find the relative order of letters in the dictionary we must compare two words.

![Graph](graph.JPG?raw=true "Graph")

# Implementation :
```java
class Solution {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> map = new HashMap<>();
        int[] indegree = new int[26];
        buildGraph(map, indegree, words);
        return bfs(map, indegree);
    }
    
    private void buildGraph(Map<Character, Set<Character>> map, int[] indegree, String[] words) {
        for(String word : words) {
            for(char ch : word.toCharArray())
                map.putIfAbsent(ch, new HashSet<Character>());
        }
        
        for(int i = 0; i < words.length - 1; i++) {
            String first = words[i];
            String second = words[i+1];
            // Check If second word is a prefix of first word, If thats the case its not a valid alien dictionary
            if(first.startsWith(second) && first.length() > second.length()) {
                map.clear();
                return;
            }
            int minLength = Math.min(first.length(), second.length());
            for(int index = 0; index < minLength; index++) {
                if(first.charAt(index) != second.charAt(index)) {
                    // If check is required, e.g. for words {ox, sx, to, ts, x}
                    // We should not increment the indegree multiple times because of same dependency/relationship
                    // comparison between words ox and sx tells us that o comes before s
                    // again comparison between words to and ts tells us o comes before s
                    if(!map.get(first.charAt(index)).contains(second.charAt(index))) {
                        indegree[second.charAt(index) - 'a']++;
                        map.get(first.charAt(index)).add(second.charAt(index));
                    }
                    break;
                }
            }
                
        }
    }
    
    private String bfs(Map<Character,Set<Character>> map, int[] indegree) {
        Queue<Character> q = new LinkedList<>();
        int totalChars = map.size();
        StringBuilder sb = new StringBuilder();
        for(Character ch : map.keySet()) { 
            // if(map.get(ch).size() == 0) , this will be wrong to check the 0 indegree node
            if(indegree[ch - 'a'] == 0) 
                q.add(ch);
        }
        int count = 0;
        while(!q.isEmpty()) {
            char ch = q.poll();
            count++;
            sb.append(ch);
            for(char c : map.get(ch)) {
                indegree[c - 'a']--;
                if(indegree[c - 'a'] == 0) {
                    q.add(c);
                }
            }
        }
        
       return  count == totalChars ? sb.toString() : "";
    }
}
```

### Implementation 2
```java
class Solution {
    public String alienOrder(String[] words) {
        if(words == null || words.length == 0)
            return "";
        Set<Character> distinct = new HashSet<>();
        for(String str : words) {
            for(char ch : str.toCharArray())
                distinct.add(ch);
        }   
        List<char[]> order = new ArrayList<>();
        for(int i = 0; i < words.length - 1; i++) {
            String str1 = words[i];
            String str2 = words[i+1];
            int minLength = Math.min(str1.length(), str2.length());
            for(int j = 0; j < minLength; j++) {
                if(str1.charAt(j) != str2.charAt(j)) {
                    order.add(new char[]{str1.charAt(j) , str2.charAt(j)});
                    break;
                }
            }
            if(str1.startsWith(str2) && str1.length() > str2.length())
                return "";
        }    
        Map<Character, Integer> map = new HashMap<>();
        for(char ch : distinct) {
            map.put(ch, 0);
        }
        for(char[] arr : order) {
            int indegree = map.get(arr[1]);
            map.put(arr[1], indegree + 1);
        }
        String letters = "";
        Queue<Character> q = new ArrayDeque<>();
        for(char ch : map.keySet()) {
            if(map.get(ch) == 0)
                q.add(ch);
        }
        while(!q.isEmpty()) {
            char ch = q.remove();
            letters += ch;
            for(char[] arr : order) {
                if(arr[0] == ch) {
                    map.put(arr[1], map.get(arr[1]) - 1);
                    if(map.get(arr[1]) == 0)
                        q.add(arr[1]);
                }
            }
        }
        return letters.length() == distinct.size() ? letters : ""; 
    }
}
```
## Important :
1. Prefix check is required to handle e.g. ["apple", "app"] these type of inputs. 
2. Don't compare after the first different character, so break; is must
3. Don't forget array's have `length` property while strings have `length()` method
4. In second implementation we are using `Map` for storing indegree for each character and we are using `Set` to know all the distinct characters in the words array.

# References :
https://www.youtube.com/watch?v=LA0X_N-dEsg
