# 我已经大到可以记得 COM 了

> 原文：<https://dev.to/t4rzsan/i-am-old-enough-to-remember-com-5gj1>

这个由 [@ben](https://dev.to/ben) 提出的问题把我带回了 90 年代末，我第一次开始专业编码的时候(是的，我就是那么老)。

[![ben](img/fe64a787b888dfb20fc13ad1e466da3d.png)](/ben) [## 你在软件开发中“老到可以记住”的是什么？

### 本哈尔彭 5 月 23 日 191 分钟阅读

#discuss](/ben/what-are-you-old-enough-to-remember-in-software-development-2e28)

我第一次开始在我的辛克莱 ZX 光谱上编码是因为一个幸运的事件(我在一次绘画比赛中获胜，但那是另一个故事了)，但我的第一份真正的工作是作为一名刚从大学毕业的非常年轻的精算师，在一家为养老金公司开发系统的公司工作。

当时，在 Windows 上构建应用程序的实际方式是使用微软 Visual Studio C++ 6.0 的 C++和 [COM](https://en.wikipedia.org/wiki/Component_Object_Model) 。

刚开始的时候我不知道自己在做什么，C++和 COM 都不适合胆小的人，所以我的学习曲线很陡。COM 真的是一个挑战...

> 声明:下面的一切都是凭记忆写的，我的记忆并不是最先进的。请原谅我。

COM 允许您在可以存在于同一进程或另一个进程中的组件之间进行通信。在某些情况下，您甚至可以使用 COM 与其他机器上的组件进行通信(即分布式 COM)。

COM 是基于接口的。如果你想让别人调用你的代码，你必须为它定义一个接口(类似于 C#中的接口)。在 C++中，你把接口写成抽象类。

但是，你可能会问，你如何创建一个可能存在于另一个进程中或者不存在于另一个进程中的对象呢？

这是一个好问题。

显然，你不能仅仅通过调用一个对象的构造函数来提升它。相反，好的 Windows API 有一个创建对象的特殊函数，叫做 [`CoCreateInstance`](https://docs.microsoft.com/en-us/windows/desktop/api/combaseapi/nf-combaseapi-cocreateinstance) 。

这就提出了一个新的问题:`CoCreateInstance`如何知道你的组件住在哪里？这是一大为什么呢？您需要在 Windows 注册表中注册您的 COM 组件。没错！您必须在 Windows 注册表中用一个唯一的 ID (Guid)和承载您的组件的 dll og exe 所在的位置注册您的 COM 组件。

您可能已经可以想象到，每当您需要部署新版本时，试图修改注册表会遇到什么样的麻烦。

但是，你可能会问，如何清理那些可能存在于也可能不存在于另一个进程中的对象？

这是一个很好的问题。

自然，你不能只是`delete`一个你真的不知道住在哪里的物体。

COM 的解决方案是引用计数。每个对象都保存了对该对象的引用数量的内部计数。当然，C++没有内置的方法来帮助开发人员跟踪引用，所以有一堆关于如何成为 COM 开发人员的规则。规则规定了开发人员何时必须手动增加或减少引用计数。您可以通过每个 COM 组件都必须实现的 [IUnknown](https://docs.microsoft.com/en-us/windows/desktop/api/unknwn/nn-unknwn-iunknown) 接口来实现。

如果您忘记遵守规则，内存泄漏将开始堆积。下面是一些关于如何快速创建内存泄漏的伪代码:

```
IHorse *p1;
CoCreateInstance(..., &p1);
IHorse *p2;
CoCreateInstance(..., &p2);
p2 = p1; 
// Oops! You forgot to release p2 before reassigning 
// and to increase the ref. count for p1. 
```

Enter fullscreen mode Exit fullscreen mode

寻找这种内存泄漏是一件非常痛苦的事情。你几乎不知道从哪里开始。在我工作的公司，我们创建了一个特殊的内存日志来记录引用计数，这非常有帮助，我们还会使用[智能指针](https://docs.microsoft.com/en-us/cpp/cpp/how-to-create-and-use-ccomptr-and-ccomqiptr-instances?view=vs-2019)来自动处理发布。但是疼痛...这让您真正体会到现代运行时的垃圾收集。

> 如果你正在用 Swift 或 Objective-C 编程，你可能知道引用计数就是 ARC。ARC 可能会给你带来麻烦，但是苹果设备上的运行时知道引用计数，所以对开发者来说比 COM 容易得多。

但是，你可能会问，当调用可能存在于也可能不存在于另一个进程中的对象时，你是如何处理并发性的？

这是一个非常好的问题。

自然地，当对象存在于不同的进程中时，你将不得不担心并发性，COM 试图通过将对象放在“单元”中来帮助你。

非线程安全的对象应该驻留在单线程单元(STA)中，而线程安全的对象可以驻留在多线程单元(MTA)中。当在注册表中注册对象时，你必须决定它应该在哪里。

当你打电话到另一个公寓时，你的电话需要被一个所谓的编组器序列化。除非你疯了，否则你希望那些编组器是自动创建的，这意味着你不能用 C++写你的接口。你需要在[接口定义语言(IDL)](https://docs.microsoft.com/en-us/windows/desktop/midl/interface-definition-idl-file) 中定义它们，它们将被编译成 C++。

正如你现在所怀疑的，COM 公寓可能会让你严重自残。他们应该有一个警告。例如，考虑用于连接数据库的 [`ADODB.Connection`](https://docs.microsoft.com/en-us/sql/ado/reference/ado-api/connection-object-ado?view=sql-server-2017) 类型。

`ADODB.Connection`是 STA 组件，意味着同一时间只允许一个线程访问对象。在 21 世纪初，并不是所有的开发人员都意识到了这一点，所以人们会将连接对象放在经典 ASP 网站的会话缓存中，以节省时间并加快响应速度。当然，通过同一个线程向网站发送所有请求通常对您的响应时间来说不是一个好主意。

> 当时，包括微软在内的许多开发人员都说 VB6 可以解决 COM 的所有问题，因为 VB6 会为您处理引用计数。这在某种程度上是对的，但是你不知道引用计数的内部工作原理，你很容易在 VB6 中产生内存泄漏，你也不知道为什么！

## 关闭

当时的 COM 大师之一，Don Box，因为 COM 是建立在契约基础上的，所以他创造了一个著名的术语“COM 就是爱”。但是我可以肯定地说，我并不那么喜欢 COM，我一点也不怀念它。这只会让我感激。网那就多了。

> COM 至今仍被广泛使用:办公应用程序严重依赖 COM。如果您想编写一个组件，其函数可从 Excel 单元格或 VBA 中调用，您需要一个 COM 组件。幸运的是，您可以用 C#编写它，并相当容易地将其注册为 COM 组件。。NET 会为你创建一个 shim 来处理引用计数和所有其他的好东西。