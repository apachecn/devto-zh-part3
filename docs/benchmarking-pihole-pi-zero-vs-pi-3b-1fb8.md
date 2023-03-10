# 基准测试 Pihole : Pi Zero vs Pi 3b+

> 原文：<https://dev.to/tonymet/benchmarking-pihole-pi-zero-vs-pi-3b-1fb8>

[来自π孔的话语](https://discourse.pi-hole.net/t/benchmarking-pihole-pi-zero-vs-pi-3b/19397)

这是一个比较 Pi Zero(带 USB 以太网)和 Pi 3b+上运行的 pi-hole 的基准测试。

**TL；dr** 阻塞域的性能差异可以忽略不计，但转发+可缓存域的平均性能差异可以测量。尽管 Pi 3b+对于转发查询的平均响应时间要快 11 毫秒，但是对于阻塞查询和转发查询，pi zero 的 P95 都要快。

我会推荐使用圆周率为零。

## 假设

在实验之前，我假设 pi 零点在所有情况下都会慢 30-50%，并且 stddev 会更大(更差&更不稳定的延迟)。

## 硬件&设置

路由器:TP Link Archer C9
PI 3b+:通过 100mb 以太网接入路由器，32gb sandisk microsd 卡。Raspbian 桌面
Pi Zero-W:通过 usb 以太网适配器连接，100mb 以太网接入路由器，8gb class-4 sd 卡。拉斯边石

## 法

我使用了[DNS trace](https://github.com/redsift/dnstrace)——一个 go dns 基准。
每个测试执行 1000 个查询，并发 10 次(`go run dnstrace.go -c 10 -s 192.168.0.201 -n 100 www.googletagservices.com`)

## 结果

完整的直方图和结果[可以在这里找到](https://gist.github.com/tonymet/bfd85f8c09bb9cfc53e6592c59844658)

#### 封锁域名【www.doubleclick.net】T2

**pi 3b+**

```
 min:        16.252928ms
     mean:       38.372412ms
     [+/-sd]:    23.097544ms
     max:        385.875967ms 
```

**π零**T2】

```
DNS timings, 1000 datapoints
     min:        19.922944ms
     mean:       40.043544ms
     [+/-sd]:    7.762346ms
     max:        71.303167ms 
```

#### 转发域名【www.amazon.com】T2

**pi 3b+**

```
DNS timings, 997 datapoints
     min:        13.1072ms
     mean:       19.527493ms
     [+/-sd]:    12.101107ms
     max:        142.606335ms 
```

**π零**T2】

```
DNS timings, 999 datapoints
     min:        16.252928ms
     mean:       30.097752ms
     [+/-sd]:    5.005948ms
     max:        88.080383ms 
```

## 总结和发现

我相信零足以取代π3 b+。而且让我印象深刻和震惊的是，pi 零点上的 stddev 比 3b+还低。因此，尽管平均转发响应时间更差，pi zero 的 P95 在阻塞查询和转发查询方面都更好。

⭐:我会让这两个设备在线一段时间，如果你想做进一步的测试，请告诉我。⭐