# 汇编

x86汇编主要有两个语法分支，AT&T和Intel。GNU系工具产生的汇编都是AT&T语法(GAS)的。两者的主要区别如下表。

|            | AT&T          | Intel         |
|------------|---------------|---------------|
| 寄存器前缀 | %eax          | eax           |
| 立即数前缀 | $5            | 5             |
| 指令后缀   | movl          | mov           |
| 参数次序   | movl $5, %eax | mov eax, 5    |
| 取址       | var           | [var]         |
| 取址       | 0x8(%eax)     | [eax + 0x8]   |
| 取址       | arr(,%eax,4)  | [eax*4 + arr] |


## 寄存器

8个通用寄存器。

| 寄存器                                  | 16位 | 32位 | 64位 |
|-----------------------------------------|------|------|------|
| 累加寄存器(Accumulator register)        | AX   | EAX  | RAX  |
| 基数寄存器(Counter register)            | CX   | ECX  | RCX  |
| 数据寄存器(Data register)               | DX   | EDX  | RDX  |
| 基址寄存器(Base register)               | BX   | EBX  | RBX  |
| 堆栈顶指针(stack pointer)               | SP   | ESP  | RSP  |
| 堆栈基指针(Stack base pointer)          | BP   | EBP  | RBP  |
| 原地址变址寄存器(source index register) | SI   | ESI  | RSI  |
| 目的地址变址(Destination index)         | DI   | EDI  | RDI  |

其中ABCD四个寄存器由可通过XH, XL来访问高低8位(X=A,B,C,D)。 下面是RAX寄存器的示意图。

```
+---------------------------------------------------------------+
| 8bits | 8bits | 8bits | 8bits | 8bits | 8bits | 8bits | 8bits |
|---------------------------------------------------------------|
|                              RAX                              |
|---------------------------------------------------------------|
|                               |              EAX              |
|---------------------------------------------------------------|
|                                               |      AX       |
|---------------------------------------------------------------|
|                                               |  AH   |   AL  |
+---------------------------------------------------------------+
```

## 其他常用寄存器

* EFLAGS, 32位，保存指令结果和处理器状态。
* IP，指令寄存器，与通用寄存器类似，EIP，RIP。

## 汇编指令

操作数的三种类型：
* 立即数Imm，以$开头
* 寄存器值Reg
* 内存值Mem

源操作数可以是这三种类型，目的操作数只能是后两种之一。源操作数和目的操作数不能同时为内存。

movq [Imm|Reg|Mem], [Reg|Mem]
* movb(byte, 8), movw(word 16), movl(long 32), movq(64)
* 寄存器寻址，movl %eax, %edx
* 立即数寻址，movl $1, %edx
* 直接寻址，movl (%ebx), %edx
* 变址寻址，movl 4(%ebx), %edx
带括号的代表寻址，有两种情况，
* 普通模式，(R), 相当于Mem[Reg[R]]，寄存器R中存放的是内存地址
* 移位模式，D(R), 相当于Mem[Reg[R]+D], 寄存器R中存放基址，D是偏移量
* 通用的寻址格式为`D(Rb, Ri, S) -> Mem[Reg[Rb]+Reg[Ri]*S+D]`
    * D 常数偏移量
    * Rb 基寄存器
    * Ri 索引寄存器
    * S 系数，1,2,4,8

三种特殊情况：
* (Rb, Ri) -> Mem[Reg[Rb]+Reg[Ri]]
* D(Rb, Ri) -> Mem[Reg[Rb]+Reg[Ri]+D]
* `(Rb, Ri, S) -> Mem[Reg[Rb]+Reg[Ri]*S]`


## 运算指令

需要两个操作数的运算
* addq Src, Dest -> Dest = Dest + Src
* subq Src, Dest -> Dest = Dest - Src
* imulq Src, Dest -> Dest = Dest * Src
* salq Src, Dest -> Dest = Dest << Src
* sarq Src, Dest -> Dest = Dest >> Src (算术右移，左边补符号位)
* shrq Src, Dest -> Dest = Dest >> Src (逻辑右移，左边补0)
* xorq Src, Dest -> Dest = Dest ^ Src
* andq Src, Dest -> Dest = Dest & Src
* orq Src, Dest -> Dest = Dest | Src

需要一个操作数的运算
* incq Dest -> Dest = Dest + 1
* decq Dest -> Dest = Dest - 1
* negq Dest -> Dest = -Dest
* notq Dest -> Dest = ~Dest

## 流程控制

四个标志位
* CF, Carry Flag
* ZF, Zero Flag
* SF, Sign Flag
* OF, Overflow Flag

##cmp

比较操作符，结果暂存在EFLAGS中。

## 跳转指令

* jmp label，无条件跳转到label
* je label, 上一次cmp相等则跳转到label
* jne label, 上一次cmp不等则跳转
* jl label, 上一次cmp小于则跳转
```
cmp $5, %eax
jl label
```
如果 R[%eax] < 5, 则跳转到label。

## call, ret
call proc, 将下一条指令地址入栈，跳转到proc地址处。
```
pushl %eip
movl proc, %eip
```
ret，将栈顶数据出栈并存入ip
```
popl %eip
```

enter, leave
----
enter, 新建一个栈帧，相当于
```
pushl %ebp
movl %esp, %ebp
```
leave, 销毁当前栈帧，相当于
```
movl %ebp, %esp
popl %ebp
```

## 其他指令

lea, load effective address
跟mov的区别在于只计算地址，不取值。
```
# R[eax] = R[ebx] - 8
lea -0x8(%ebx), %eax
```

## 过程调用
栈，从高到低，栈底为高地址，栈顶为低地址。
基本功能：
* 传递参数
* 保存返回地址
* 保存调用完需要恢复的寄存器
* 分配局部变量

## 参考文档
* https://www.mawenbao.com/note/asm-summary.html
* http://wdxtub.com/2016/04/16/thin-csapp-2/
