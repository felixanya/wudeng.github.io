# 敏感词过滤

## DFA
Deterministic Finite Automation 确定有穷自动机。
其实就是一个简单的Trie树，节点带output，对于结束的节点，把对应的字符串写在output里面。
对于字符串中的每个字符，查找Trie树中能否走到有单词结束标记的节点。匹配失败的话需要回退。

## AC自动机
Aho-Corasick算法，在Trie树的基础上加入了fail指针。Double Array Trie

node: children, output, fail point
* build a trie tree with output, when finish, every node only has one output
* build fail point
  - root node's children fail to the root, add children to a fifo queue
  - pop one node out of queue, build fail point for children of that node
    - for every child, find the closest children, starting from the fail node,
    has the character of the child node
      - if true, then point to that node, add the failed node's output to the current node
      - if false, fail to the root
    - node in queue, already build fail node
* match
  - if fail, go to the fail node, and continue the match

构造失败指针这部分比较麻烦一点。思路是从父节点的fail node开始，递归的往上找fail node，
看fail node的子节点是否有包含当前节点相同字符的节点，如果有，将该子节点设为当前节点的
fail节点，并且将fail节点的output也加到当前节点的output列表中。如果没有，一路递归到根节点。
构造的过程是一层层往下的，所以处理低层的节点的时候高层的节点都已经处理好了。
其实就是一个宽度优先的遍历。

匹配的时候从根节点开始往下找，匹配失败就递归的往上找失败节点。匹配失败目标字符串不需要回退。
类似KMP的匹配，所以效率很高。

## Erlang实现AC自动机

在[这里](https://github.com/josemic/ahocorasick.git)找到了一个erlang版本的ac自动机，
因为失败指针没有构建，所以有些情况下不能匹配。
比如：词库中有"BC"、"ABCD"这两个违禁词，但是尝试匹配"ABC"的时候发现匹配不到。
而正常的AC自动机是能够匹配上的。这是因为这里的自动机少了第二步，构造失败指针，
这样失败指针执行的节点的output也不会加入到当前节点。所以出现上面的问题。

## binary模块

> 众里寻他千百度，蓦然回首，那人却在灯火阑珊处。

AC自动机的实现有各种语言的版本，唯独Erlang的非常少。
找到一个纯Erlang实现的还只是实现了一部分。最关键的fail函数没有实现。导致匹配的时候
会出现上面的问题。

其实Erlang已经为我们实现了一个AC自动机，就在binary模块中。通过这个模块
的源码`erl_bif_binary.c`可以看到这样一段注释：
``` c
/*
 * The native implementation functions for the module binary.
 * Searching is implemented using either Boyer-Moore or Aho-Corasick
 * depending on number of searchstrings (BM if one, AC if more than one).
 * Native implementation is mostly for efficiency, nothing
 * (except binary:referenced_byte_size) really *needs* to be implemented
 * in native code.
 */
```
可以看出binary的搜索是基于BM和AC算法来实现的。如果模式只有一个就是BM，如果有多个就是AC。因此要实现替换功能，
只需要直接调用`binary:replace(Subject, Pattern, Replacement, Options)`方法即可。
另外binary还提供了一个`binary:compie_pattern(Pattern)`函数，这样可以把模式存储下来，
后续的匹配直接使用即可。不过这里每个节点其实不是一个有效的utf8字符，而只是一个字节。

```erlang
Cp = binary:compile_pattern(Patterns),
io:format("~ts",[binary:replace(Text,Cp,<<"*">>,[global])]).
```

### 参考文档
* https://blog.csdn.net/liangrui1988/article/details/44873331 Java实现
* https://github.com/importcjj/sensitive 就是一个简单Trie树，DFA
* https://github.com/gonet2/wordfilter go
* https://github.com/observerss/textfilter python
* https://github.com/toolgood/ToolGood.Words
* http://www.hankcs.com/program/algorithm/aho-corasick-double-array-trie.html
* https://github.com/anknown/ahocorasick go ac
