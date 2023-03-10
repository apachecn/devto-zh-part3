# 即将到来的操作系统内核模块化时代

> 原文：<https://dev.to/yonkeltron/the-coming-era-of-os-kernel-modularity-5emc>

人们曾经就单片内核和微内核哪个是更好的架构进行过辩论。这可能很有趣，但有时会变得很难看。当火焰战争激烈进行时，研究和开发在学术界和工业界同时进行。现在回过头来看，这显然不仅仅是一个整体架构和微内核架构的问题。相反，这是一个关于代码模块化的更大的讨论。

过去几年向我们展示了隐藏在操作系统(OS)开发领域中经常被忽略的活动中的一些关键趋势。其中最主要的观点是未来不需要看起来像过去。从谷歌的 Fuchsia 项目到 Linux 的 T2 BPF 项目，内核模块化的时代已经到来。

[![](img/d997d4fba998bef74e2838b5b5d10d9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SDvvlqpI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://source.unsplash.com/z508Zk08HNU)

# 目前正在开发的模块化内核

如果你留心听，你会不可避免地听到观看谷歌新的开源操作系统的不那么秘密的开发的人们兴奋的抱怨。紫红色的内核叫做[锆石，用 C++](https://fuchsia.googlesource.com/zircon) 写成。锆石因各种原因而引人注目，尤其是它避开了传统的 POSIX 标准，支持者称之为“更现代”的约定。特别值得注意的是，出于安全原因，[有意反对点点文件系统约定](https://fuchsia.googlesource.com/fuchsia/+/master/docs/the-book/dotdot.md)。任何对谷歌 Fuchsia 团队认为的其他现代惯例感兴趣的人都应该参考 [Fuchsia docs](https://fuchsia.googlesource.com/fuchsia/+/master/docs) ，最受欢迎的目的地是[书](https://fuchsia.googlesource.com/fuchsia/+/master/docs/the-book/)。最近来自中国的这组纸币可能也会引起人们的兴趣。

尽管 Google Fuchsia 抛弃了 POSIX，但并不是所有的模块化内核都效仿了它。例如， [Redox](https://www.redox-os.org/) 项目选择继续 POSIX 的传统，同时明确采用微内核方法。因为氧化还原的目标是制造一个可靠、安全、通用的操作系统，所以团队选择用 [Rust 编程语言](https://www.rust-lang.org/)来编写一切。这似乎是一个很好的主意，因为 Rust 有非常好的安全保证和非常符合人体工程学的错误处理。

在 Rust 方面，Redox 不是唯一的游戏，因为新的 [Tock](https://www.tockos.org/) OS 因针对嵌入式和低功耗系统而受到更多关注。该网站吹嘘 Tock 能够在从工业传感器到安全钥匙到手表的所有东西上运行。如果你对物联网感兴趣，并且重视安全性和可靠性，请关注 Tock。

此外，甚至还有一个名为[蝎狮](https://github.com/manticoreos/manticore)的实验研究操作系统，也是用 Rust 编写的，旨在探索[超内核](http://penberg.org/parakernel-hotos19.pdf)的世界和概念。

没有提到 [Genode](https://genode.org/) 项目，任何关于模块化内核的讨论都是不完整的。Genode 与其说是一个真正的内核，不如说是一个微内核构建工具包。尽管它是用 C++编写的，Genode 声称它提供了一种安全第一的操作系统开发方法，着眼于物联网和更传统的工作负载。

[![](img/d3a5d9136c9c7d0cd8e4f990a41e0341.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u-zj-zdO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://source.unsplash.com/xFjti9rYILo)

# 巨石反击

尽管 Linux 的根基是一个整体内核，但它总是能够通过在运行时利用动态链接的内核模块来回避一整类问题。然而，虽然这被证明是一个成功的策略，但是随着内核支持使用[Berkeley Packet Filter(BPF)](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter)机制编写的程序的出现，模块化的讨论已经发生了变化。BPF 最初是为控制网络 IO 而开发的，后来被扩展(eBPF ),允许将过滤器附加到各种操作系统功能上。Linux 中 ePBF 支持的结果是，它允许将代码深度绑定到内核中，而无需修改内核本身。

[Brendan Gregg 在他的博客上有一个非常棒的 eBPF 学习资源汇编](http://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html),并且 [IO Visor 项目也保持了](https://www.iovisor.org/technology/ebpf)一个非常可靠的概述。此外，还有一本以[表演为导向的 BPF 书](https://learning.oreilly.com/library/view/linux-observability-with/9781492050193/)将于 2019 年 10 月出版。

尽管它们肯定比经典操作系统要小，但新的世界[单核](http://unikernel.org/)大量借鉴了研究界对纳米内核的开发，并带来了[库 OS](https://en.wikipedia.org/wiki/Operating_system#Library) 的概念。其中最引人注目的是古老的 [MirageOS](https://mirage.io/) 项目，一个完全用 OCaml 编写的库操作系统。对于对“迷你独石”(minilith？)Unikernels 的方法寻求用 OCaml 之外的语言进行开发， [Unikernels 网站维护了一个项目列表](http://unikernel.org/projects/)，您可以从中挑选从 Go 到 BEAM 语言的任何项目。

另一个更适合传统开发的令人兴奋的选择是由我的朋友 Idit Levine 发起的 [UniK 项目，它可以获取几乎任何现有的程序并从中生成一个 Unikernel。常见语言和平台的例子有](http://unikernel.org/) [Node.js](https://github.com/solo-io/unik/blob/master/docs/getting_started_node.md) 、 [Python3](https://github.com/solo-io/unik/blob/master/docs/getting_started_python3.md) ，甚至 [Java](https://github.com/solo-io/unik/blob/master/docs/getting_started_java.md) 。关于 UniK 的一个更酷的事情是代码有多干净，你可以通读(Go)源代码并了解很多关于它如何提供一个可插入的后端支持风格，例如[rum run](https://github.com/solo-io/unik/blob/master/docs/compilers/rump.md)甚至[bracket](https://firecracker-microvm.github.io/)。

[![](img/b3c584bab1cbdacea9c8b3ed66cff2a9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k8CDVFnZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://source.unsplash.com/hsPFuudRg5I)

# 一个语言 VM 作为内核怎么样？

已经多次尝试用解释或半编译语言编写 OS 内核。最引人注目的两个项目实际上是现已停止的 JNode 项目和 T2 JX 项目。然而，过去的一项额外努力也非常重要。

微软的 Singularity OS 研究项目已经停止了近十年的时间，但是每当我想起这个项目，它就一直吸引着我。它试图只用 C#和一个最小的 VM 来制作一个完整的操作系统，据说它大量利用了一个实时(JIT)编译器来提供远远超过传统提前(AOT)编译内核所能提供的运行时配置文件和优化。此外，这种由[管理的](https://en.wikipedia.org/wiki/Managed_code)语言所谓的内存安全可以提供一些极好的安全保证，比如运行时内存检查和执行监督。

最近几个月，我怀着极大的兴趣观看了 WebAssembly (WASM)的发展和 [WebAssembly 系统接口(WASI)](https://wasi.dev/) 的[公告](https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/)。虽然像 [wasmer](https://wasmer.io/) 这样的工具使得运行编译到 WASM 的应用程序变得很容易，但我刚刚接触到了 NanoVM 人员运行 WASM 单核的努力。相当酷。

[![](img/9645e38f0c491c58c0014058dcd08c81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9WGzQtoc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://source.unsplash.com/eMc0lpn1P60)

# 伟大，那么有什么意义呢？

很明显，在许多领域，关于代码和系统模块化的讨论越来越多。随着人们探索像[微服务架构](https://microservices.io/)这样的技术(它不是所有问题的解决方案)，社区继续发展各种观点。因此，操作系统开发对这种讨论做出贡献，并反过来受到影响，这似乎是很自然的。让我们看看会发生什么(双关语)。