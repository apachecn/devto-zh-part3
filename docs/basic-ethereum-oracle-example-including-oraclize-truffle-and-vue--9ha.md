# 基本以太坊甲骨文例子，包括 Oraclize，Truffle 和 Vue

> 原文：<https://dev.to/marlowl/basic-ethereum-oracle-example-including-oraclize-truffle-and-vue--9ha>

## 什么是神谕

简单地说，甲骨文是一种智能合约，它能够与外界进行交互，在以太坊的世界中，这就是所谓的链外世界。

## 举例

我没有找到很多关于如何将前端与以太坊神谕相结合的例子。因此，作为一个小项目，我建立了一个小的启动包。希望它可以作为其他想研究 oracles 的开发人员的起点或范例！

它从比特币基地 API 获取当前的 ETH/USD 价格，并将其纳入智能合约

该示例包括以下技术

*   view-CLI 3 . 2 . 1
*   TypeScript(类样式组件)
*   智能合同示例
*   奥拉基泽
*   松露

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [马洛](https://github.com/marlowl) / [以太坊-奥拉基泽-启动器](https://github.com/marlowl/ethereum-oraclize-starter)

### 基本以太坊甲骨文示例，使用 Oraclize，Truffle，Vue 和 Web3

<article class="markdown-body entry-content container-lg" itemprop="text">

# 以太广场或露天广场

前端由版本 3.2.1 的 vue-cli 生成

基本以太坊 Oracle 示例，包括 Oraclize、Truffle、比特币基地 API、Web3

# 演示

通过以太坊 Oracle 从比特币基地 API 获取当前的 ETH/USD 价格

[![](img/75332685d3c1007192c981bad61dd995.png)](https://raw.githubusercontent.com/marlowl/ethereum-oraclize-starter/master/demo.gif)

# 项目设置

### 安装依赖项

```
npm install 
```

### 安装 truffle(适用于下面的 Truffle 版本，以后新版本可能会出现编译错误)

```
npm install -g truffle@4.1.15 
```

### 运行块菌发展(产卵发展区块链)

```
truffle develop 
```

### 打开一个单独的终端并运行以太坊桥(参见 package.json 了解它的功能)

```
npm run bridge 
```

等到它完全加载后，您会看到以下消息

```
Please add this line to your contract constructor
OAR = OraclizeAddrResolverI(address) 
```

### 在 Truffle 开发控制台中编译合同

```
truffle(develop)> compile 
```

### 将合同也迁移到 Truffle develop 控制台中

```
truffle(develop)> migrate --development --reset 
```

### 从以太坊桥终端获取合同地址

输出应该是这样的

```
 "contract_myid": "0x9fa87c542cab950bd129f3d2d13cba6199741bd1984351c04e83c54620dfee1b"
    "contract_address":
```

…</article>

[View on GitHub](https://github.com/marlowl/ethereum-oraclize-starter)

尽情享受吧！