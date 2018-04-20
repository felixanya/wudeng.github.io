# golang strconv

## 10进制转换
* strconv.Atoi(s string) (int, error)
* strconv.Itoa(i int) string

## 字符串转基础类型
* strconv.ParseBool(str string) (bool, error)
* strconv.ParseFloat(s string, bitSize int) (float64, error)
* strconv.ParseInt(s string, base int, bitSize int) (int64, error)
* strconv.ParseUint(s string, base int, bitSize int) (uint64, error)

## 基础类型转字符串
* strconv.FormatBool(b bool) string
* strconv.FormatFloat(f float64, fmt byte, prec, bitSize int) string
* strconv.FormatInt(i int64, base int) string
* strconv.FormatUint(i uint64, base int) string

## string

string: utf-8 encoded sequence of characters.

* len(str)
* str[i] 不能被赋值
* []rune(str) 将string转化为字符slice。slice每个元素是一个字符。
* []byte(str)
* `Raw string literals`

string是utf8string。len返回字节数。如果要遍历字符，要用range而不是for loop。

## 参考文献
* https://golang.org/pkg/strconv/
* http://programming.guide/go/string-functions-reference-cheat-sheet.html
