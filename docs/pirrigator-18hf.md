# 灌注器

> 原文：<https://dev.to/neilgall/pirrigator-18hf>

夏天我在我的温室里种西红柿。我有一个西红柿灌溉计时器，但是设置合适的水量有点像猜谜游戏，而且没有考虑天气因素。如果我们不在家，就没有人监控和调节供水。但我喜欢摆弄技术，尤其是软件和硬件的组合，所以让它更智能是我今年上半年的项目。我将使用湿度和温度传感器，一个电磁驱动水阀和一个树莓皮来驱动这一切。然而，我从不喜欢以简单的方式做事；建造一些东西必须涉及我不熟悉的技术，所以我决定用 Rust 编写控制软件。

我的第一个挑战是给整个系统供电。我的温室里没有电力供应，所以需要用电池。幸运的是，我的车库里有一块来自一辆旧车的 45Ah 铅酸电池，什么也没用，我将使用的 Raspberry Pi model B 需要高达 1W 的功率才能运行，因此理论上最长运行时间超过 400 小时。我真的希望它能运行整个夏天，所以我买了一个 10W 的太阳能电池板，它带有一个控制器，你可以用电线连接电池板、电池和负载，它会处理其余的事情。即使在典型的多云的苏格兰夏天，它自身也能提供足够的能量持续运转。电池只能为黑暗中的几个小时提供电能。

这里仍然是冬天，我决定本周解决软件先决条件。如果我想在 Rust 中编写代码，我需要一个针对 BCM2835 处理器的交叉编译器，它有一个带硬件浮点的 ARMv6 指令集。在 GCC 工具链的语言中，这被称为`arm-unknown-linux-gnueabihf`。我曾经以编写嵌入式代码为生，所以交叉编译工具链对我来说并不陌生，但是让我吃惊的是在过去的十年中它变得多么容易。

安装了 [rustup](https://rustup.rs/) 之后，添加目标就像
一样简单

```
rustup target add arm-unknown-linux-gnueabihf 
```

Enter fullscreen mode Exit fullscreen mode

你还需要一个兼容的 GCC 工具链来链接。我的开发笔记本电脑运行的是 Arch Linux，我需要的工具链可以在 GCC 8 . 2 . 1 版本的 AUR 库中找到，所以这是一个
的问题

```
yaourt -S arm-linux-gnueabihf-gcc 
```

Enter fullscreen mode Exit fullscreen mode

在对未知的 GPG 键进行几个步骤的中止后，用
添加这些键

```
gpg --recv-keys <key-id> 
```

Enter fullscreen mode Exit fullscreen mode

然后安装工具链。将 Rust 目标与 GCC 工具链关联起来还有最后一步。创建或编辑`~/.cargo/config`并添加

```
[target.arm-unknown-linux-gnueabihf]
linker = "arm-linux-gnueabihf-gcc-8.2.1" 
```

Enter fullscreen mode Exit fullscreen mode

为了测试它，我用`cargo`创建了一个新的 hello world 项目，并用
编译它

```
cargo build --target arm-unknown-linux-gnueabihf 
```

Enter fullscreen mode Exit fullscreen mode

然后，在将生成的二进制文件复制到 Raspberry Pi 后，我尝试运行它。上面印着“你好，世界！”在终端上！

然而，我仍在学习 Rust，所以我想试试 Rust 编程语言书第二章的猜谜游戏教程。当我将它复制到 Raspberry Pi 并运行它时，出现了一个关于不支持的 GLIBC 版本的动态链接错误。哦不！我在 Arch 上构建，在 Raspbian 上运行，Raspbian 是从 Debian 派生出来的。我会碰到各种各样的库版本问题，不是吗？所以我决定在 Raspberry Pi 上安装 Arch 来保持一致性。我非常喜欢 Arch Linux 的一点是 [wiki](https://wiki.archlinux.org/) ，这是一个惊人的各种信息和说明的资源。在那里快速搜索发现，当然有一个 [Arch Linux on ARM](https://archlinuxarm.org/) 项目，它提供了包下载和[安装](https://archlinuxarm.org/platforms/armv6/raspberry-pi)指令。

在用新的安装重启 Raspberry Pi 和一些最后的设置步骤后，我再次复制了猜谜游戏的二进制文件并进行尝试。有用！

后续步骤:

*   我可以手动在 Raspberry Pi 上启动 wifi(使用官方 USB wifi 适配器)，但我还没有设法让它在启动时启动。我希望增加一个摄像头，并随着时间的推移产生一些漂亮的数据图表，所以 wifi 将是必要的。
*   研究如何从 Rust 访问 Raspberry Pi GPIO。
*   让湿度传感器工作
*   让外部温度/湿度/大气压力传感器工作
*   建立一个继电器电路来激活电磁水阀并使其工作
*   开始思考程序设计