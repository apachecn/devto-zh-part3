# Repasando Artículos:比特币:一种点对点电子现金系统

> 原文：<https://dev.to/gustavo94/bitcoin-a-peer-to-peer-electronic-cash-system-26b6>

[中本聪](https://bitcoin.org/bitcoin.pdf)

Bitcoin 是 blockchain 最受认可的实现方式，匿名作者 satoshi nakamoto 撰写的白皮书是首批列举和描述 blockchain 实现的所有要素之一，本文的主要目的是提出一种电子货币，消除将信任的第三方作为所有交易的中间人作为避免

## 交易

bitcoin 数据块是一种数据结构，旨在存储网络用户之间的货币交易，每一个用户想要将一种货币转移到另一种货币时，都会创建一个“交易”，其中包含一个由以前的货币交易散列和接收转移的用户的公钥组成的电子签名；有了这一签字，新的货币所有者可以核实是否向他转让了货币，但不能核实以前的所有者没有向另一个所有者进行同样的转让，这就是所谓的双重支出问题。此外，事务由多个输入和多个输出组成，允许合并和分离货币组，而无需单独处理。

## 工作证明

作业测试是查找一个值，以便在将该值绑定到组时，下一个事务组(或块)将生成指定数量的前导零，并应用散列函数(如 SHA-256)。找到此值所需的计算工作可确保攻击者在整个网络中至少拥有 51%的计算能力的情况下无法创建包含损坏事务的块，而这反过来又可作为一致算法中的一个参数，在从不同节点接收不同字符串时，节点总是选择最长的块字符串

此外，为了控制计算组件容量的增长，网络不断增加增加增加难度所需的零数，从而对更多的计算能力提出要求。

建立每一个新集团的这种计算成本也与建立新货币和向网络中添加新货币有关，鼓励那些设法通过注入新货币和收取交易价值的"费用"来解决这一挑战的人。预计到某个时候，费用将足以支付向网络提供这种计算能力的费用。

## 网络

每次执行新事务时，都会按 6 个步骤传输该事务并将其添加到块链中:

1.  事务处理将发送到所有节点
2.  每个节点会将新交易群组为一个区块
3.  每个节点都会尝试寻找符合工作测试的值
4.  当一个节点通过作业测试时，会将新块发送到整个网络
5.  只要所有事务都有效且块通过作业测试，节点即可接受块
6.  节点会加入它们接受的图块，并移至下一个图块

## 默克尔树

随着时间的推移，如果自网络启动以来保留了所有历史记录，则网络中的事务数可能会占用过多的磁盘空间，因此，当有足够的旧货币事务时，可以使用 merkle tree 作为数据结构来排列块中不同事务的散列值

## 隐私

但是，在网络内，所有交易都是公开的，只能看到正在转移的价值数额，而接收价值的人的公用钥匙仍然隐藏着这些交易背后的人的身份。