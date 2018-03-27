区块链

SHA256(head)


添加新区块很困难。平均10分钟全网才能生成一个区块。一小时也就6个。

block：
  - 区块头，Hash由区块头唯一确定。
    - 生成时间
    - 区块体的Hash
    - 上一个区块的Hash
  - 区块体

全节点：geth, go
轻节点：parity, rust
第三方：Infura

代币：ERC20 -> ERC197
              ERC23 (ERC223)
      协议代币
      APP币

难度系数difficulty, 目标值 = 常数 / 难度系数。只有小于目标值的Hash才有效。

难度系数动态调节。每两周调整一次。2016个区块。难度系数越调越高，目标值越来越小，导致采矿越来越难。

六次确认：看哪个分支先达到6个新区块。按照10分钟一个区块计算，一小时就可以确认。

区块的生成需要矿工进行无意义的计算，消耗能源。

使用场景：
  - 不存在所有成员都信任的管理当局
  - 写入数据不要求实时使用
  - 挖矿的收益能够弥补自身的成本

IPFS network : global p2p merkle-dag filesystem  InterPlanetary File System
  go-ipfs
    go get github.com/jbenet/go-ipfs/cmd/ipfs


    gateway.ipfs.io/ipfs/hash...
    localhost:8080/ipfs/...

data hashes storeed in blockchain


问题：
没有中心，新节点如何加入？
新区快的Hash到底是什么内容的Hash？
etherscan.io

防止伪造：私钥签名，公钥验证

交易顺序共识：
  new transaction -> pending transaction
  基于工作量的证明

以太坊白皮书：

智能合约：根据事先任意制定的规则来自动转移数字资产的系统。

DAOs: 去中心化自治组织

状态转换

first-to-file:先申请应用，交易顺序。实现去中心化共识。拜占庭容错：多方共识系统，参与方已知，容忍N/4的恶意参与者。

在匿名的情况下容易遭受女巫攻击，单方面确保拥有多数份额。

节点通过工作量证明机制获得参与到系统的权利。

比特币账本：状态转换系统。
  - APPLY(S,TX) -> S'
    - 交易的每个输入：
      - 如果引用的UTXO不存在于现在的状态（S）中，返回错误提示 （防止花费不存在的比特币）
      - 如果签名与UTXO所有者的签名不一致，返回错误 （防止花费别人的比特币）
    - 如果所有的UTXO输入面值总额小于所有的UTXO输出面值总额，返回错误 （价值守恒）
    - 返回新的状态S'，新状态中移除了所有输入的UTXO，增加了所有输出的UTXO
  - 现存比特币所有权状态：所有已经被挖出的、没有花费的比特币的集合，（unspent transaction outputs UTXO）。
    - UTXO：面值和所有者（20个字节的公钥）
  - 状态转换函数
    - 输入：当前状态 + 交易
    - 输出：新的状态


区块：
  - 时间戳
  - 随机数Nonce
  - 上一个区块的hash

  工作量证明：256bit，SHA256，小于不断调整的目标数值
    - 使区块的创建变得困难，从而阻止女巫攻击者恶意生成区块链
    - 创建有效区块的唯一方法就是简单地不断试错，不断地增加随机数的数值，查看新的哈希数值是否小于目标数值
    - 每隔2016个区块重新设定目标数值，保证每10分钟产生一个区块
    - 每一个成功生成区块的矿工有权在区块中包含一笔凭空发给他们自己25BTC的交易
    - 如果交易的输入大于输出，差额部分就作为交易费用付给矿工，对矿工的奖励是比特币发行的唯一机制。创世区块中并没有比特币。
    - 发生区块链分叉是，区块链长的分支被认为是诚实的区块链。
    - 区块存储在多层次的数据结构中。
    - 区块的hash实际上只是区块头的hash。包括时间戳 + 随机数 +  上个区块的hash + 存储了所有区块交易的默克尔树根hash，长度大约为200字节的一段数据。

默克尔树
  - 叶节点存数据，其他节点存hash
  - 允许区块的数据可以零散的传送：节点可以从一个源下载区块头，从另外的源下载与其相关的树的其他部分。
  - 对比特币的长期持续性至关重要。内存空间的增产
  - SPV 简化支付确认，轻节点：下载区块头，使用区块头确认工作量证明，然后下载与其交易相关的默克尔树分支。

区块链的应用：
  - 域名币 namecoin，去中心化的名称注册数据库。先申请原则first-to-file。利用区块链实现名称注册系统的最早的，最成功的系统。
  - 彩色币 colored coins
  - 元币 metacoins


以太坊：
  解决比特币扩展性不足的问题。
  图灵完备的脚本语言：Ethereum Virtual Machine Code, EVM
  合约：以太网的核心。自动代理人。


合约：
  tx
    - value
    - sender
    - data[0-1...]
  block
    - basefee
  contract
    - storage


ethereumjs-testrpc@4.1.3 renamed to ganache-cli
http://truffleframework.com/ganache/

## 运行一个合约

git clone https://github.com/llSourcell/Your_First_Decentralized_Application.git
cd Your_First_Decentralized_Application
cnpm install

执行环境：
node node_modules/ethereumjs-testrpc/build/cli.node.js
会创建10个账户。
监听8545端口。

