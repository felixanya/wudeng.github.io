# Leetcode (718) Maximum Length of Repeated Subarray

LCS的解法：
```java
class Solution {
    public int findLength(int[] A, int[] B) {
        int m = A.length;
        int n = B.length;
        int dp[][] = new int[m+1][n+1];
        for (int i=0; i<=m; i++) {
            for (int j=0; j<=n; j++) {
                if (i == 0 || j == 0) {
                    dp[i][j] = 0;
                } else {
                    if (A[i-1] == B[j-1]) {
                        dp[i][j] = dp[i-1][j-1] + 1;
                    } else {
                        dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]);
                    }
                }
            }
        }
        return dp[m][n];
    }
}
```

本题跟LCS不太一样，这里要求是连续的。
```java
class Solution {
    public int findLength(int[] A, int[] B) {
        // dp[m][n] = max(dp[m][n-1], dp[m-1][n])
        int m = A.length;
        int n = B.length;
        int dp[][] = new int[m+1][n+1];
        int ans = 0;
        for (int i=0; i<=m; i++) {
            for (int j=0; j<=n; j++) {
                if (i == 0 || j == 0) {
                    dp[i][j] = 0;
                } else {
                    if (A[i-1] == B[j-1]) {
                        dp[i][j] = dp[i-1][j-1] + 1;
                        ans = ans > dp[i][j] ? ans : dp[i][j];
                    } else {
                        dp[i][j] = 0;
                    }
                }
            }
        }
        return ans;
    }
}
```