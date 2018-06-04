# Leetcode (520) Detect Capital

```java
class Solution {
    boolean isUpper(String w, int i) {
        char c = w.charAt(i);
        return c >= 'A' && c <= 'Z';
    }
    
    public boolean detectCapitalUse(String word) {
        int len = word.length();
        if (len <= 1) return true;
        boolean first = isUpper(word, 0);
        boolean second = isUpper(word, 1);
        for (int i=2; i<len; i++) {
            if (isUpper(word, i) != second) {
                return false;
            }
        }
        return first || !second;        
        
        
    }
}
```
35ms