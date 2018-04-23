# Leetcode (462) Minimum Moves to Equal Array Elements II

给定一个非空整数数组，对数组中的元素一次增加，一次减少，都可以称之为一次move。
求最少多少次move可以让一个数组所有元素相等。

## 分析过程

关键在于怎么去选择一个目标值，让所有元素向它靠拢。这个目标值的范围肯定在[min, max]这个区间。
min，max分别代表数组中的最小和最大元素。
不难想到暴力求解的方法：遍历min到max的每个元素，每个尝试一下，如果数值相差太大，复杂度太高。肯定超时。

给定一个目标值，目标值加1，所有小于等于目标值元素的差值，都要增加1。所有比目标值大的元素差值都要减1.

所以给定一个值k，怎么快速的知道，哪些值小于等于这个k，哪些值大于这个k？
排好序，二分搜索？是一种办法。

目标值能不能只取数组中的元素？其实是可以的。可以证明。当目标值不在数组中的时候，肯定存在两个最靠近目标值的元素是落在数组中的。
假设，目标值为k, 最接近k比k小的为a, 最接近k比k大的为b, a, b都是数组中的元素。假设小于等于a的元素个数记为na, 大于等于b的元素记为nb
* na == nb，k取a和b之间的任意值其实都不影响大局。
* na > nb, k取a好过任意(a, b]区间的值。
* na < nb，k取b好过任意[a, b)区间的值。

三种情况下都有，取端点值好过起码是不弱于取中间某个值。所以我们把最差情况复杂度降到了O(n^2)。其实是一个可以接受的结果。

```go
func minMoves2(nums []int) int {
    ans := math.MaxInt32
    for _, num := range(nums) {
        move := calcMove(nums, num)
        if move < ans {
            ans = move
        }
    }
    return ans
}

func calcMove(nums []int, target int) int {
    total := 0
    for _, num := range(nums) {
        if num > target {
            total += num - target
        } else {
            total += target - num
        }
    }
    return total
}
```

结果是可以AC，但是时间有点多，花了488ms。肯定有优化空间。


## 优化

先排序，找到数值中的一个数k，使得大于k的数个数跟小于k的数个数尽量接近。这个k就是我们要找的值。
这不就是排好序数值最中间的那个值么？

如果数组大小为奇数，那么直接就找到了。如果为偶数？还得比较一下？
其实不需要啦，根据之前的分析知道两个结果是一样。

```go
func minMoves2(nums []int) int {
    sort.Ints(nums)
    return calcMove(nums, nums[len(nums)/2])
}

func calcMove(nums []int, target int) int {
    total := 0
    for _, num := range(nums) {
        if num > target {
            total += num - target
        } else {
            total += target - num
        }
    }
    return total
}
```

复杂度为排序算法的O(nlogn)，时间变为16ms，打败了62.5%的提交。

有人给出了一个更加简便的方法，直接排序以后，从两端向中间靠拢。用后一个值减去前面的值。将差累加起来就是答案。

```go
func minMoves2(nums []int) int {
    sort.Ints(nums)
    ans := 0
    for i, j := 0, len(nums) - 1; i < j; {
        ans += nums[j] - nums[i]
        i++
        j--
    }
    return ans
}
```

```python
def minMoves2(self, nums):
    nums.sort()
    return sum(nums[~i] - nums[i] for i in range(len(nums) / 2))
```
python中波浪线表示取反。~0=-1, ~1=-2，~2=-3，表示从数组从后往前取值。

复杂度还是O(nlogn)，因为要排序。运行时间为12毫秒。

## 继续优化
我们需要一个全部拍好序的数组么？不需要。我们只需要找到数组的中位数而已。这不就是经典的中位数求解问题么？
复杂度为O(n)。那么问题来了，怎么快速找到一个未排序数组的中位数呢？


## 相关问题
* Leetcode 215 Kth Largest Element in an Array
