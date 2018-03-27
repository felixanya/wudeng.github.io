# ethereum

## accounts
- externally owned accounts, controlled by private keys and no code associated with them
- contract accounts, controlled by their contract code and have code associated with them



## state

Account State
- nonce
  * number of transaction sent from the account's address
  * number of contracts created by the account
- balance: Wei 1e+18 Wei = 1 Ether
- storageRoot: hash of root node of a Merkle Patricia tree
- codeHash: hash of EVM code

World State
- State trie
- transaction trie
- Receipts trie

## gas and fees

unit gwei, 1 gwei = 10^9 wei

## tranactions
- message calls
- contract createions

nonce: count of number of transaction sent by the sender
gasPrice: number of Wei that the sender is willing to pay per unit of gas
gasLimit: the maximum amount of gas that the sender is williing to pay
to
value: the amount of Wei to be transfered from sender to the Receipt
v,r,s
init: contract creation
data: message calls

## blocks

- block header
  * parentHash
  * ommersHash
  * beneficiary
  * transactionsRoot
  * receiptsRoot
  * stateRoot
  * logsBloom
  * difficulty
  * number
  * gasLimit
  * gasUsed
  * timestamp
  * extraData
  * mixHash
  * nonce
- set of transaction
- a set of other block headers for the current block's ommers

## transaction execution
mining
proof of work



* https://medium.com/@preethikasireddy/how-does-ethereum-work-anyway-22d1df506369
* https://github.com/ethereum/yellowpaper
