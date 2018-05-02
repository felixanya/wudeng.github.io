# Leetcode (504) Base 7


```go
func convertToBase7(num int) string {
    if num == 0 {
        return "0"
    } else if num > 0 {
        return helper(num)
    } else {
        return "-" + helper(-num)
    }
}


func helper(num int) string {
    ans := ""
    for num > 0 {
        r := strconv.Itoa(num % 7)
        num /= 7
        ans = r + ans
    }
    return ans
}

```
