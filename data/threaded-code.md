Threaded Code
====

什么是Threaded Code
----
虚拟机解释器的实现技术。

* Direct threading
基于栈的虚拟机执行`push A, push B, add`。
```
thread:
  &pushA
  &pushB
  &add
  ...
pushA:
  *sp++ = A
  jump *ip++
pushB:
  *sp++ = B
  jump *ip++
add:
  addend = *--sp
  *sp++ = *--sp + addend
  jump *ip++
```
参数也可以包含在thread中，缺点是thread变大了。
```
thread:
  &push
  &A
  &push
  &B
  &add
push:
  *sp++ = *ip++
  jump *ip++
add:
  addend = *--sp
  *sp++ = *--sp + addend
  jump *ip++
```
* Indirect threading
```
thread:
  &i_pushA
  &i_pushB
  &i_add
i_pushA:
  &push
  &A
i_pushB:
  &push
  &B
i_add:
  &add
push:
  *sp++ = *(*ip + 1)
  jump *(*ip++)
add:
  addend = *--sp
  *sp++ = *--sp + addend
  jump *(*ip++)
```
* Subrouting threading
```
thread:
  call pushA
  call pushB
  call add
  ret
pushA:
  *sp++ = A
  ret
pushB:
  *sp++ = B
  ret
add:
  addend = *--sp
  *sp++ = *--sp + addend
  ret
```
* Token threading bytecode, 非常紧凑
```
bytecode:
  0 /*pushA*/
  1 /*pushB*/
  2 /*add*/
top:
  i = decode(vpc++)
  addr = table[i]
  jump *addr
pushA:
  *sp++ = A
  jump top
pushB:
  *sp++ = B
  jump top
add:
  addend = *--sp
  *sp++ = *--sp + addend
  jump top
```
实现方法
----

* Continuation-passing style (CPS) tail call optimization

参考文档
----
* [Threaded Code - Complang](https://www.complang.tuwien.ac.at/forth/threaded-code.html)
* [Context Threading](http://webdocs.cs.ualberta.ca/~amaral/cascon/CDP05/slides/CDP05-berndl.pdf)
