# EUnit

要使用EUnit，需要包含头文件：`-include_lib("eunit/include/eunit.hrl").`：
* 自动导出`test()`函数，这个函数用来执行所有测试用例。这个函数是自动生成的，其实就是执行`eunit:test(Module).`
* 自动导出函数`..._test()`或者`...test_()`
* 引入宏

测试代码可以直接写在模块中，也可以将测试代码分离到专门的测试模块：`Mod_tests.erl`，注意后面有s。

## 开关测试
关闭单元测试可以直接编译的时候定义NOTEST宏，也可以定义头文件`common.hrl`：
```erlang
-define(NOTEST, true).
-include_lib("eunit/include/eunit.hrl").
```
在其他源文件包含这个头文件则默认不打开Eunit。如果需要打开，直接在编译的时候加上宏定义：
`erlc -DTEST my_module.erl`，因为TEST优先级比NOTEST高，所以会覆盖头文件中的NOTEST定义。

## Macros
通过宏的使用增强输出消息。这些宏都有对应的生成器宏，也就是下划线开头的宏。

* ?assert(Expression)   true
* ?assertNot(Expression)    false
* ?assertEqual(Expect, Expr)
* ?assertNotEqual(A, B)
* ?assertMatch(Guard, Expr)
* ?assertError(Pattern, Expression)
* ?assertThrow(Pattern, Expression)
* ?assertExit(Pattern, Expression)
* ?assertException(Class, Pattern, Expression)

## Simple Test Function

`_test()` 结尾的函数。

`eunit:test(Module).`

## Test Generators
以`_test_()`结尾的函数称为测试生成函数。
`?_assert(A == B)` 称为测试生成器（注意前面的下划线）。等价于`fun() -> ?assert(A == B) end`。

测试生成函数可以返回：
* 测试生成器、测试生成器的列表
* Fixture、Fixture的列表

只测试一部分：
`eunit:test({generator, fun ops_tests:add_test_/0})`

test representation
* {generator, Fun}
* {moduel, Mod}
* {dir, Path}
* {file, Path}
* {generator, Fun}
* {application, AppName}


## Fixtrues

Setup fixture:
* {setup, Setup, Instantiator}
* {setup, Setup, Cleanup, Instantiator}
* {setup, Where, Setup, Instantiator}
* {setup, Where, Setup, Cleanup, Instantiator}

foreach fixture:
* {foreach, Where, Setup, Cleanup, [Instantiator]}
* {foreach, Setup, Cleanup, [Instantiator]}
* {foreach, Where, Setup, [Instantiator]}
* {foreach, Setup, [Instantiator]}


名词解释：
* Setup: A function that takes no argument. Each of the tests will be passed the value returned by the setup function
* Cleanup: A function that takes the result of a setup function as an argument, and takes care of cleaning up whatever is needed
* Instantiator: A function that takes the result of a setup function and returns a test set
* Where: specifies how to run the tests:
    - local
    - spawn
    - {spwan, node()}


## 参考文档
* http://learnyousomeerlang.com/eunit
* http://erlang.org/download/eunit.hrl
* erl9.2/lib/eunit-2.3.5/doc/html/chapter.html
