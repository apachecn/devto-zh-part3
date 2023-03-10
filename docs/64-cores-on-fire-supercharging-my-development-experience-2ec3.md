# 燃烧的 64 个内核:增强我的开发体验

> 原文：<https://dev.to/azure/64-cores-on-fire-supercharging-my-development-experience-2ec3>

我最近开始为 Rust 标准库做贡献，开始充实对 WebAssembly 系统接口(WASI)的网络支持。问题是，在我的 2015 MacBook Pro 上构建 rustc 及其 LLVM 等组件需要两个半小时。

[![My poor machine](img/1e7020bfe97e15a098d9c71f83d796b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XhON3irD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/77s8wl6im1ubgco6eh97.png)

如果我想提高工作效率，我需要一个不同的解决方案！

## 磨颜色！

当你的机器运行速度不够快时，最简单的方法就是尝试多扔几个内核，看看是否有帮助。幸运的是，构建 rustc 的很大一部分是构建 llvm，当它访问更多内核时，速度会大大加快。

所以我和我的同事 [Duncan](//twitter.com/duncan) 在 Azure 上搭建了一个 F64s_v2 虚拟机，总共有 64 个内核。Azure 上的 Fsv2 系列虚拟机针对计算进行了优化，使其成为这项工作的合适虚拟机。

有了这台机器，构建时间显著减少到总共 17 分钟左右！

[![All the cores!](img/8218ad7bef55fe68ae31e7283b827db0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XTS9wFhZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rxuoxwfe215mempe4pwm.png)

想知道更少的内核会发生什么，我们在 32 个内核的 F32s_v2 和 16 个内核的 F16s_v2 上运行了相同的设置。这些构建分别在 21 分钟和 30 分钟内运行——不算太差！

## 预计成本

Azure 虚拟机在美国通常便宜一点，所以我们把它们托管在那里。我位于欧洲，但是根据我的经验，延迟并不是一个真正的问题，因为我使用的开发工具(ssh 和 VS 代码远程扩展)可以很好地处理延迟。

[撰写本文时，64 核机器的成本](https://azure.microsoft.com/en-us/pricing/details/virtual-machines/ubuntu-advantage-standard?WT.mc_id=devto-blog-rylevick)为每小时 2.73 美元，而 32 核机器为每小时 1.38 美元，16 核机器为每小时 0.71 美元。

鉴于较小的机器更便宜，我选择在 32 核机器上进行初始构建，然后进行增量构建，将磁盘转移到 B8MS 虚拟机。B8MS 是一台 8 核机器，针对“突发”工作负载(即每隔一段时间发生一次的高 CPU 使用率，如编译)进行了优化。每小时只需 0.36 美元。当然，当你不用机器的时候，你仍然应该记得关掉它。

你还必须支付保管费。使用 64GB 内存的标准固态硬盘，每月只需 5 美元。

如果我们将所有这些放在一起，我估计大约 4 或 5 小时的典型开发会话的成本约为 2.00 美元。如果这意味着我几乎把所有的时间都花在编码上，而不是等待编译，那还不算太坏。

## 开发经验

显然，设置一台机器需要一点时间，但是通过使用 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest&WT.mc_id=devto-blog-rylevick) 的简单 shell 脚本，我现在可以轻松地复制两台机器的设置，并在它们之间交换磁盘。

至于写实际代码，我真的很喜欢 [VS Code](https://code.visualstudio.com?WT.mc_id=devto-blog-rylevick) 的[远程开发扩展](https://code.visualstudio.com/docs/remote/remote-overview?WT.mc_id=devto-blog-rylevick)。大多数时候我没有注意到，我没有在我的机器上工作。我过去作为 vim 用户有过类似的经历，但这是下一个级别，甚至让文件传输到远程机器，超级简单。

希望这是像 Rust 这样的大型项目中对远程开发的有趣观察。请在下面发表评论或在 [Twitter](//twitter.com/ryan_levick) 上留言，让我知道你的想法