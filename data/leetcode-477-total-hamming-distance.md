```java
class Solution {
    public int totalHammingDistance(int[] nums) {
        int a[] = new int[32];
        for (int i=0; i<32; i++) {
            a[i] = 0;
        }
        int ans = 0;
        for (int i=0; i<nums.length; i++) {
            for (int j=0; j<32; j++) {
                if ((nums[i] & (1 << j)) != 0) {
                    a[j] += 1;
                }
            }
        }
        for (int i=0; i<32; i++) {
            ans += a[i] * (nums.length - a[i]);
        }
        return ans;
        
    }
}
```

55ms,
优化：内外循环交换一下：


```java
class Solution {
    public int totalHammingDistance(int[] nums) {
        int ans = 0, n = nums.length;
        for (int j=0; j<32; j++) {
            int d = 0;
            for (int i=0; i<n; i++) {
                if ((nums[i] & (1 << j)) != 0) {
                    d++;
                }
            }
            ans += (n - d) * d;
        }
        return ans;
        
    }
}
```
47ms

去掉if。length抽出变量。交换循环变量。
```java
class Solution {
    public int totalHammingDistance(int[] nums) {
        int ans = 0, n = nums.length;
        for (int j=0; j<32; j++) {
            int d = 0;
            for (int i=0; i<n; i++) {
                d+=(nums[i] >> j) & 1;                
            }
            ans += (n - d) * d;
        }
        return ans;
        
    }
}
```
19ms


