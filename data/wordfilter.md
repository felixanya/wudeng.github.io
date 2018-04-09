

DFA

AC自动机
node: children, output, fail point
* build a tri tree with output, when finish, every node only has one output
* build fail point
  - root node's children fail to the root, add children to a fifo queue
  - pop one node out of queue, build fail point for children of that node
    - for every child, find the closest children, starting from the fail node, has the character of the child node
      - if true, then point to that node, add the failed node's output to the current node
      - if false, fail to the root
    - node in queue, already build fail node
* match
  - if fail, go to the fail node, and continue the match

https://github.com/gonet2/wordfilter go
https://github.com/observerss/textfilter python
