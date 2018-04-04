# Erlang Maps

OTP 17 开始引入。

`M#{K => V}`，如果K不存在，创建K，如果已经存在，更新K
`M#{K := V}`，更新K，如果K不存在，抛出异常。
模式匹配：
`#{name := Name} = M2`
`#{} = M2` M2为map

```
M1 = #{name => adam, age => 24, date => {july, 29}}.
maps:get(name, M1).
M2 = maps:update(age, 25, M1).
map_size(M2).
map_size(#{}).
```

is_map(M)
map_size(M)


| Maps Module | Maps Syntax |
| ------------|-------------|
| maps:new/0 | #{} |
| maps:put/3 | Map#{Key => Val} |
| maps:update/3 | Maps#{Key := Val} |
| maps:get/2 | Map#{Key} |
| maps:find/2 | #{Key := Val} = Map |

map不能直接存到ets表中。只能作为tuple的一部分写入ets表。

map comprehensions:

```erlang
[X || X := fog <- Weather].
```

# 参考文档
* http://erlang.org/doc/man/maps.html
* http://erlang.org/documentation/doc-6.0/doc/reference_manual/maps.html
