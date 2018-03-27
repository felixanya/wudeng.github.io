# 游戏开发中的人工智能


## 追逐和闪躲


## 移动模式


## 群聚


## 以势函数实现移动


## 基本路径寻找及航点应用


## A*路径寻找算法


## 描述式AI及描述引擎


## 有限状态机


## 模糊逻辑


## 规则式AI

## 概率概论


## 不确定状态下的决策：贝叶斯技术


## 神经网络

优点：
* 简化复杂状态机或规则系统的程序编写
* AI自适应

未广泛使用，原因：
* 难控制，难预测，难测试，难调试

决策：是否和玩家交战
* chase
* evade
* flock
* attack

三层前馈神经网络，multilayer feed-forward network
* 输入层input
    * 输入数据 转换成实数 
* 隐匿层hidden
* 输出层output

除了输入层，每个神经元都有一个额外的输入值叫偏差值(bias)。

活化函数：接收神经元的总输入值，予以处理，产生神经元的输出结果。非线性。
* logistic aka sigmoid(S shape function)
* 阶跃函数
* 双曲正切函数


## 遗传算法 genetic algorithm

适应度函数

Problem：平衡: 技巧水平 => 挑战性
演化evolution

交叉crossover
突变

四步
* first generation: 多样化很重要
* rank fitness: fitness function
* selection 
* evolution: 组合，随机突变
后三部构成一个循环。

不可预测性，试误法。




* [Game AI Pro 3](https://books.google.co.uk/books?id=kFAsDwAAQBAJ&printsec=frontcover#v=onepage&q&f=false)
