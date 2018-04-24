# LeetCode (795) Number of Subarrays with Bounded Maximum

求数组中有多少个子数组的最大元素在L，R之间。


~~子数组的数量为N^2，所以复杂度估计至少O(N^2)。~~
这个推论不正确。如果每个位置为O(1)计算出来，最后求和也只需要O(N).



给定子数组，如何快速求最大元素？
* 遍历一遍，时间复杂度太高
* 全部存起来，空间复杂度太高
* 线段树，时间O(N^2*logN)，空间O(N)

其实这个题不需要计算每个子数组的最大元素。

O(N)

i 到i为止的数组有多少个。使用动态规划，从底向上构建。

dp[i]
* dp[i-1] 当元素小于L时，不能单独出来，将前一个扩展到当前位。
* 0 当元素大于R时
* dp[i-1] + n，(n=这个数前面连续的小于L的数的个数)

n 很容易被丢掉。要注意。


```go
func numSubarrayBoundedMax(A []int, L int, R int) int {
    sz := len(A)
    dp := make([]int, sz+1)
    ans := 0
    n := 0
    for i:=1; i<=sz; i++ {
        if A[i-1] < L {
            dp[i] = dp[i-1]
            n++
        } else {
            if (A[i-1] > R) {
                dp[i] = 0
            } else {
                dp[i] = dp[i-1] + 1 + n
            }
            n = 0
        }
        ans += dp[i]
    }
    return ans
}
```

因为dp[i]只依赖前一位，可以把空间优化掉。

```go
// dp记录当前元素结尾的符合条件的子数组个数
func numSubarrayBoundedMax(A []int, L int, R int) int {
    ans := 0
    n := 0
    dp := 0
    for i:=1; i<=len(A); i++ {
        if A[i-1] < L {
            n++
        } else {
            if (A[i-1] > R) {
                dp = 0
            } else {
                dp += 1 + n
            }
            n = 0
        }
        ans += dp
    }
    return ans
}
```



```cpp
class Solution {
public:
    int numSubarrayBoundedMax(vector<int>& A, int L, int R) {
        int result=0, left=-1, right=-1;
        for (int i=0; i<A.size(); i++) {
            if (A[i]>R) left=i;
            if (A[i]>=L) right=i;
            result+=right-left;
        }
        return result;
    }
};
```
