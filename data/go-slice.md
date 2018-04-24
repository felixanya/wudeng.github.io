# go slice

slice是底层数组的一个视图。可以看做是三部分构成的：数组地址，len，cap。

跟python不一样，go不支持负数索引。
* len(s)
* cap(s)

[]sting{"hello", "world"}

s := make([]int, 5)
s := make([]int, len, cap)

* copy
```go
b = make([]T, len(a))
copy(b, a)
// or
b = copy([]T{nil}, a...)

```

* 反转slice：
```go
for left, right := 0, len(a)-1; left < right; left, right = left+1, right-1 {
	a[left], a[right] = a[right], a[left]
}
```

* 删除元素
删除元素有两种方式：快速的方式就是直接把最后一个元素替换需要删除的元素，然后删除最后一个元素。
```go
a := []string{"a","b","c"}
i := 2
a[i] = a[len(a)-1]
a = [:len(a)-1]
```

第二种方法是把需要删除元素后面的元素全部前进一位。这种方式比较慢。
```go
a := []string{"a","b","c"}
i := 2
copy(a[i:], a[i+1:])
a[len(a)-1] = ""
a = a[:len(a)-1]

// or
a = append(a[:i], a[i+1:]...)
```

append(s, elment)
append(s, elements...)
append([]T{x}, a...)


如果通过函数来修改slice，如果只是修改slice中对应的元素是没有问题的。但是如果需要修改视图大小，
比如往后面append一个元素。这种情况下只能通过传递指针来实现。


## 排序

[sort](https://golang.org/pkg/sort/)模块

Len() int
Swap(i, j int)
Less(i, j int) bool

sort.Sort()

sort.Slice()

原地排序
func Ints(a []int)


## 参考文档
* https://nanxiao.gitbooks.io/golang-101-hacks/content/posts/pass-slice-as-a-function-argument.html
