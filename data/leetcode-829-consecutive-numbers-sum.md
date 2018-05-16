# Leetcode (829) Consecutive Numbers Sum

给定正整数N，求N能分解成连续正整数和的分法有几种。

最开始想到的算法是滑动窗口，一前一后两个指针向后移动，和小于N后指针加1，和大于N前指针加1.
负责度为O(n)，但是数字太大的时候会超时。

```java
class Solution {
    public int consecutiveNumbersSum(int N) {
        int i = 1, j = 1;
        long sum = 0;
        int count = 0;
        while (i <= N/2 || j <= N) {
            if (sum < N) {
                sum += j;
                j++;
            } else if (sum > N ) {
                sum -= i;
                i++;
            } else {
                count++;
                sum += j;
                j++;
            }
        }
        return count;
    }
}

```


正确的解法是用数学的方法来解题。

k * a1 + (1+k) * k / 2 = N

(N - (1 + k) * k / 2) % k == 0


```java
class Solution {
    public int consecutiveNumbersSum(int N) {
        int count = 0;
        for (long i = 1; (i + 1) * i / 2 <= N; i++) {
            if ((N - (i + 1) * i / 2) % i == 0) {
                count++;
            }
        }
        return count;
    }
}
```

复杂度为O(n^0.5)
