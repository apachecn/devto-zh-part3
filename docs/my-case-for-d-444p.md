# 我对 D 的看法

> 原文：<https://dev.to/jessekphillips/my-case-for-d-444p>

几乎从一开始，d 就是我编程生活的一部分。我从 ascii 计算机游戏《ZZT》开始，HTML 在我的开发中发挥了作用，但最终选择 perl 作为我的第一种编程语言，而一个朋友选择了 BASIC。我通读了“21 天自学 C++的大部分内容。”所有这些都是在高中的时候。我最终选择了 Java 课程，我所做的事情之一就是寻找一种比 C++更好的语言，我遇到了 D，我说的是[老文档](https://web.archive.org/web/20021205114505fw_/http://digitalmars.com:80/d/index.html)。但是我还没准备好学习这么年轻的语言。最后，几年后，我又去寻找那种更好的语言(我一辈子都想不起名字了)，从那以后它一直伴随着我。

[D](https://dlang.org/) 已经成为我的脚本语言。当其他人想使用动态语言时，我选择了 d。它也是我用来探索其他编程领域的语言:不同的编程技术、不同的编程层(编译时与运行时)、手动内存管理、C、dll、COM、Webservers、GUI。

看到了吧，D 的图形化方面即使在今天也还是没有达到一个合理的状态。但是有绑定，可以做到。除此之外，D 创造了丰富的瑞士军刀功能。我必须测试我们的软件，这意味着做一些非常创新的事情，比如编写一个 [MSI 自定义动作 DLL](https://he-the-great.livejournal.com/58420.html) 。最重要的是，我需要将它注入到 MSI 中，而 Orca 不会自动完成设置，这意味着需要更多 Win32 API 调用 MSI 操作。虽然我知道我可以用 C#编写 DLL，但我不确定是否可以在 C、C++、d 之外操作 MSI。

[D 中编程](http://ddili.org/ders/d.en/index.html)