node:
```
> Web3 = require('web3')
> web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
> code = fs.readFileSync('Voting.sol').toString()
> solc = require('solc')
> compiledCode = solc.compile(code)
> abiDefinition = JSON.parse(compiledCode.contracts[':Voting'].interface)
> VotingContract = web3.eth.contract(abiDefinition)
> byteCode = compiledCode.contracts[':Voting'].bytecode
> deployedContract = VotingContract.new(['Rama','Nick','Jose'],{data: byteCode, from: web3.eth.accounts[0], gas: 4700000})
> deployedContract.address
> contractInstance = VotingContract.at(deployedContract.address)
```

web3.eth.accounts
comiledCode.contracts[':Voting'].bytecode comiled from source, deployed to the blockchain
compileCode.contracts[':Voting'].interface interface or template of the contract, called abi definition
which tell the contract user what method are available in the contract.


VotingContract.new deploys the contract to the blockchain
  data: compiled bytecode
  from: who deployed the contract
  gas

when interact with your contract, you need the deployed address and the abi definition.

contractInstance.totalVotesFor.call('Rama')
{ [String: '4'] s: 1, e: 0, c: [ 4 ] } https://en.wikipedia.org/wiki/Scientific_notation
contractInstance.voteForCandidate('Rama', {from: web3.eth.accounts[0]})
contractInstance.totalVotesFor.call('Rama').toLocaleString()

Ropsten testnet
Truffle Framwork

solidity -> bytecode -> EVM
web3.js connect to your blockchain node.


truffleframework

cnpm install -g truffle
cnpm install -g webpack
truffle unbox webpack 电脑上执行这段的时候总是失败，报503，too many open connections错误。原因是npm加了代理，
可以给npm配置淘宝镜像，去掉代理即可。



https://www.zastrin.com/ 一个学习Dapp开发的网站，有一个投票Dapp开发的免费的课程，很棒。不过其他两个课程是收费的。不过不要紧，下面这个网站以博客的形式
教你入门，质量杠杠的。

https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2 这个网站也分享了投票app开发，而且是免费的。

Ethereum has 2 public blockchain:
- Testnet (also called Ropsten), a test blockchain
- Mainnet (also called Homestead), for real

安装geth的
```
geth --testnet --syncmode "fast" --rpc --rpcapi db,eth,net,web3,personal --cache=1024  --rpcport 8545 --rpcaddr 127.0.0.1 --rpccorsdomain "*" --bootnodes "enode://20c9ad97c081d63397d7b685a412227a40e23c8bdc6688c6f37e97cfbc22d2b4d1db1510d8f61e6a8866ad7f0e17c02b14182d37ea7c3c8b9c2683aeb6b733a1@52.169.14.227:30303,enode://6ce05930c72abc632c58e2e4324f7c7ea478cec0ed4fa2528982cf34483094e9cbc9216e7aa349691242576d552a2a56aaeae426c5303ded677ce455ba1acd9d@13.84.180.240:30303"
```

启动geth，不加参数正常，但是加上参数就不对了。ping了下启动节点的ip也是无法ping通的。

生成猫的模型：
https://bytesforbites.github.io/cryptokitty-designer/

基因 12 * (4 * 5 bit)
https://medium.com/@kaigani/the-cryptokitties-genome-project-68582016f687
5bit : cattribute body,pattern type, eye color, eye type, primary color, pattern color, secondary color, fancy type, mouth
4 : 1 dominant trait, 3 recessive trait

https://github.com/openblockchains/awesome-cryptokitties/blob/master/mixGenes.py

翻墙访问：
https://cryptokittydex.com/
https://cryptokittydex.com/resources 猫的数据

ERC-721 protocol, non fungible tokens,
https://github.com/ethereum/EIPs/issues/721
http://ethfans.org/ajian1984/articles/747


https://ethfiddle.com/09YbyJRfiI


https://etherscan.io/address/0x06012c8cf97bead5deae237070f9587f8e7a266d#code

http://ethfans.org/posts/how-to-code-your-own-cryptokitties-style-game-on-ethereum



白皮书
https://www.dropbox.com/s/a5h3zso545wuqkm/CryptoKitties_WhitePapurr_V2.pdf?dl=0


共识机制：
- POW
- POS
- DPOS
- Ripple
- Pool验证池

GHOST protocol
Gready Heaviest Observed Subtree


国内：
迅雷：链克（玩客币）链克口袋，链克开放平台
云帆 LLT
暴风：云播控
行云科技：运动挖矿
云搬家：星际魔盒
极路由：挖矿
斐讯：挖矿
网易：招财猫
玩客猴


交易所：
龙交所 - dragonex  [ 推荐、支持RMB进场、溢价最高适合套现 ]
币安 - binance  [ 币币交易，货币最齐全，不支持RMB进场，进场需龙交所买币提进币安]
比特儿 - beter  [老牌靠谱交易所，支持RMB进出场]
久币网 - CEX [老牌靠谱交易所，支持RMB进出场]
中比特 - ZB [老牌靠谱交易所，支持RMB进出场]
可盈可乐 - CoinCola  [老牌靠谱交易所，支持RMB进出场]
云币 - bigone [老牌靠谱交易所，币币交易]
火币 - huobipro [老牌靠谱交易所，币币交易]

coincola
localbitcoins



https://github.com/BytesForBites/cryptokitty-designer

* [区块链入门教程](http://www.ruanyifeng.com/blog/2017/12/blockchain-tutorial.html)
* https://github.com/llSourcell/Your_First_Decentralized_Application
* blockchain_guide.pdf
