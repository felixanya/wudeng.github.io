# Leetcode(738) Monotone Increasing Digits

给定一个正整数N，找到不大于N的最大整数X，使得X的数字从高位到低位满足 x <= y，其中N的范围为[0, 10^9]

## 解题思路
按照直观的感觉，只能从高位向低位一层层尝试，满足约束条件输出结果，类似经典的八皇后回溯问题。
所以这个题应该也是回溯法解决。回溯法基本都有固定的套路了。

需要实现一个helper函数。利用递归，一层层递推下去。helper函数需要返回当前的尝试是否成功。如果成功，往下递推。
如果失败，往上回溯。为了方便计算约束条件，可以将数字提取出来，这里将整数转化成slice，求出一个符合条件的slice，
最后将slice转化为整数。也就是需要求解的答案。

* 第一步：首先需要将N转化为数字数组，方便处理。

不需要提前计算位数。直接分配10位。利用求余，除法得到数组。为了思路更加直观需要反转slice。
或者直接构造一个高位在前的slice。

```go
// 逆转slice，利用go的multi assignment非常方便，交换值不需要中间变量
func reverse(a []int) {
    for i, j := 0, len(a) - 1; i < j; i, j = i + 1, j - 1 {
        a[i], a[j] = a[j], a[i]
    }
}
```

* 第二部：构造helper函数
从高位到低位层层递进。i从0到len-1，每一位从9到0尝试。看是否满足条件。
条件判断的依据是前i位构造的数字不大于N的前i位构造的数。因为后面的值肯定要大于等于前面的值，
所以维护一个min最小值。小于这个值的不考虑。

* 第三步：根据输出的slice构造答案。

```go
func monotoneIncreasingDigits(N int) int {
	arr := to_array(N)
	size := len(arr)
	out := make([]int, size)
	helper(arr, 0, size, 0, out, 0, 0)
	ans := 0
	for i := 0; i < len(out); i++ {
		ans = ans*10 + out[i]
	}
    return ans
}

func to_array(i int) []int {
	arr := make([]int, 0, 10)
	for i > 0 {
		arr = append([]int{i % 10}, arr...)
		i = i / 10
	}
	return arr
}

func helper(arr []int, i, size, min int, out []int, acc1, acc2 int) bool {
	if i < size {
		acc2 = acc2*10 + arr[i]
		for j := 9; j >= min; j-- {
			tmp := acc1*10 + j
			if tmp <= acc2 {
				out[i] = j
                // 递推推进，失败的话尝试下一个值
				if helper(arr, i+1, size, j, out, tmp, acc2) {
					return true
				}
			}
		}
        // 如果遍历了所有可能性都没有找到解，返回false
		return false
	}
	return true
}
```

优化空间：无需手动转成slice。直接用strconv转成字符串。然后将字符串强制转换为[]byte类型。
最后把[]byte转换回字符串然后用strconv转换成整数。

## 参考答案
高票答案的思路是这样的。从后往前扫描，如果发现相邻的n_str[i] < n_str[i-1]，我们就把n_str[i-1]减1，
记录i的位置，从i开始往后全部替换成9。另外也不需要转化成整数数组。直接转成字符串。所以其实这个题是有规律的，
用回溯法有种高射炮打蚊子的感觉。

```go
func monotoneIncreasingDigits(N int) int {
    s := []byte(strconv.Itoa(N))
    marker := len(s)
    for i := len(s) - 1; i > 0; i-- {
        if (s[i-1] > s[i]) {
            s[i-1] -= 1
            marker = i
        }
    }
    for i := marker; i < len(s); i++ {
        s[i] = '9'
    }
    ans, _ := strconv.Atoi(string(s))
    return ans
}
```
