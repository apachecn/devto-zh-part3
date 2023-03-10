# 推土机

> 原文：<https://dev.to/commonshost/bulldohzer--dns--doh-performance-testing-50fm>

几个月前 [Commons Host](https://commons.host) 仅用了 10 天就建立并推出了 HTTPS 域名服务[。该服务已被证明是可靠和高性能的，用户享受安全和私人的 DNS 服务。](https://dev.to/commonshost/how-we-built-a-doh-cdn-with-20-global-edge-servers-in-10-days-1man)

用户面临的一个挑战是找到最好的 DoH 或 DNS 服务。公共 DoH & DNS 性能报告的价值值得怀疑。这是因为大型数据中心或互联网交换机中的测试服务器的性能与您自己网络中的设备不同。

所以运行你自己的性能测试。

## 简介:斗牛士🚜

[bulldhzer](https://www.npmjs.com/package/bulldohzer)是一个易于使用的 DNS 和 DoH 性能测试。你可以自己运行 Bulldohzer 来找到最适合你的解决方案*。*

 **   https://github.com/commonshost/bulldohzer
*   https://www.npmjs.com/package/bulldohzer NPM

Bulldohzer 不需要任何安装。试运行只需要几秒钟。报告旨在提供大量细节，但易于一目了然。还支持原始 JSON 数据的输出。

```
$ npx bulldohzer 
```

*注意:`npx`命令由 Node.js 提供，它是唯一的依赖项。您需要 Node.js v11.4.0 或更高版本。*

[![Screenshot](img/0b7853f6d49ad84b1bfe3f5e9fb7516a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f3xOhrRr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/43laskohmhbntnezg43d.png)

## 如果你不能衡量它，你就不能改进它。

由于几十年的广泛使用，传统的 DNS 得到了极大的优化。不幸的是，它容易被篡改和监控。DoH 是一种新的安全的 DNS 协议。DoH 通过长期 HTTP/2 连接传输 DNS。因为 DoH 太新了，一些实现还没有优化，也没有广泛部署。

请试用 Bulldohzer，并与 DoH 提供商分享您的结果。

*封面照片由[khao productions](https://www.flickr.com/photos/khaosproductions/4870139498/)T3】**