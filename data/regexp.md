regexp


text: `'Speed',6.0,`
查找Speed后面的速度：positive lookbehind
```
grep -oP "(?<=Speed',)[^,]*" file
```

-o 仅显示匹配的字符
-P perl-like regexp
里面要匹配单引号，所以外面用双引号。`[^,]`表示除了`,`外的其他符号。
`(?<=pattern)` positive lookbehind


(?:pattern) 非获取匹配
- 匹配 pattern 但不获取匹配结果，也就是说这是一个非获取匹配，不进行存储供以后使用。这在使用 "或" 字符 (|) 来组合一个模式的各个部分是很有用。
例如， 'industr(?:y|ies) 就是一个比 'industry|industries' 更简略的表达式。

(?=pattern) look ahead positive assert
- 正向肯定预查（look ahead positive assert），在任何匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。
例如，"Windows(?=95|98|NT|2000)"能匹配"Windows2000"中的"Windows"，但不能匹配"Windows3.1"中的"Windows"。
预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

(?!pattern) look ahead negative assert
- 正向否定预查(negative assert)，在任何不匹配pattern的字符串开始处匹配查找字符串。这是一个非获取匹配，也就是说，该匹配不需要获取供以后使用。
例如"Windows(?!95|98|NT|2000)"能匹配"Windows3.1"中的"Windows"，但不能匹配"Windows2000"中的"Windows"。
预查不消耗字符，也就是说，在一个匹配发生后，在最后一次匹配之后立即开始下一次匹配的搜索，而不是从包含预查的字符之后开始。

(?<=pattern) look behind positive assert 查找pattern后面的内容，需要先匹配上pattern，但是pattern并不会返回
- 反向(look behind)肯定预查，与正向肯定预查类似，只是方向相反。例如，"(?<=95|98|NT|2000)Windows"能匹配"2000Windows"中的"Windows"，但不能匹配"3.1Windows"中的"Windows"。


(?<!pattern) look behind negative assert
- 反向否定预查，与正向否定预查类似，只是方向相反。例如"(?<!95|98|NT|2000)Windows"能匹配"3.1Windows"中的"Windows"，但不能匹配"2000Windows"中的"Windows"。

`=` positive: 需要匹配该模式
`!` negative: 不匹配该模式，否定该模式，比如查找不是xxx

`<` look behind 否则 Look ahead
