# 后台取 API:准备使用！

> 原文：<https://dev.to/webmaxru/background-fetch-api-get-ready-to-use-it-314n>

在过去的 12 个月里，在我的 PWA 幻灯片中，有一部分是关于后台获取 API 的解释。它在我的“高级服务人员”会议的“即将推出的功能”一章中，我只介绍了基本概念，幻灯片上有一些代码。我在 HolyJS 会议上演讲的前一天晚上，我决定在 ChromeStatus.com T2 和 Chrome Canary 查看这个有前途的 API 的状态(我喜欢在我的会议期间报告所有最新的状态),并且……那天晚上我睡得不多——我发现 T4 最新的 API 规范几乎处于工作状态，并花了几个小时进行实验。这篇文章是关于服务人员后台获取的想法，以及如何开始使用它。

> 声明:后台取 API 还是很实验的。它只有粗略的规范建议和 Chrome Canary (M72)中的部分工作实现，并启用了[实验性 Web 平台特性](https://dev.tochrome://flags/#enable-experimental-web-platform-features)标志。所有的概念/接口/方法/事件在未来都可能改变。

### 什么是后台取？

如果我们从“填补 web 和本地应用程序功能之间的差距”的角度来看待渐进式 Web 应用程序的概念，这个 API 带来了可管理和可预测的大型资产(确切地说是资产集)的下载和上传:文件、资源，应有尽有。大到足以开始担心可能的网络中断和/或脚本超时。例如，在本地存储音乐流媒体服务的 MP3 文件，或播客剧集，甚至可能是一些视频，或大型 PDF 文件。

*   你可能会说，我们在没有任何服务人员的情况下做了很多年——只需点击文件的链接，浏览器就会完成剩下的工作:可以暂停、取消和恢复下载。没错，但是您将这个资源下载到了*操作系统的*上下文(文件系统)，而不是您的 web 应用程序。我们使用后台获取 API 从网络上获得的一切都可以存储在相应应用程序的上下文中，供以后使用，没有任何额外的麻烦。
*   现在你说我们只要使用服务工作者的 *fetch* 事件就可以达到同样的效果。是的，我们可以下载一些文件，并将其放入 app origin 的缓存存储中(这是我们通常为 app shell 架构和运行时 API 端点缓存所做的)。这里的区别还是在于该资源的大小——在下载过程中，浏览器是否有可能因为超时而杀死服务人员。此外，从 UX 的角度来看，通知用户下载的事实和进度是有意义的。

后台获取是为了以对开发者和用户友好的方式解决大型资产下载/上传问题。让我们来看看 WICG(网络孵化器社区小组) [repo](https://github.com/WICG/background-fetch) 上列出的这个 API 的特性，扮演规范提案的角色:

> *   Allow the acquisition (request and response) to continue, even if the user closes all windows and worker threads to the source.
>     
>     
> *   According to the definition of application, a single job can contain multiple requests.
>     
>     
> *   Allows the browser/operating system to display UI to indicate the progress of the job, and allows the user to pause/abort.
>     
>     
> *   Allow the browser/operating system to handle bad connections by suspending/resuming downloading.
>     
>     
> *   Allow applications to react to the success/failure of jobs, perhaps by caching the results.
>     
>     
> *   Allows access to resources in the background.

简而言之:在我们注册了一个后台获取并通过这个事实启动了传输之后(这可以在我们的主线程和服务工作者中完成)，我们完全被浏览器(或平台)的可能性所覆盖。在传输过程中，我们可以使用应用程序关闭选项卡—传输不会停止。我们可以关闭浏览器，浏览器重启后传输将继续。网络可能会中断，浏览器在恢复后会自动继续传输。操作的结果将被发送给服务人员(因为应用程序选项卡可能会在那时关闭)。尽管这个 API 的名字，它不是 100%的“后台”操作，因为它让用户清楚地了解正在发生的事情。

这个特性集看起来真的很有趣，让我们开始编码，看看它是如何工作的，看起来怎么样！

### 后台取 MVP

我们从非常小的解开始，只是为了检查它是否可行。计划如下:

*   我们注册了一名服务人员
*   我们通过点击按钮在我们的主应用程序中注册一个后台获取
*   我们在服务工作器中监听后台获取事件。成功时—我们将资产放入缓存存储。失败时—我们仍会尝试将所有可能获得的数据存储到存储中

> 声明:为了专注于我们今天的主题，我们不会创建完整意义上的 PWA(既不会有 app shell 也不会有 web app manifest)，所以我们可以正式地将我们的项目称为“服务工作者驱动的网站/webapp”。

#### 最小代码查看下载开始

步骤 1 的代码如下: