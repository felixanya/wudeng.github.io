# 敏感词过滤

## DFA
Deterministic Finite Automation 确定有穷自动机。
其实就是一个简单的Trie树。对于字符串中的每个字符，查找Trie树中能否走到有单词结束标记的节点。
匹配失败的话需要回退。
Java实现：https://blog.csdn.net/liangrui1988/article/details/44873331


## AC自动机
Aho-Corasick算法，在Trie树的基础上加入了fail指针。Double Array Trie
https://github.com/anknown/ahocorasick go ac


https://github.com/gonet2/wordfilter go

https://github.com/importcjj/sensitive 就是一个简单Trie树，DFA


https://github.com/observerss/textfilter python


https://github.com/toolgood/ToolGood.Words
http://www.hankcs.com/program/algorithm/aho-corasick-double-array-trie.html
