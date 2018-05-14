# Leetcode (97) Interleaving String


递归解法超时。
只能用动态规划了。
```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int ie = s1.length();
        int je = s2.length();
        int ke = s3.length();
        if (je + ie != ke) {
            return false;
        } else {
            return helper(s1, s2, s3, 0, ie-1, 0, je-1, 0);
        }
    }

    public boolean helper(String s1, String s2, String s3, int i, int ie, int j, int je, int k) {
        if (i > ie && j > je) {
            return true;
        }
        return (i <= ie && s1.charAt(i) == s3.charAt(k) && helper(s1, s2, s3, i+1, ie, j, je, k+1)) ||
            (j <= je && s2.charAt(j) == s3.charAt(k) && helper(s1, s2, s3, i, ie, j+1, je, k+1));
    }
}
```

dp[i][j] 表示 s1的前i个字符，s2的前j个字符是否能表示 s3的前i+j个字符。

dp[i][j] 
