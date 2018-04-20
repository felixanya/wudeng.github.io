# Golang中省略号的使用

省略号ellipsis`...`出现在Go中的四种情况。

##  Variadic function parameters

> The final parameter in a function signature may have a type prefixed with ....
A function with such a parameter is called variadic and may be invoked with zero or more arguments for that parameter.

可变参数函数声明，函数的 **最后** 一个参数如果是类型：`...T`，调用它的时候可以在后面传任意个数的T类型的参数。
在函数内部，`...T`被当成是`[]T`来处理。省略号在类型前面，表示接受任意数量的类型为T的参数。

```go
func Sum(nums ...int) int {
    res := 0
    for _, n := range nums {
        res += n
    }
    return res
}
//调用函数的时候直接传整数，数量随意。
Sum(1, 2, 3)
Sum()
//如果是传slice，需要在数组后面加`...`
```

## Arguments to variadic functions
可变参数函数调用，可以直接传slice给可变参数函数，不过需要用`...`来unpacking。省略号在slice后面，表示unpack。
```go
nums := []int{1, 2, 3}
Sum(nums...)
```
append函数就是一个典型的例子：
```go
var buf []byte
buf = append(buf, 'a', 'b')
buf = append(buf, "cd"...) // without ..., it will try to append "cd" as a whole, which is invalid, cos buf is slice of byte, not slice of string
fmt.Println(buf) // [97,98,99,100]
```

## Array Literal
这种情况下，`...`代表一个等于数组元素的个数。

```go
stooges := [...]string{"Moe", "Larry", "Curly"}
// len(stooges) == 3
```

## Go Command

Three dots are used by the go command as a wildcard when describing package lists.
Example: Tests all packages in the current directory and its subdirectories:
```shell
$ go test ./...
```

## 参考文献
* http://programming.guide/go/three-dots-ellipsis.html
