# Leetcode (611) Valid Triangle Number

最简单的方法就是先排好序，所有组合遍历一次，复杂度为O(n^3)，因为排好了序，所以可以优化到O(n^2logn)。

```java
class Solution {
    public int triangleNumber(int[] nums) {
        int ans = 0;
        Arrays.sort(nums);
        int len = nums.length;
        for (int i = 0; i < len; i++) {
            if (nums[i] > 0 ) {                
                for (int j = i+1; j< len; j++) {
                    for (int k = j+1; k<len; k++) {
                        if (nums[i] + nums[j] > nums[k]) {
                            ans++;
                        }
                    }
                }
            }
        }
        return ans;
    }
}
```

305ms，显然还有优化的空间。

```java
class Solution {
    public int triangleNumber(int[] nums) {
        int ans = 0;
        Arrays.sort(nums);
        int len = nums.length;
        for (int i = 0; i < len - 2; i++) {
            if (nums[i] > 0 ) {                
                for (int j = i+1; j< len-1; j++) {
                    ans += helper(nums, j+1, len-1, nums[i] + nums[j]);
                }
            }
        }
        return ans;
    }

    int helper(int []nums, int lo, int hi, int sum) {
        int low = lo;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] >= sum) {
                hi = mid - 1;
            } else if (nums[mid] < sum) {
                lo = mid + 1;
            }
        }
        return lo - low;
    }
}
```

时间为70ms.
二分需要注意防止死循环。

二分，如果目标在终点，如何防止死循环？
两种情况，上面采用的是[lo, hi]

下面来看一个[lo, hi)的实现：
```java
class Solution {
    public int triangleNumber(int[] nums) {
        int ans = 0;
        Arrays.sort(nums);
        int len = nums.length;
        for (int i = 0; i < len - 2; i++) {
            if (nums[i] > 0 ) {                
                for (int j = i+1; j< len-1; j++) {
                    ans += helper(nums, j+1, len, nums[i] + nums[j]);
                }
            }
        }
        return ans;
    }

    int helper(int []nums, int lo, int hi, int sum) {
        int low = lo;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (nums[mid] >= sum) {
                hi = mid;
            } else if (nums[mid] < sum) {
                lo = mid + 1;
            }
        }
        return lo - low;
    }
}
```

二分要注意的点：
* 退出条件跟区间的开闭相关，开区间没有等号，闭区间有等号
* 防止死循环。要么lo右移，要么hi左移。
* 不管是开区间还是闭区间，mid的计算方法是一样的。

二分的方法还是不够快。我们忽略了一个事实。最后那个元素的坐标，上限会越来越大。
所以不需要回退去查找。

对于每一个i：jk这两个值是同步后移的，所以复杂度为O(N^2)。

```java
class Solution {
    public int triangleNumber(int[] nums) {
        int ans = 0;
        Arrays.sort(nums);
        int len = nums.length;
        for (int i = 0; i < len - 2; i++) {
            if (nums[i] > 0 ) {                
                int j = i+1;
                int k = j+1;
                for (; j< len-1; j++) {
                    while (k < len && nums[k] < nums[i] + nums[j]) {
                        ans += k - j;
                        k++;
                    }
                }
            }
        }
        return ans;
    }
}
```
29ms

