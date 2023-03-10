# 使用 strace 进行调试

> 原文：<https://dev.to/dbdanilov/using-strace-for-debugging-mmg>

调试是软件工程的重要部分，每个开发人员的工具箱中都有一些调试工具。通常，我使用日志和调试器，这对我的日常工作来说已经足够了。
但是最近，我不得不使用一个叫做 baresip 的开源 SIP 客户端。
Baresip 是一款非常稳定的开源 sip 客户端，拥有支持插件的出色设计。

过了一段时间，我发现有时候 baresip 会因为系统错误“设备或资源忙”而无法开始播放
拨出电话的音频。
我注意到，当客户接听来电时，这种情况并没有发生。
我的第一印象是，baresip 两次尝试打开音频播放来电音频。我试着在代码中找到这个地方，但是因为我不熟悉代码，所以没有找到。然后我就想到了`strace`。
[![Alt Text](img/48c319fb2423e0753fa5ee60810d4bb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qW4dW7nk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/883u6z8m7a5xlre8kdc5.png) 
`strace`是一个用户空间工具，打印给定进程的所有系统调用。
您可以在 strace 内部启动一个流程，也可以将其附加到一个正在运行的流程中。
我和 strace 一起跑了 baresip:`strace -o strace.log baresip -e 7100`，其中:

*   -o filename 将跟踪输出写入文件 filename，而不是 stderr。
*   strace.log 是输出日志文件
*   baresip 是正在调查的过程
*   -e 是命令行参数，告诉 baresip 拨打号码 7100。

然后我在日志文件中查找“设备或资源正忙”。
[![Alt Text](img/cf56671c5c0a56fc6a92c852bc186192.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--XhUcU9Dz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/2gyeglkgqh2x8mddzyf5.png)

正如我们在第 1308 行看到的那样。顺便说一下，vim 为 strace 日志提供了一个内置的语法亮点。

于是我找了`open("/dev/snd/pcmC0D0p", O_RDWR|O_NONBLOCK|O_LARGEFILE|O_CLOEXEC)`。
[![Alt Text](img/9ed481f64dde1d071a29fcbe673cfba1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ibTWAjec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/9stjbj379pryjatnw091.png) 
如您所见打开退货文件描述符“14”。那么我们来看看 baresip 是否关闭了文件描述符:
[![Alt Text](img/c94dcc6c4fec3cfe619dd43e309123f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u6UyFvTh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/z4ypuhugqxcfv1r691hq.png)

接下来打开调用:
[![Alt Text](img/6c700566001f41a3259cf59c366b92b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ns3_uOKw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/8gjfp5liqzgk07aqhj2l.png)

以及 1484 行中对应的关闭调用:
[![Alt Text](img/dea06e4da91bde221a5e4bd0df1fafc8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q90UROtV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/810ghi1tuikkj6ag725t.png)

而如果你还跟着我这里就是下一个开放的称呼:
[![Alt Text](img/3406195557d81bc00bd70e882279a19d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gjxP7cpB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/mof67cnomtyfzc6noo5m.png)

所以你可以看到下一个 open 函数调用发生在第 1308 行，而 close 函数调用发生在第 1484 行。
表示 baresip 在关闭音频设备之前先打开它。
看来我们找到了有问题的函数调用！

据我所知，baresip 会打开音频设备来播放
传入的音频，但为什么之前会打开呢？因此，让我们来看看第二次公开通话前后会发生什么。在第 1120 行中，我们看到 baresip 打开并读取 ringback.wav 文件，该文件是
一种回铃音，即主叫方在拨号后听到的可听见的铃声:
[![Alt Text](img/c43bc30ab09e67e77362903321477a0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g_BMiUh_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/1i7esnca22upum3pfj1d.png)

之后，它显然会打开音频设备来播放文件。
并且当音频设备正在播放铃声时，另一个线程接收来自被呼叫者的响应
,并且打开相同的音频设备以便播放传入的音频。
然后问题就发生了。

这就是我如何以非常有限的应用知识设法找到根本原因的。
我没有修复这个问题，因为它需要对 baresip 的设计进行重大修改，但我
通过在配置文件中禁用回铃找到了一个快速的解决方法。