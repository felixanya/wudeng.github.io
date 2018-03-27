# solidity


随机数：
  oracles
  keccak256
  
smart contract
EVM: stack machine, max 1024
statically typed,

magic global variables:
  msg
  tx
  block

  now: unix timestamp
  days
  seconds
  hours

事件都是大写开头
函数和变量都是小写开头。
合约名也是大写开头。

contract
  mapping
  bytes32
  uint8
  uint

使用状态变量时，使用小于32字节的数据可能可以减少gas，因为编译器会pack这些变量。但是函数参数以及内存变量是没必要使用小于32字节数据的。不会pack，反而增加了操作。
mappimn
  struct
  address 160bit
  msg.sender

  storage: permanantly, outside of function
  memory: temporary


  public
  private

  internal:private, accessable by children contract
  external: only be called by outside the contract

/// natspec comment

modifier

keccak256

  wei
  gwei
  finney
  ether

loomx.io
loom network: slack for  dev


cnpm install ganche-cli web3
cnpm install solc

0.4.20

IDE:
运行环境：remix https://remix.ethereum.org
http://ethereum.github.io/browser-solidity/
通过remixd可以访问本地的文件夹。remixd是一个node模块。通过命令 npm install -g remixd全局安装。
remixd -S <absulote-path-to-the-shared-folder>
Environment:
- Javascript VM
- Injected provider: Mist or Metamask
- Web3 Provider: ethereum node, e.g.: geth, parity

swarm

OpenZeppelin library

event

https://api.cryptokitties.co/kitties/1


https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3
https://etherscan.io/address/0x06012c8cf97bead5deae237070f9587f8e7a266d#code
https://etherscan.io/address/0x06012c8cf97bead5deae237070f9587f8e7a266d#readContract

geneScience: todo 尝试反编译
https://etherscan.io/address/0xf97e0a5b616dffc913e72455fde9ea8bbe946a2b#code


https://cryptozombies.io/

swarm:source
bzzr://a6465fc1ce7ab1a92906ff7206b23d80a21bbd50b85b4bde6a91f8e6b2e3edde



assert : should never happen checks
revert : user input validation fails, user-generated errors
require

函数有四种作用域：external, public, internal, private，默认的是public。
状态变量没有external作用域，默认是internal.

external: 合约接口的一部分，可以被其他合约或者交易调用。不能被内部调用，也就是不能直接调用f()，但是可以通过this.f()调用（这是非常不合理的方式，CALL，非常昂贵）。
当传递大数组时，这种方式更加高效。因为参数不需要分配内存空间。
传递数组的时候，public比external要消耗更多的gas。这是因为在public函数中，Solidity会拷贝数组参数到内存中。而external函数可以直接读calldata. 内存分配是很昂贵的，而读取calldata很便宜。

那么public为什么要把参数拷贝到内存呢？这是因为public函数可以被内部函数调用，这跟external函数完全不一样的过程。内部调用时通过jump来执行的，并且数组参数是通过指向内存的指针来传递的。
所以编译器生成内部函数代码的时候，函数会去内存中读取参数。

external函数不能被内部调用，所以参数可以直接从calldata中读取，省略了拷贝这一步。

所以我们写代码的时候，如果函数只会被外部调用，应该用external, 如果需要被内部调用才考虑使用public。


public: public函数是合约接口的一部分。可以被内部调用也可以被消息调用。对应的状态变量，会自动生成一个getter()函数。


internal: internal函数和状态变量只能被内部访问：只能被当前合约或者继承当前合约的子合约调用，不需要this前缀。

private: private函数和状态变量只能被当前合约访问。自合约也不能访问。



view: 不会修改状态，没有副作用。
pure: 不会读写状态。包括block,tx,msg(msg.sig, msg.data除外)


Fallback函数。payable, 如果没有这个函数，合约无法通过普通交易接受Ether。<2300gas

coinbase transaction, aka miner block reward

## 状态变量存储结构

## 问题：
msg.data 如何设置的。比如部署一个合约，构造函数里面用到了msg.data，那么这个值从何而来。remix 貌似没有提供部署的参数选项。

解决：实际上是有的。最上面的value可以指定多少wei。默认是0. 所有的transcation都需要提供三个值：account,gas limit, value.
其中account对应msg.sender, value对应msg.value.

etherscan
hevm
evmdis
truffle
testrpc
myetherwallet
solidity

http://solidity.readthedocs.io/en/latest/index.html

https://cryptozombies.io/
