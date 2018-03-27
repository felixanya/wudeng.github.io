怎样实现一个虚拟机
====

我们来实现一个支持加法和乘法的虚拟机。利用Erlang的erl_scan和erl_parse可以帮助我们实现一个简单的编译器，把表达式编译成可以被虚拟机执行的代码。
从输入的表达式到可以执行的虚拟机代码，经历了以下几个过程：
* 词法分析，将输入的符号序列解析成有意义的符号，对应Erlang里面的erl_scan模块。
* 语法分析，词法分析得到的符号转化成抽象语法树（AST），对应Erlang里面的erl_parse模块。
* 代码生成
* 解释执行

Erlang模拟栈式虚拟机
----
```
-module(stack_machine).
-compile(export_all).

eval(String) ->
    {ok, Tokens, _} = erl_scan:string(String++"."),
    {ok, [ParseTree]} = erl_parse:parse_exprs(Tokens),
    Code = generate_code(ParseTree),
    interpret(Code).

interpret(Code) -> interpret(Code, []).

interpret([], [Value|_]) -> Value;
interpret([push, Arg|Rest], Stack) -> interpret(Rest, [Arg|Stack]);
interpret([add|Rest], [Arg1, Arg2|Stack]) -> interpret(Rest, [Arg1 + Arg2|Stack]);
interpret([minus|Rest], [Arg1, Arg2|Stack]) -> interpret(Rest, [Arg1 - Arg2|Stack]);
interpret([multiply|Rest], [Arg1, Arg2|Stack]) -> interpret(Rest, [Arg1 * Arg2|Stack]);
interpret([divide|Rest], [Arg1, Arg2|Stack]) -> interpret(Rest, [Arg1 / Arg2|Stack]).

generate_code({op, _, '+', Arg1, Arg2}) -> generate_code(Arg1) ++ generate_code(Arg2) ++ [add];
generate_code({op, _, '-', Arg1, Arg2}) -> generate_code(Arg2) ++ generate_code(Arg1) ++ [minus];
generate_code({op, _, '*', Arg1, Arg2}) -> generate_code(Arg1) ++ generate_code(Arg2) ++ [multiply];
generate_code({op, _, '/', Arg1, Arg2}) -> generate_code(Arg2) ++ generate_code(Arg1) ++ [divide];
generate_code({integer, _, Arg1}) -> [push, Arg1];
generate_code({float, _, Arg1}) -> [push, Arg1].

```
上面的代码模拟了一个简单的栈式虚拟机，操作数保存在栈中。然后能够执行简单的加减乘除，输入有效的算术表达式，能够给出最终结果。

生成字节码
----
上文中实现的虚拟机在执行生成的代码的时候利用了Erlang的模式匹配， 实际中我们使用的Beam虚拟机执行的字节码文件，需要为每个指令分配一个指令码。
虚拟机通过case语句来分派指令。下面我们来实现一个基于字节码的虚拟机。

首先需要生成字节码，还是利用Erlang来编译表达式，生成文件。
```
-module(bytecode_generator).

-compile([export_all]).

gen(String) ->
    {ok, Tokens, _} = erl_scan:string(String++"."),
    {ok, [ParseTree]} = erl_parse:parse_exprs(Tokens),
    ByteCode = generate_code(ParseTree),
    file:write_file("bytecode", [ByteCode, stop()]),
    ok.

generate_code({op, _, '+', Arg1, Arg2}) -> 
    generate_code(Arg1) ++ generate_code(Arg2) ++ [add()];
generate_code({op, _, '*', Arg1, Arg2}) -> 
    generate_code(Arg1) ++ generate_code(Arg2) ++ [multiply()];
generate_code({integer, _, Arg1}) -> 
    [push() | integer(Arg1)].

add() -> 1.
multiply() -> 2.
push() -> 3.
stop() -> 0.

integer(Num) ->
    NumList = binary_to_list(binary:encode_unsigned(Num)),
    [length(NumList) | NumList].
```
我们定义了4个不同的指令，分别是add，multiply，push，stop，用不同的字节码表示。为了支持大于255的整数，我们将整数编译成长度加整数编码的形式。
通过上面的gen函数，我们可以为表达式生成一个名为bytecode的字节码文件。下面我们来解释执行这个文件。

switch-case指令分派
----

```
#include <cstdio>
#include <cstdlib>

#define ADD 1
#define MUL 2
#define PUSH 3
#define STOP 0

char *read_file(char *filename) {
    FILE *file = fopen(filename, "r");
    if (!file) {exit(-1);}
    fseek(file, 0, SEEK_END);
    long size = ftell(file);
    char *code = (char *)calloc(size, 1); 
    if(!code) {exit(-1);}
    fseek(file, 0, SEEK_SET);
    fread(code, size, size, file);
    close(file);
    return code;
}


int run(char *code) {
    int stack[1000];
    int sp = 0, size = 0, val = 0;
    char *ip = code;
    int op1 = 0, op2 = 0;

    while (*ip != STOP) {
        switch (*ip++) {
            case ADD: 
                op1 = stack[--sp];
                op2 = stack[--sp];
                stack[sp++] = op1 + op2;
                break;
            case MUL: 
                op1 = stack[--sp];
                op2 = stack[--sp];
                stack[sp++] = op1 * op2;
                break;
            case PUSH:
                size = *ip++; 
                val = 0;
                while (size--) { 
                    val = (val << 8) + *ip++;
                }   
                stack[sp++] = val;
                break;
        }   
    }   
    return stack[--sp];
}

int main() {
    int result = 0;
    char *code = read_file("bytecode");
    //printf("read code %s\n", code);
    result = run(code);
    printf("result %d\n", result);
    free(code);
    return 0;
}
```
关键代码是中间的switch-case语句，这种方式实现的虚拟机需要不停的比较指令来执行相应操作，每条指令都需要跟所有指令比较一次，如果有好几百条指令，这样的效率不高。
Beam应用了一种叫做dynamic threaded code的技术，加载文件的时候直接将指令替换成执行相应指令的代码地址。

token-threaded code
----
要去掉switch-case语句，我们需要在加载代码的时候做更多的工作。

```
#include <cstdlib>
#include <cstdio>

#define ADD 1
#define MUL 2
#define PUSH 3
#define POP 4
#define STOP 0

typedef void (*instructionp_t)(void);

int stack[1000];
instructionp_t *ip;
int running;
int sp = 0;

#define pop() stack[ --sp ]
#define push(x) stack[ sp++ ] = (x)


void add() {
    int x, y;  
    x = pop(); 
    y = pop(); 
    push(x+y);
}
void multiply() {
    int x, y;  
    x = pop(); 
    y = pop(); 
    push(x*y);
}
void pushi() {
    long x;  
    x = (long) (*ip++); 
    push(x);
}

void stopi() {
    running = 0;
}

instructionp_t *read_file() {
    instructionp_t *code;
    instructionp_t *cp;
    char c;
    int val = 0, len = 0;
    FILE *file;
    long size = 0;
    file = fopen("bytecode","r");
    if (!file) {
        printf("open file error");
        exit(-1);
    }   
    fseek(file, 0, SEEK_END);
    size = ftell(file);
    fseek(file, 0, SEEK_SET);
    code = (instructionp_t *)calloc(size, sizeof(instructionp_t));
    cp = code;
    while((c = fgetc(file)) != STOP) {
        switch(c) {
            case ADD:
                *cp++ = &add;
                break;
            case MUL:
                *cp++ = &multiply;
                break;
            case PUSH:
                *cp++ = &pushi;
                len = fgetc(file);
                val = 0;
                while(len-- > 0) {
                    val = val*256 + fgetc(file);
                }
                *cp++ = (instructionp_t)val;
                break;
        }
    }
    *cp = &stopi;
    fclose(file);
    return code;

}

int run() {
    sp = 0;
    running = 1;
    while(running) (*ip++)();
    return pop();
}

int main() {
    int result = 0;
    ip = read_file();
    result = run();
    printf("resutl:%d\n", result);
    return 0;
```
这种方式，我们加载字节码的时候直接将字节码替换成需要执行的函数指针，最终执行的时候只需要从头到尾执行ip指针指向的函数即可。

参考文档
----
* theBeamBook
