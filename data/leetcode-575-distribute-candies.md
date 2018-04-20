# Leetcode (575) Distribute Candies

给定一个长度为偶数的数组，里面不同的数字代表不同类型的糖果。把糖果平均分给男孩女孩，问女孩能拿到的最多的糖果种类是多少。
糖果类型的范围是[-100000, 100000]。

这个题题目很长，其实简单的难以置信。女孩能分到一半糖果，所以不会超过数组长度的一半。然后统计一下糖果出现的种类，求两者的最小值即可。

统计个数这个可以用map，也可以直接开一个数组。因为其实范围很小。可以把糖果类型映射到[0, 200000]，然后下标表示一种类型的糖果。
遍历一遍统计出现的格式即可。为了节省空间可以bool类型的数组。


```go
func distributeCandies(candies []int) int {
    n1 := len(candies) / 2
    bit := make([]bool, 200001)
    ans := 0
    for _, n := range candies {
        if !bit[n + 100000] {
            bit[n+100000] = true
            ans += 1
        }
    }
    if ans > n1 {
        ans = n1
    }
    return ans
}
```

这个题用map也是可以的，不过map的性能差一点：
```go
func distributeCandies(candies []int) int {
    n1 := len(candies) / 2
    m := make(map[int]bool)
    ans := 0
    for _, n := range candies {
        if !m[n] {
            m[n] = true
            ans += 1
        }
    }
    if ans > n1 {
        ans = n1
    }
    return ans
}
```
