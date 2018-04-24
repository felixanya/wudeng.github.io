# LeetCode (506) Relative Ranks


给定一个数组，每个元素代表分数，求出排名，前三名分别输出"Gold Medal", "Silver Medal", "Bronze Medal".
分数都是唯一的。

## 分析过程
要保持原数组的位置，所以需要新建一个数组来保存排序后的结果。
并且用map保存每个数字的排名。

```go
func findRelativeRanks(nums []int) []string {
    sz := len(nums)
    dup := make([]int, sz)
    copy(dup, nums)
    sort.Slice(dup, func(i, j int) bool {
        return dup[i] > dup[j]
    })
    rank := make(map[int]int)
    for idx, score := range(dup) {
        rank[score] = idx + 1
    }
    res := make([]string, sz)
    for idx, score := range(nums) {
        r := rank[score]
        switch r {
        case 1:
            res[idx] = "Gold Medal"
        case 2:
            res[idx] = "Silver Medal"
        case 3:
            res[idx] = "Bronze Medal"
        default:
            res[idx] = strconv.Itoa(r)
        }
    }
    return res
}
```
