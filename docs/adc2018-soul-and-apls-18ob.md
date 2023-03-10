# ADC2018、SOUL 和 APL

> 原文：<https://dev.to/atsushieno/adc2018-soul-and-apls-18ob>

## ADC2018

当我决定离开 Xamarin 开发工作，在初夏投身音频开发世界时，我心中有一件事要做:参加[音频开发者大会(ADC) 2018](https://juce.com/adc/) 。

两天的会议(如果算上训练日的话是三天)结束了，我在那里很开心。有一个关于一种叫做灵魂的新语言的公告，声音语言。你可以在 Youtube 上找到这段视频。

[https://youtu.be/UHK1QQM0bnE?t=910](https://youtu.be/UHK1QQM0bnE?t=910)

这些天来，我一直在查阅各种音乐和音频相关的软件库和工具，为我的日语实验性[音频技术/库/工具降临节日历](https://adventar.org/calendars/3353)写了许多博客条目，并阅读了一些关于这类东西的文本。我想我明白这个灵魂的东西*可能*有多棒(作为一个持怀疑态度的开发者，我会一直说它仍然只是一个公告)，并认为我应该描述一下为什么。它部分是从我准备的日文翻译过来的，甚至在 ADC 开始之前。

不得不说，我从来不熟悉音频开发。我加入了 ADC2018，而我不知道它实际上主要是关于 JUCE 的。我一直告诉朋友们“我将加入 ADC，可能会在那里找到下一份工作”，但我不知道这是什么样的。我甚至对加入它并不认真(“我会加入，但如果有更有趣的事情发生，我会很快放弃”)。有些事情对出席的音频开发者来说是显而易见的，但我却不知道。我从来都不是一个专注的 C++程序员。但是 ADC 仍然很有趣，我学到了很多东西。

## 那么，APL 是什么样的呢？

SOUL 是一个新的 APL(音频编程语言)平台。APL 是语言，不仅仅是一种语言(这里它并不意味着“一种编程语言”)。就像 VPLs-alike(可视化编程语言)一样。

他们设计来处理音频和作曲(或者更准确地说，“创作”)？)音乐或者实现音效。理想情况下，它们是为非程序员(或者“不是很高级”的程序员)设计的。

它们实际上是基于语言的工具，而不是语言规范。APL 的例子有:Csound、ChucK、Pure Data、Alda、Faust、Tidal。有趣的是，他们总是定义自己的语言。实际上，其中一些语言仅仅是基于 Lisp 或 Scheme，有争议的是，它们是在它们自己的“语言”之下，但是让我们跳过这一点，到目前为止，它们最终拥有自己的生态系统。

这些语言的一个有趣的方面是，它们从不使用 Java 或. NET 这样的虚拟机。描述音乐或声音效果将非常有用，特别是在游戏这样的应用程序中，而且有许多应用程序或游戏是用 Java 或. NET 编写的。我们为什么要学习一种完全不同的语言呢？难道它们就不能是一些音频库，比如 raw-audio libs 或 raw-midi libs，这样我们就可以任意操纵这些声音对象了吗...？

然而，那些 APL 设计者有理由这样做。有趣的是，其中一些在学术论文中有描述。在这些论文中，我从安德鲁·索伦森那里找到了一篇关于他的即兴语言的有趣论文。它是从非常原始的层次来描述的，检查每一种可能性以达到最小的延迟。

[https://open research-repository . anu . edu . au/handle/1885/144603](https://openresearch-repository.anu.edu.au/handle/1885/144603)

即兴是一种“现场编码”的语言(他称之为“网络物理”编程语言)。你可以从 OSCON 2014 的主题演讲中找到答案:

[https://www.youtube.com/watch?v=yY1FSsUV-8c](https://www.youtube.com/watch?v=yY1FSsUV-8c)

## 音频处理用例

APL 处理的典型工作通常是关于音频的“实时”处理。例如，这些工作是什么？

*   想象一个虚拟钢琴应用程序。你按下一个键，应用程序通过将钢琴声发送到音频输出来做出反应。如果你按了一个键，用了一秒钟，那么你不想用它。
*   MP3 播放器解码`*.mp3`文件，并将结果发送到音频输出。解码时间通常比原始 PCM 播放时间短，但如果输出是片状的，那么它是无用的。
*   DAWs(数字音频工作站)用于创作多首歌曲。根据歌曲的不同，它可能需要同步发送音频输出和 MIDI 输出。如果它们不同步，产生的音乐听起来就不对。
*   想想使用数字乐器(如 OSCON 主题演讲视频)的现场表演，可以选择视觉同步。如果应用程序冻结甚至非常短的时间，如不到 0.1 秒，它仍然是有问题的。

在这里讨论的上下文中，这些任务可能不一定是“实时”的，但是这些任务对“精确的”处理时间有严格的要求。

## 实时需求

因此，精确的处理时间似乎很重要。但是它们需要多精确呢？它们应该有多“实时”？延迟 1 秒显然是错误的。100 毫秒？10?1?...对我们来说已经不明显了。VR 中的 3D 动画帧速率需要类似的实时性，例如 60fps、120fps...音频也受到类似的限制，据说如果延迟变成 20 毫秒，那么一些人会注意到。

我应该澄清一点:实时处理不是指“最高效的性能”中的高性能处理。要求是:**一个周期性任务必须总是在预期的时间框架**内被调用和运行。它在某种意义上与高性能计算完全相反。

在现代通用计算环境中，存在由操作系统管理的抢占式多任务处理(进程和/或线程)。Sorensen 的论文讨论了其他可能性(手动协作多任务处理),它们在一些嵌入式环境中是真实的，但是对于 APLs 来说，它将主要是抢占式的。(像 ROLI 积木和 Littlefoot 编译器这样的设备可能是另一种方式。)

在任何情况下，这里重要的是这样一个实时应用程序需要在一个实时线程(或进程)上运行，该线程需要得到操作系统的支持。一般的线程没有这样的保证，它必须以“及时”的方式被调用。

虚拟机还有更多问题。也就是说，垃圾收集器“停止世界”(包括应用程序线程)来收集未使用的内存块，JIT 编译器在运行时将 VM 代码编译成实际的机器代码。它们会导致不可计数的延迟。

索伦森提到了 Ruby 上的 SonicPi、JavaScript 上的 Gibber、Scheme 上的 impedict 和 Clojure 上的 Overtone 作为例子。

因此，像 Extempore 这样的语言通过设计自己的语言来静态生成本机代码，并要求显式的内存分配，从而避免了这些问题。如今，LLVM-IR 是他们常用的代码生成器解决方案。我猜 Lisp/Scheme 解析器可能也是他们的前端解决方案。

截至 2018 年，我们，基于虚拟机的开发人员，将有一些话对这一前提(例如，我们有 AOT 解决方案)，但让我们把它放在一边。今天我更感兴趣的是灵魂提供了什么。

## 语言运行时和声音服务器通信

Sorensen 在那篇论文中提出的一个有趣的观点是 APL 的全栈解决方案与半栈解决方案——仍然可以编写客户机-服务器系统，它可以是进程内的，也可以是进程间的。所以，不是用一种语言和框架实现所有的东西，而是可以使用多种语言和框架，实现每个感兴趣的部分并一起合作。

对于进程内设计，FFI 将提供实时声音服务器实现和客户端语言桥。根据论文，查克，即兴，和 Fluxus 是基于他们。对于进程间模型，SuperCollider 就是一个例子。在这种情况下，即席也是基于进程间模型的(文章说它处于这两种模型之间)。

## 语言障碍

抛开 Sorensen 的论文不谈，我一直对一些可以在 C#和 Mono 中操作的声音对象模型感兴趣。我对什么都不感兴趣。人们创造的. NET Windows。纯 Windows 的东西很快就会消亡。我只对跨平台解决方案感兴趣。

仍然有一些黑客创建了 portaudio 绑定或 rtaudio 绑定。我有自己的 libsoundio 绑定，但也有其他人这样做。Javascript 世界里有 Web Audio API，在 ADC2018 上有一个关于把 Audio API 带入 C++标准(“std::audio”)的会议。. NET 里没有这种东西，他们比 C++或者 Web 落后好几年。

无论如何，原始音频的下一步，我想要的是一些通用的声音对象模型，这些 APL 可以共享。现在他们生活在巴别塔周围，但是一些普通的东西可能有助于改善跨语言解决方案的情况。

然后我可以实例化音频输出轨道并及时播放，协调它们，就像 DAWs 所做的那样。我有自己的 MIDI 播放器，甚至有文本宏编译器来生成 MIDI 文件，所以我有一些基础。

我看了一些 APL，但总是找不到通用的音乐解决方案。他们的作品是特定于某些音乐流派的。这和我在 20 世纪的日本看到的不一样——人们用 FM 合成器和 MIDI 乐器演奏各种音乐，即使表现力有限。

我的理解是不可能有好的语言。取而代之的是，在任何新的可能的语言中可能有一个共同的声音平台。

我担心的另一个问题是，我可能应该用 C 写所有的东西(或者至少提供 C API)。我的主要语言是 C#(尽管现在我已经独立了，实际上我可以自由选择任何语言), C#仍然有许多开发人员，但是如果它有技术上的困难(如上所述),那么它在技术上就是一个糟糕的选择。

索伦森论文中的客户机-服务器模型可能是我接下来要尝试的，即设计原始的声音对象系统，并通过 C#私下操纵它们。

老实说，我并不太关心音频延迟。但是如果我要进行某种形式的现场编码，我会介意，这仍然是一个可能的未来。

## 灵魂发生的地方

灵魂公告刚出现的时候我的想法是那样的。

[![soul-worst-case-scenario](img/aefa99297dffcff6d63f6484bbd971c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7XlFZQ0v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/p4hsr7ea-Ru1M0FBf20Q-qKHutAzOQC-OXwdP6noTzqNrdBttNBl5ETdMvf0sp37PK4Zn9byb_1fhukXnMLnk1IuhkKEZZRyiW8wZDHuQ9dLYdXTWjQakZl5OIrwHIwlOFokgr6bE8tSYrFY8wTYhvf2_jOuE5puQJllDEumJi6NYc0-LlhXrPPVxBzuI8UROQA0btwMS0RDRCWBSrJZB3RSKHCiwkifbAc4ziZEBmiNNVbOU1DKSgDYWxXariCZ10GCqc9TKdvymEupdpkHCC8-47QNkS5skRXMI0oSWzQeioprOOo2TmnE1iD6L296lWGZiBUB5BpCljPCzcvv2QgX5v6qmvdwAW4otJ3yEtpZ1jG7JPWBTct-1xEso3MWQ0QEjze41CXYpGHR8bECcBnChjXLmLJIEQX9xnGw4L3l8VHYxwpsM5wga4O2hKqnjxwY1Yh9lPAsQ2SONT7a11MpQcc50mzUUidVx6RQxDuvYNznUCvz9rTnV2K2hr7HJb0acLq47OkYP0KACeNbvZTbg3o1s78pzywpPyiZ34Qa5m0RGFi3c1YfRWjRjXu53j7qzbWxOGvj6l52gbnBPz282bTaetlAjt1YiDJX3wIEl9qqxAAeVjB4n3FuIk7m4c-iwswC3Wm3MyGDvFO9MTR2DA%3Dw971-h491-no)

(我没有图片存储，所以我把它们放在谷歌照片上，你必须导航到它们...)

这是视频流的截图。你可以使用任何语言来编写一个程序员来操作 SOUL API，它可能类似于 OpenGL 着色器。使用 LLVM IR 将 SOUL 语言源代码编译成一些本机代码，并通过 JIT 运行。

好在声音平台本身并没有放弃音频延迟。它实际上旨在通过为声音平台的硬件加速处理提供机会来提供更好的解决方案:

[![soul-hardware-accelerated-case-scenario](img/ccfd04a8c3a9305ea21d8aa7de5ab075.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zmk7joCM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh3.googleusercontent.com/-wiCGiJanLo66PJ0R78nzCbACHiK1iCiQHc7jBMOp8nUMnxQMVUCIbTCTIJ9Tmgc3lOO9Bf6dpoYZY2XbqsluE77UqmdFpTDHssTH0FwjLmUN6c8DpKEqwstCK9QAM_l_IcJpEZLxCdaLYhJqsM29yS6W5Dbb9CUcx_qI6aoFGgZ6W0oQEe_eRW98C8ese6ZWrBdZw7mOQOOcNUlGb27vXvOWHABC1mAlQuLCvpntMHrhlrvzeYvlAYsx9kg-crlIp4_Z3g23xxTFeIAsvsKUrRnHemsYxAhG61gzBdwoyub6OlZSe6lJd2MgQgvO3nE9oahdNNtJ8uwXmOPHD4TJEOVbbi8SDQj0P7s6kANdzVhDdkn0nKjwva7kMDTLDQFYpcsZ48aZ7WDHS_C4N8eV1YyNGwUZyH3C5J4tkyBzZexX5NpRoI0diibcdcpEj81DRvm9wsomm5TtnOd3FDq91zUdRbZ5-VtomCYcG2RNSfKf5YTlIayjh_NZpGlEETOgSrfXFzxyNm11CjgK_QNOnPyxKe_cYMx9AtPxQhJTtdn_0ZN_uFyBj39Y2EiL1oRFsvaiwtivsllatDjLJ8xAoKU6ANkkhg0wsQ4CqP04JdpV_oY5HpEG_ReHR3q8HcnM7uAvO5REwxh6Cn9eE_ZI4VDBg%3Dw972-h490-no)

硬件驱动程序加速的程序运行在更好(更少)的延迟上。(当然有一些商业机会是灵魂语言开发者可以瞄准的。)

是的，JIT 是那些 APL 设计者担心的事情，所以我在理论上假设。这里的解决方案类似于如何编译着色器，即 GPU。GPU 很受欢迎，但对于这种灵魂设备，我们大多持怀疑态度。这可能是也可能不是时间问题，我现在不知道。

## 思想

我对 SOUL 的担心主要是关于编译管道，尤其是周转时间有多快。如果从按键产生钢琴声音有点慢，那么它可能不是一个解决方案。

作为一种实时编码语言，它将 XTLang 源代码编译成 LLVM IR，这样当它被调用时就可以立即执行。但它也涉及到编译，这些语言的用户会在他们的编辑器上进行预编译。编译应该还是要花一些时间的，除非语言本身表达能力不是很强。

SOUL 本身不太可能是高度表达性的(它似乎并不打算成为一种通用语言)，所以客户端语言(对我来说可能是 C#)必须生成相当复杂的代码。

但是因为还没有实际的代码出来，我们将会看到会发生什么。即使它只是一个蒸汽器皿(我想这不太可能)，它仍然是一个迄今为止任何人都可以完成的伟大想法。