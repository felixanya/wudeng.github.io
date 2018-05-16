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

动态规划，二维空间：

```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int m = s1.length();
        int n = s2.length();
        if (m + n != s3.length()) {
            return false;
        }
        boolean dp[][] = new boolean[m+1][n+1];
        for (int i=0; i<=m; i++) {
            for (int j=0; j<=n; j++) {
                if (i==0 && j==0) {
                    dp[i][j] = true;
                } else if (i == 0) {
                    dp[i][j] = dp[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1);
                } else if (j == 0) {
                    dp[i][j] = dp[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1);
                } else {                
                    dp[i][j] = (dp[i][j-1] && s2.charAt(j-1) == s3.charAt(i+j-1)) || (dp[i-1][j] && s1.charAt(i-1) == s3.charAt(i+j-1));
                }
            }
        }
        return dp[m][n];
    }
}
```

由于只动态规划的过程只依赖当前行和上一行，可以优化掉一维空间：
```java
class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int m = s1.length();
        int n = s2.length();
        if (m + n != s3.length()) {
            return false;
        }
        boolean dp[] = new boolean[n+1];
        for (int i=0; i<=m; i++) {
            for (int j=0; j<=n; j++) {
                if (i==0 && j==0) {
                    dp[j] = true;
                } else if (i == 0) {
                    dp[j] = dp[j-1] && s2.charAt(j-1) == s3.charAt(i+j-1);
                } else if (j == 0) {
                    dp[j] = dp[j] && s1.charAt(i-1) == s3.charAt(i+j-1);
                } else {                
                    dp[j] = (dp[j-1] && s2.charAt(j-1) == s3.charAt(i+j-1)) || (dp[j] && s1.charAt(i-1) == s3.charAt(i+j-1));
                }
            }
        }
        return dp[n];
    }
}
```
