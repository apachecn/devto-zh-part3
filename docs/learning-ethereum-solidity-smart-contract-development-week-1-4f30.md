# 学习以太坊/Solidity 智能合约开发–第 1 周

> 原文：<https://dev.to/hasanaliqureshi/learning-ethereum-solidity-smart-contract-development-week-1-4f30>

[![](img/03f098e4930e6af88680e4c0546703e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DobnPddw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hasanali.me/wp-content/uploads/2019/05/vr-cOVER-1024x534.png)

我已经开始通过摆弄块浏览器和 RPC APIs 来使用区块链。设置 RPC APIs 并将它们连接到前端 web 是我所知道要做的事情。但我真正想要的是了解区块链的核心，并想知道如何成为一名区块链开发者。当时我也很困惑，因为区块链的生态系统正在迅速发展，新的开发工具也在不断涌现，所以我很难决定从哪里开始。

我在 Udemy 上找到了这个关于“以太坊和坚固性:完全开发者指南”的课程，它奠定了我的区块链开发之旅的第一步。

现在作为一名区块链开发人员，我更频繁地被问到这个问题，所以我认为我应该详细记录我的 solidity 开发课程，任何想开始区块链开发的人都可以像我一样从这里开始学习。

第一周是以太坊区块链的介绍，以及一些术语和它们的解释，但之后，我们将深入编码智能合约。

**先决条件:**

1.  你应该对区块链有一个基本的了解，只是地址、钱包和区块链的基本知识。如果你不知道区块链的基本知识，不要担心检查安德斯播放列表

<figcaption>Blockchain 101 – A Visual Demo</figcaption>

<figcaption>Blockchain 101 – Part 2 – Public / Private Keys and Signing</figcaption>

1.  如果你知道 React.js，这将是一个加分点，因为未来我们将在 it 领域开发智能合约的前端。如果你不知道 react，不要担心，看看这个免费的 React.js 课程

**以太坊的历史:**

2013 年 12 月，比特币白皮书发布后，Vatalik Buterin 设想将区块链技术不仅用作支付转移模式，还用作可以运行程序和代码的虚拟机，因此引入了智能合同的概念。Vatalik 并不是第一个引入智能合同概念的人，Nick Szabo 之前也提出过，但这是第一次成功实现这一概念。

**区块链:**

区块链是一个保存所有交易记录的数据库，任何人都可以保存和维护这个数据库，因此它是去中心化的(不受权威机构控制)

以太坊:

以太坊是一种特殊类型的区块链，它不仅保存交易记录，还为其上的主机编程代码提供存储，这些代码由以太坊虚拟机执行。

**meta mask:**

meta mask 是 chrome 浏览器的扩展，允许用户在浏览器中与以太坊应用程序进行交互。安装它并探索它。([https://chrome . Google . com/web store/detail/meta mask/nkbihfbeogaeaoehlefnkodbefgpgknn？hl=en](https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en)

**智能合约:**

智能合约是区块链以太坊的一个特殊账户，里面有 3 样东西。

1.  余额:该账户中以太币的数量
2.  存储:此合同的数据存储
3.  代码:智能合同代码

**坚实度:**

Solidity 是以太坊推出的编写智能合约的编程语言。它是一种非常类似于 javascript 的强类型语言。

**智能合同生命周期:**

当智能合约在 solidity 中编程时，它由 Solidity 编译器编译，这给了我们两件事。

1.  字节码:这是部署在以太网上的代码
2.  应用程序二进制接口(ABI): ABI 是一个长 JSON 对象，包含智能合约方法和信息，我们可以通过它们与智能合约进行交互。