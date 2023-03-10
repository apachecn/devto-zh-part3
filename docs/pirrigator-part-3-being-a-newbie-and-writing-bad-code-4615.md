# Pirrigator，第 3 部分。作为一个新手，编写糟糕的代码。

> 原文：<https://dev.to/neilgall/pirrigator-part-3-being-a-newbie-and-writing-bad-code-4615>

这周我有点拖延。还有其他事情要做——我收到了达林顿晶体管，并让水阀在 Raspberry Pi 控制下工作，带有到 GPIO 的 sysfs 接口。我还收到了 MCP3008 ADC，并获得了一些 Python 代码来读取我的湿度传感器的模拟输出。

但是我发现学习一门新语言最困难的事情是感觉你做错了。我不介意生产率下降的感觉，不介意不得不查找所有内容，也不介意有一个非常短且频繁的编辑-编译周期来捕捉所有愚蠢的语法错误。三十多年来，我一直在用多种语言编写代码，我喜欢认为我对做事情的正确和错误方式有很好的感觉，但进入 Rust 的新世界感觉我已经失去了这种感觉。我已经学到了足够多的东西，可以写那些*有效*的东西，但是在 Rust 中这样做是正确的吗？我发现自己在说，我会等到我读了更多的书，或者找到其他人做类似事情的代码，然后我会写我自己的代码。但是我必须战胜这种冲动。写任何东西，哪怕很烂，都是进步。软件的奇妙之处在于你可以随时重写它。

所以我制作了我的第一个 Rust 代码！它很可怕，可能在很多方面都不符合习惯，但它是可以编译和运行的 Rust 代码。我为 Raspberry Pi 构建了一个 ARMv6 原生二进制文件，它读取温度/压力/湿度传感器(我想我从现在开始就称它为天气传感器)，并将当前值打印到控制台。我仍将等待在 [crates.io](https://crates.io) 上发布之前了解更多一点 Rust，因为我不认为它已经准备好“生产”，但它美丽的功能和直接来自 C 的丑陋在 [GitHub](https://github.com/neilgall/rust-bme280) 上可供所有人使用。

我还在开发一个读取湿度传感器的模块，它有一个 SPI 接口。我真的很想把所有东西都放在 I C 上，但是 SPI 只是多了几条线，多了一点代码。RustpiIO 看起来是一个很有希望的建造基地。

所以今天的信息是当你学习的时候继续努力，写下那些糟糕的代码。你写过的最差的。彻底改造标准库或忽略使其更简洁或健壮的语言特性的可怕的废话。扔掉别人会嘲笑的垃圾。总有一天，你会成为其他人嘲笑的对象，因为糟糕的代码会教会你比你能找到的所有完美的例子更多的东西。