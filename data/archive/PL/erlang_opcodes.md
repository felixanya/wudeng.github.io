BEAM Instruction Set
====



allocate t t
Allocates Arg0 slots on the stack. Arg1 is Live.

allocate_heap t I t
Allocates Arg0 slots on the stack. See allocate t t. Ensures that heap contains at least Arg1 words after HTOP. Arg2 is Live.

deallocate I
Restores CP to the value referenced by EP. Adds Arg0 + 1 to EP to remove the saved CP and Arg0 slots from the stack.


参考文档
----
* ops.tab
* beam_opcodes.erl
* http://erlangonxen.org/more/beam
