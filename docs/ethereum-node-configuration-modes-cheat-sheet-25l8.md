# 以太坊节点配置模式备忘单

> 原文：<https://dev.to/5chdn/ethereum-node-configuration-modes-cheat-sheet-25l8>

这份文档是一份解释最常见的`geth`和`parity`配置的快速备忘单——通常是运行以太坊节点所需要知道的一切。

# 光节点

轻型客户端只存储头链，并根据需要从网络请求其他所有内容。他们可以对照块头中的状态根来验证数据的有效性。

## `❯ geth --syncmode light`

使用[轻型以太坊子协议(LES)](https://wiki.parity.io/Light-Ethereum-Subprotocol-(LES)) 将轻型客户端的块头与以太坊网络同步，从开发人员插入的可信检查点开始:

```
INFO [01-05|22:42:27.103] Added trusted checkpoint                 chain=mainnet block=6848511 hash=5e9f76…d9d6ac
WARN [01-05|22:42:27.123] Light client mode is an experimental feature 
INFO [01-05|22:43:13.912] Block synchronisation started 
INFO [01-05|22:43:40.953] Updated latest header based on CHT       number=6848511 hash=5e9f76…d9d6ac age=4w9h38m
INFO [01-05|22:44:01.569] Imported new block headers               count=192 elapsed=2.386s number=6848703 hash=2e78ec…851f48 age=4w8h54m
INFO [01-05|22:44:08.962] Imported new block headers               count=192 elapsed=341.059ms number=6848895 hash=4b8c89…d6b233 age=4w8h5m
INFO [01-05|22:44:12.912] Imported new block headers               count=192 elapsed=392.806ms number=6849087 hash=1b80b2…8515b4 age=4w7h15m
INFO [01-05|22:44:42.234] Imported new block headers               count=192 elapsed=379.507ms number=6849279 hash=ef9c20…605416 age=4w6h29m 
```

请注意，它是如何插入可信检查点并在之后导入剩余的头的。

## `❯ parity --light`

使用[奇偶校验轻协议(PIP)](https://wiki.parity.io/The-Parity-Light-Protocol-(PIP)) 从开发人员插入的可信检查点开始，将轻客户端的块头与以太网同步:

```
2019-01-05 22:46:08  Running in experimental Light Client mode.
2019-01-05 22:46:08  Inserting hardcoded block #6692865 in chain
2019-01-05 22:46:08  Debug API is not available in light client mode.
2019-01-05 22:46:23  Syncing #6692865 0xa988…8132     0.0 hdr/s      0+    0 Qed   2/50 peers   664 bytes cache 0 bytes queue  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 22:47:59  Syncing #6693604 0x10bc…90fd   147.8 hdr/s    535+    0 Qed   4/50 peers   480 KiB cache 404 KiB queue  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 22:48:09  Syncing #6698114 0x1b74…9c97   451.0 hdr/s    122+    0 Qed   3/50 peers   3 MiB cache 95 KiB queue  RPC:  0 conn,    0 req/s,    0 µs 
```

请注意，它是如何插入硬编码的块并随后同步剩余的头的。

## `❯ parity --light --no-hardcoded-sync`

从 genesis:
开始，使用奇偶校验轻协议(PIP)将轻客户端的块头与以太网同步

```
2019-01-05 22:48:17  Running in experimental Light Client mode.
2019-01-05 22:48:17  Debug API is not available in light client mode.
2019-01-05 22:48:17  Public node URL: enode://4959d0273c8b7410ce46a47907c52c64c32cd4fca4ce3bfbb62c1f9d2d52a64d37d94b5a9e00e61fc218af482b6e04ea2c5d21480a0a079e15077020e4870590@192.168.1.110:30303
2019-01-05 22:48:27  Syncing       #0 0xd4e5…8fa3     0.0 hdr/s      0+    0 Qed   1/50 peers   0 bytes cache 0 bytes queue  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 22:48:37  Syncing    #3356 0x4f82…dbf7   335.6 hdr/s    223+    0 Qed   2/50 peers   2 MiB cache 172 KiB queue  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 22:48:42  Syncing    #5977 0x8339…9045   524.2 hdr/s    160+    0 Qed   2/50 peers   4 MiB cache 126 KiB queue  RPC:  0 conn,    0 req/s,    0 µs 
```

请注意，它是如何从`#0`开始同步标题的。

# 满节点

完整节点在磁盘上有完整的区块链数据，可以根据请求向网络提供任何数据。

## `❯ geth`

**默认模式。**同步一个完整的节点，通过下载整个状态数据库，首先请求报头，然后填充块主体和收据，进行 [*快速*同步](https://ethereum.stackexchange.com/questions/1161/what-is-geths-fast-sync-and-why-is-it-faster)。

```
INFO [01-05|22:56:41.759] Loaded most recent local header          number=0 hash=d4e567…cb8fa3 td=17179869184 age=49y8mo3w
INFO [01-05|22:56:41.759] Loaded most recent local full block      number=0 hash=d4e567…cb8fa3 td=17179869184 age=49y8mo3w
INFO [01-05|22:56:41.759] Loaded most recent local fast block      number=0 hash=d4e567…cb8fa3 td=17179869184 age=49y8mo3w 
```

一旦快速同步到达以太坊网络的最佳块，它就切换到完全同步模式(见下文)。

## `❯ geth --syncmode full`

同步从源节点开始的完整节点，验证所有块并执行所有事务。这种模式比*快速*同步模式稍慢，但安全性更高。

## `❯ parity`

**默认模式。**通过下载`30_000`最佳区块的快照和最新状态数据库，使用 [*warp* 同步](https://ethereum.stackexchange.com/questions/9991/what-is-paritys-warp-sync-and-why-is-it-faster-than-geth-fast)模式同步一个完整以太坊节点。

```
2019-01-05 23:00:33  Syncing snapshot 0/2577        #0    4/25 peers   8 KiB chain 3 MiB db 0 bytes queue 11 KiB sync  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 23:00:43  Syncing snapshot 2/2577        #0    7/25 peers   8 KiB chain 3 MiB db 0 bytes queue 11 KiB sync  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 23:00:48  Syncing snapshot 3/2577        #0    9/25 peers   8 KiB chain 3 MiB db 0 bytes queue 11 KiB sync  RPC:  0 conn,    0 req/s,    0 µs 
```

快照恢复后，客户端切换到完全同步，旧数据块从网络后台同步，日志中以黄色数据块编号表示。

## `❯ parity --no-warp`

同步从源节点开始的完整节点，验证所有块并执行所有事务。这种模式比 *warp* sync 模式稍慢，但安全性更高。

# 归档节点

除了由所有完整节点保存的链数据之外，还可以构建历史状态的档案。

## `❯ geth --syncmode full --gcmode archive`

从源节点开始同步存档节点，彻底验证所有数据块，执行所有事务，并将所有中间状态写入磁盘(“存档”)。

```
INFO [01-05|23:04:54.981] Imported new chain segment               blocks=2 txs=0 mgas=0.000 elapsed=741.138ms mgasps=0.000 number=2 hash=b495a1…4698c9 age=3y5mo3w  cache=0.00B
INFO [01-05|23:04:56.475] Imported new chain segment               blocks=4 txs=0 mgas=0.000 elapsed=27.599ms  mgasps=0.000 number=6 hash=1f1aed…6b326e age=3y5mo3w  cache=0.00B
INFO [01-05|23:05:03.616] Imported new chain segment               blocks=86 txs=0 mgas=0.000 elapsed=206.526ms mgasps=0.000 number=92 hash=c86dcb…f8b64c age=3y5mo3w  cache=0.00B 
```

在 Geth 中，这被称为`gcmode`，它指的是[垃圾收集](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science))的概念。设置为`archive`基本就是关机。

## `❯ parity --no-warp --pruning archive`

从源节点开始同步存档节点，彻底验证所有数据块，执行所有事务，并将所有中间状态写入磁盘(“存档”)。

```
2019-01-05 23:03:21  State DB configuration: archive
2019-01-05 23:03:21  Warning: Warp Sync is disabled because of non-default pruning mode.
2019-01-05 23:03:31  Syncing    #1689 0x3c91…2cea   169.80 blk/s    0.0 tx/s    0 Mgas/s   4019+    0 Qed     #5715    9/25 peers   2 MiB chain 51 KiB db 7 MiB queue 7 MiB sync  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 23:03:41  Syncing    #6422 0x750c…dc88   473.30 blk/s    0.0 tx/s    0 Mgas/s   6270+    0 Qed    #12700   12/25 peers   5 MiB chain 120 KiB db 10 MiB queue 12 MiB sync  RPC:  0 conn,    0 req/s,    0 µs
2019-01-05 23:03:51  Syncing   #11293 0x576c…7163   487.20 blk/s    0.0 tx/s    0 Mgas/s  13332+    0 Qed    #24638   19/25 peers   7 MiB chain 166 KiB db 20 MiB queue 8 MiB sync  RPC:  0 conn,    0 req/s,    0 µs 
```

在奇偶校验中，这被称为`pruning`，它指的是[状态 trie 修剪](https://ethereum.stackexchange.com/questions/174/what-is-state-trie-pruning-and-how-does-it-work)的概念。设置为`archive`基本就是关机。

* * *

感谢阅读到目前为止。基本就是这样。