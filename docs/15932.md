# 从以太坊提取数据的解决方案

> 原文：<https://dev.to/madewithtea/solutions-to-extract-data-from-ethereum-3p09>

最初发表于[madewithtea.com](https://www.madewithtea.com/solutions-to-extract-data-from-ethereum.html)

以太坊始于 2013 年。以太坊成为开发和运行智能合约的事实标准。数据要么是以太、ERC-20(想想所有的[ico](https://icodrops.com))中的价值转移，要么是不可替代的加密收藏品代币(NFT，例如 [CryptoKitties](https://www.cryptokitties.co/) 中的数字小猫，在[分散土地](https://decentraland.org/)中的虚拟土地财产)或定制智能合约呼叫。[Dapps 的状态](https://www.stateofthedapps.com/rankings)正在以太坊上统计 1763 个 Dapps。另一个很好的资源是 DappRadar。

如果你想从已经部署的智能合同中提取数据——或者在部署之后——你可以**购买类似于 Google Analytics 或 Mixpanel 的监控和分析工具**。然而，**还没有确定的玩家**。监控和分析只是两个使用案例，数据的更多使用案例在列表之后。既然**去中心化和开源齐头并进**，我就从它开始:

### 开源

*   **[EthQL](https://github.com/ConsenSys/ethql)** : ConsenSys 项目，GraphQL 端点到公共以太坊总账
*   **[QuickBlocks](https://github.com/Great-Hill-Corporation/quickBlocks)** :检索以太坊数据的工具集合
*   **[EthSlurp](http://ethslurp.com/)** :将特定地址或智能合约的文本提取到 CSV 或文本文件
*   **[e events](https://github.com/brainbot-com/ethevents)**:将事务、事件索引成 ElasticSearch， [talk](https://www.youtube.com/watch?v=zoNlF4MfHVg)
*   **[昆德拉](https://github.com/Impetus/Kundera)** : JPA 从节点提取数据并摄取到各种接收器中
*   **[Presto-以太坊](https://github.com/xiaoyao1991/presto-ethereum)** :以太坊客户端的 Presto 连接器
*   **[图](https://www.thegraph.com/)** :区块链数据的分散可扩展查询(ICO 阶段)
*   **[智能合约手表](https://github.com/Neufund/smart-contract-watch)** :智能合约监控工具
*   **[Eventeum](https://github.com/ConsenSys/eventeum)** :以太坊与微服务之间的事件桥梁
*   **[Seth](https://github.com/dapphub/dapptools/tree/master/src/seth)** :命令行的元掩码
*   **[粗糙](https://github.com/XLNT/gnarly)** :粗糙的 ETL 工具
*   **[以太坊 ETL](https://github.com/medvedev1088/ethereum-etl)**:ETL 脚本集
*   **[CoinMetrics 脚本](https://github.com/coinmetrics-io/haskell-tools)** :基于 Haskell 的脚本
*   **[以太 SQL](https://github.com/analyseether/ether_sql)** : Python 脚本
*   **[电锯](https://github.com/SpankChain/chainsaw)**:spank chain 简单事件追踪
*   **[dapp board-etl](https://github.com/DAppBoard/dappboard-etl?files=1)**:dapp 的分析 ETL
*   **[VulcanizeDB](https://github.com/vulcanize/VulcanizeDB)** :写 dapp 索引和缓存的工具集。
*   **[Blockchain2Graph](https://github.com/straumat/blockchain2graph)** :提取区块链数据到 Neo4j

### 查询公司数据

*   **[TokenAnalyst](https://docs.tokenanalyst.io)** :免费跨链链上数据提供者
*   :ConsenSys 支持，致力于数据的语义提升， [talk](https://www.youtube.com/watch?v=KzfEDNuNFi8&t=21s)
*   :交易所和政府间的 KYC 和反洗钱服务
*   **[椭圆形](https://www.elliptic.co/)** : KYC 和反洗钱服务
*   **[扎皮尔](https://zapier.com/apps/dagger-for-ethereum/integrations)** :以太坊融入扎皮尔

### 分析仪表板

*   :以太坊的分析仪表板
*   **[以太扫描](https://etherscan.io/)** :以太坊事实上的标准块浏览器
*   :对 ERC 的分析——20 个代币，[说话](https://www.youtube.com/watch?v=IhugJ__ickM)
*   **[token analyst](https://www.tokenanalyst.io/)**:ERC 分析-20 token 和 ico， [talk](https://www.youtube.com/watch?v=JopYHeEetC8)
*   :由 CryptoKitties 的创造者 Axiom Zen 破解的智能合约 Mixpanel
*   **[Supermax](https://supermax.cool/)** :又一个智能合约仪表盘
*   **[Blockspur](https://blockspur.com/)** :又一个智能合约仪表盘
*   **[琐碎的](https://trivial.co/)** :又一个智能合约仪表盘

### 用例

以下是获取数据的五个用例:1) **为 Dao 或 DAC 等公司的治理提供透明度**和问责制。2) **智能合同的自动纳税申报**3)**智能合同监控**例如在发生异常时获得通知 4) **智能合同报告**例如网站报告、销售报告 5) **审计支持**例如 ico(令牌持有者分布)。

### 我漏了一个吗？

本文应作为智能合同分析和监控领域解决方案的资源列表。请让我知道，如果我错过了一个，或重要的参考资料。如果你能分享你用这些工具做的有趣的事情的故事，也会很有趣。如果有你希望存在的工具的话！