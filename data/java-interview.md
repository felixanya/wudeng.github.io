

# vector vs array

vector 线程安全
array 非线程安全

# HashMap vs HashTable

HashMap 非线程安全，允许null key
HashTable 线程安全，不允许null key

## vilotile
* 对变量的写不依赖当前值，so，不能用作线程安全计数器
* 该变量没有包含在其他变量的不变式中，比如两个变量，lower < higher，就是一个不变式

一般用于：
* 状态标记
* 一次性安全发布
* 独立观察

乐观锁主要有哪些？

== 比较的是引用，只有指向同一堆对象时为true.
Integer(100) == Integer(100) true
Integer(150) == Integer(150) false 大于127，创建新对象

equals比较的是值。

final 修饰变量、函数、类
finally 异常也会执行
finalize 被gc前执行

hashcode

nio

java8
