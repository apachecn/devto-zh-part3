# EnqueueZero Techshack 2019-07

> 原文：<https://dev.to/soasme/enqueuezero-techshack-2019-07-1bn6>

# enqueue zero Techshack 2019-07

原帖:[https://enqueuezero.com/techshack.weekly/2019-07.html](https://enqueuezero.com/techshack.weekly/2019-07.html)。

## Kubernetes 作为 API 标准

[cloudatomiclab.com](https://www.cloudatomiclab.com/rustyk8s/)

既然 Kubernetes 是可靠运行代码的优秀 API，那么在 Rust 中实现一个 Kubernetes API 怎么样？它只是在世界和一组可扩展的事物的期望状态之间进行重新协商，这些事物默认包含一个 pod 的概念。差不多就是这样，一个简单的想法。

与其让实现成为规范，不如制定一个规范，让不同的实现竞争最好的实现。目前，还没有公开回购。

## 在亚马逊写文档

[usejournal.com](https://blog.usejournal.com/writing-docs-at-amazon-e025808616bd)

撰写亚马逊文档的指南:

*   亚马逊文档一般是段落/散文。
*   检查语法和拼写。
*   理解你想用这个文档完成什么(就像你写的任何东西一样)。如果你不同意已经承诺的事情，你需要有清晰的数据和逻辑。
*   最小化表面积/省去无关的东西。
*   房间里有合适的人。
*   在门口检查你的自我。最后得到一个不同的解决方案是可以的，但它必须是正确的答案。
*   一定要“秀出你的作品。”展示所有的解决方案，明确说明为什么其中一个是最好的。
*   代表和支持客户。写下(严肃而有说服力的)为什么你的提议对客户有好处。
*   不要让你的同事(或你的老板)感到惊讶或震惊。
*   仅在文档准备好的情况下安排会议。否则，重新安排。
*   想得足够大，不仅是指问题和解决方案，还包括其余的逻辑。
*   阅读房间。当你不能说服自己的观点时，再回来找更有力的例子。
*   不要含糊，也不要过于戏剧化。

## 了解数据库分片

[digitalocean.com](https://www.digitalocean.com/community/tutorials/understanding-database-sharding)

分片是一种与水平分区相关的数据库架构模式，即把一个表的行分成多个不同的表，称为分区。每个分区都有相同的模式和列，但也有完全不同的行。同样，每个分区中保存的数据是唯一的，并且独立于其他分区中保存的数据。常见的分片策略包括基于键的分片、基于范围的分片和基于目录的分片。

对数据库进行分片的主要吸引力在于，它有助于促进水平扩展，也称为向外扩展。缺点是它的复杂性。

## 调试 MariaDB Galera 星团 SST 问题——有趣的经历

[percona.org](https://www.percona.com/blog/2019/02/12/debugging-mariadb-galera-cluster-sst-problems-a-tale-of-a-funny-experience/)

*   问题:MariaDB 集群决定重新启动自己并挂起，而一些节点在复制了一些数据后拒绝加入集群的请求。
*   原因:Systemd 正在终止 mysqld 进程，但没有停止服务。这导致无限 SST 循环，只有在通过 systemd 命令终止或停止服务时才会停止。
*   解决方法:在`/etc/systemd/system/mariadb.service.d/timeoutstartsec.conf`中设置`TimeoutStartSec=900`，重新加载守护进程。
*   想法:90 秒的超时对于 Galera 集群来说太短了。在 SST 期间，很可能几乎任何集群都将达到该超时。即使一个普通的 MySQL 服务器崩溃了，脏页面的比例很高，或者有很多操作需要回滚，90 秒似乎也不是一个可行的崩溃恢复时间。

## 使用 fzf 的快速 kubectl 自动补全

[github.com](https://github.com/bonnefoa/kubectl-fzf)

fzf 为 kubectl 提供了快速而强大的 fzf 自动补全功能。

## SQL:最宝贵的技能之一

[craigkerstines . com](http://www.craigkerstiens.com/2019/02/12/sql-most-valuable-skill/)

SQL 是一项尚未掌握的技能；大多数应用程序开发人员会跳过它。这是一个你可以在任何地方使用的工具。而且更重要的是，它是永久的，不像其他人那样频繁更改 API。

## 简化的云编程:关于无服务器计算的伯克利观点

[rise.cs.berkeley.edu](https://rise.cs.berkeley.edu/blog/a-berkeley-view-on-serverless-computing/)

无服务器云计算和传统云计算的三个基本区别是:

1.  计算和存储的分离；它们可以单独扩展，并独立定价。
2.  执行一段代码的抽象，而不是分配执行该代码的资源。
3.  为代码执行付费，而不是为分配给代码执行的资源付费。

相关阅读，[云端之上:伯克利对云计算的看法](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2009/EECS-2009-28.pdf)。

## 为什么 GraphQL 是 API 的未来

[hashnode.com](https://hashnode.com/post/why-graphql-is-the-future-of-apis-cjs1r2hhe000rn9s23v9bydoq)

REST 有很多端点，有取多取少的问题，而且每次我们需要包含或删除一些东西时，它都会转到另一个版本。

GraphQL 只需要一个端点，只根据需要提供足够的数据。它使您的 API 更加自文档化，并且您不需要编写大量关于它的文档。

## 原则性的 GraphQL

[principledgraphql.com](https://principledgraphql.com/)

*   您的公司应该有一个统一的图表，而不是由每个团队创建多个图表。
*   尽管只有一个图，但是该图的实现应该跨多个团队联合。
*   应该有一个真实的来源来注册和跟踪图表。
*   模式应该作为一个抽象层，为消费者提供灵活性，同时隐藏服务实现细节。
*   该模式应该基于实际需求逐步构建，并随着时间的推移平稳发展。
*   性能管理应该是一个持续的、数据驱动的过程，平稳地适应不断变化的查询负载和服务实现。
*   在整个开发过程中，开发人员应该具备丰富的图形意识。
*   基于每个客户端授予对图形的访问权限，并管理客户端可以访问的内容和方式。
*   捕获所有图形操作的结构化日志，并将它们作为理解图形使用的主要工具。
*   采用分层架构，将数据图功能分解到单独的层中，而不是嵌入到每个服务中。

## Python 磁盘备份缓存

[github.com](https://github.com/grantjenks/python-diskcache)

2018 年的云计算重视内存。由于进程争夺内存，磁盘上留下了数十亿字节的空白空间。在这些进程中，Memcached(有时是 Redis)被用作缓存。利用空磁盘空间进行缓存不是很好吗？你真的能允许用 60 毫秒将一个键存储在一个有 1000 个条目的缓存中吗？

DiskCache 有效地使千兆字节的存储空间可用于缓存。通过利用坚如磐石的数据库库和内存映射文件，缓存性能可以达到并超过行业标准解决方案。不需要 C 编译器或运行另一个进程。

```
import diskcache as dc
cache = dc.Cache('tmp')
cache[b'key'] = b'value'
print(cache[b'key']) 
```

Enter fullscreen mode Exit fullscreen mode

## 如果不是打字稿，那就不性感

[developer.okta.com](https://developer.okta.com/blog/2019/02/11/if-it-aint-typescript)

越来越多的开源项目正在或者将要用 TypeScript 编写，比如 Yarn、Vue.js、deno 等。一些好处是更高的速度，减少的缺陷，更快的上线，更容易的重构。
使用 TypeScript 的风险就像 CoffeeScript 的遭遇；有一天，我们可能都会重新开始写 JavaScript。

## CVE-2019-5736: runc 集装箱突围(所有版本)

[seclists.org](https://seclists.org/oss-sec/2019/q1/119)

最近在 runc 上报告了一个漏洞。它允许恶意容器(通过最少的用户交互)覆盖主机 runc 二进制文件，从而在主机上获得根级代码执行权限。

用户交互的级别是能够在以下任一环境中以 root 用户身份在容器内运行任何命令(如果命令不受攻击者控制也没关系):

*   使用攻击者控制的映像创建新容器。
*   将(docker exec)附加到攻击者以前具有写访问权限的现有容器中。

修复方法是在运行 run `nsenter/nsexec`之前检查我们是否不在克隆的库中。[github.com](https://github.com/opencontainers/runc/commit/0a8e4117e7f715d5fbeef398405813ce8e88558b)

## 为 Envoy v2 构建 Kubernetes 边缘(入口)控制平面

[立方。我](https://kubernetes.io/blog/2019/02/12/building-a-kubernetes-edge-control-plane-for-envoy-v2/)

*   Ambassador 本身作为 Kubernetes 服务部署在一个容器中，并使用添加到 Kubernetes 服务中的注释作为其核心配置模型。
*   Kubernetes 和 Envoy 是非常强大的框架，但它们也是移动速度极快的目标
*   Kubernetes / Envoy 生态系统中支持最好的库都是用 Go 编写的。
*   重新设计一个测试工具有时对软件的发展是必要的。
*   重新设计测试工具的真正成本通常是将旧的测试移植到新的工具实现中。

## 结构化 RFC 流程

[philado . com](http://philcalcado.com/2018/11/19/a_structured_rfc_process.html)

你如何培养一种更能接受和善待变化的文化？答案是结构化征求意见(RFC)流程。

*   作者按照一个模板写一份描述提案的文档，该模板旨在确保在邀请人们给出反馈之前回答一些基本问题。
*   然后，他们会要求其他工程师提供书面反馈，通常是通过向一个众所周知的邮件列表发送电子邮件。
*   审阅文档的人向作者提供他们的观点、以前经历的趣闻以及与提案相关的事实。
*   作者感谢给出的每一条反馈，并承诺在未来的某个时候重新审视他们的最终决定，分享他们学到的经验教训。

## Kubernetes 配置管理的状态:一个未解决的问题

[blog.argoproj.io](https://blog.argoproj.io/the-state-of-kubernetes-configuration-management-d8b06c1205)

配置管理是一个难以解决的问题。好的 Kubernetes 配置工具具有以下特性:

*   声明性的。该配置是明确的、确定的并且不依赖于系统。
*   可读。配置是以一种易于理解的方式编写的。
*   灵活。这个工具有助于促进，并且不会妨碍你完成你想要做的事情。
*   可维护。该工具应该促进重用和可组合性。

以下是一些可能的解决方案。

*   掌舵。它自称是 Kubernetes 的包管理器，并不自称是一个配置管理工具，尽管许多人都是这样使用它的。好的一面是已经有许多高质量的图表得到了很好的维护；坏的部分是模板和复杂的 cd 管道。
*   草泽。它已经被合并到 kubectl 中。
*   Jsonnet。它是一个超级强大的 JSON，结合了一种合理的方法来做模板，并不特定于 Kubernetes。好的方面是它功能强大，保证生成语法正确的 YAML。
*   Ksonnet，Kubernetes 的 jsonnet。好像太难了。
*   复制船。
*   头盔 3 + LUA 脚本。它还不成熟，是最不发达的国家之一。

## 我的警戒哲学

[docs.google.com](https://docs.google.com/document/d/199PqyG3UsyXlwieHaqbGiWVa8eMWi8zzAn0YfcApr8Q/edit)

*   网页应该是紧急的，重要的，可操作的，真实的。
*   它们应该代表您的服务正在发生或即将发生的问题。
*   宁可去除嘈杂的警报——过度监控比监控不足更难解决。
*   您应该总是能够将问题分为以下几类:可用性&基本功能；潜伏期；正确性(数据的完整性、新鲜度和持久性)；和特定功能的问题。
*   症状是一种更好的方法，可以用更少的努力更全面、更有力地捕捉更多的问题。
*   在基于症状的页面或仪表板上包含基于原因的信息，但避免直接针对原因发出警报。
*   你的发球级别越高，你在一条规则中发现的不同问题就越多。但不要走得太远，你不能充分区分发生了什么。
*   如果你想要一个安静的轮流值班，有一个系统来处理那些需要及时响应，但并不紧急的事情是非常必要的。

## 刮目相看

[docs.influxdata.com](https://docs.influxdata.com/kapacitor/v1.5/working/scraping-and-discovery/)

这就是 Kapacitor 搜集和发现的工作方式。

[![kapacitor](img/d4e6f7ed6675fb81856d86f65e7e944e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fakXTBYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs.influxdata.com/img/kapacitor/pull-metrics.png)

## 不测量单元测试代码覆盖率

[jamesshore.com](https://www.jamesshore.com/Blog/Dont-Measure-Unit-Test-Code-Coverage.html)

如果人们不知道如何正确地进行 TDD，代码覆盖度量就没有帮助。相反，你应该，

*   执行 RCA，然后改进您的设计和流程，以防止此类缺陷再次发生。
*   教授测试技巧，加速测试循环，更多地重构，使用进化设计，尝试结对或者围攻。
*   每当一个 bug 被修复，首先添加一个测试。每当一个类被更新时，首先对它进行改进测试。
*   让客户代表尽早参与流程。
*   混合使用真实世界的监控、故障快速代码和专门的测试平台。

## 日志 vs 结构化事件

[charity.wtf](https://charity.wtf/2019/02/05/logs-vs-structured-events/)

*   在执行代码时，从请求的角度发出丰富的记录。包括所有你能接触到的背景。
*   对每个服务的每个请求发出一个事件。在请求出错或退出服务之前写下来。
*   完全绕过本地磁盘，写入远程服务。
*   成本或资源限制所需的样本。练习动态抽样。
*   将此视为运营数据，而不是交易数据。挥霍无度，任人宰割。
*   将这些数据输入到柱状存储器或蜂巢或类似装置中
*   现在每天都用。不仅仅是作为最后的手段。每天都埋头于生产。探索。询问和回答关于您的系统、系统质量、系统行为、异常值、错误条件等丰富的问题。

## Kubernetes 能在 CERN 工作吗？

[youtube.com](https://www.youtube.com/watch?v=2PRGUOxL36M)|[speakerdeck.com](https://speakerdeck.com/rochaporto/kubernetes-at-cern-use-cases-integration-and-challenges)

使用了很多现代技术:OpenStack、Kubernetes (Federation)、Kubeflow、Jupyter 等。

## 当 AWS 自动缩放不

[segment.com](https://segment.com/blog/when-aws-autoscale-doesn-t/)

找到正确的自动伸缩策略的最好方法是在您的特定环境中并针对您的特定负载模式进行测试。有时候，这不是你想的那样。

## trims tray/nginx-快速参考

[github.com](https://github.com/trimstray/nginx-quick-reference)

这篇文章描述了如何提高 Nginx 的性能、安全性和其他重要的东西。