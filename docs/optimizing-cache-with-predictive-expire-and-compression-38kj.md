# 通过预测过期和压缩优化缓存

> 原文：<https://dev.to/zohaibility/optimizing-cache-with-predictive-expire-and-compression-38kj>

在非常高的流量场景中，一个可观察到的问题是**缓存踩踏**或**缓存未命中风暴**。事实证明，如果你知道自己在做什么，解决起来并不难。另外，随着 Redis 流量的增加和人们在 Redis 中存储更大的有效负载，读取或写入大的有效负载现在会更慢。在接下来的演讲中，我不仅会详细讨论这些问题，还会讨论如何解决它们。所以事不宜迟:

[https://www.youtube.com/embed/QkUz2_kRV9g](https://www.youtube.com/embed/QkUz2_kRV9g)

### 幻灯片

[//www.slideshare.net/slideshow/embed_code/key/r4750Et9lRXplt](//www.slideshare.net/slideshow/embed_code/key/r4750Et9lRXplt)

### 模拟演示

[https://repl.it/@maxpert/DebounceSimulator?lite=true](https://repl.it/@maxpert/DebounceSimulator?lite=true)

### 资源/参考文献

*   [最优概率缓存防踩踏](http://www.vldb.org/pvldb/vol8/p886-vattani.pdf)论文
*   原文[用压缩加速 Redis】](https://doordash.engineering/2019/01/02/speeding-up-redis-with-compression/)