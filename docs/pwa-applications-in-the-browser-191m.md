# PWA -浏览器中的应用程序

> 原文：<https://dev.to/tomavelev/pwa-applications-in-the-browser-191m>

在我开始之前，我想知道，你们中有多少人是图形用户界面程序员——不管你是使用 Web 作为可视化工具，还是一些混合的、跨平台的或本地的工具包？

我知道你的感受，兄弟们

GUI 框架、技术和库从未停止发展。对于 Web——从简单的页面和定制的设计/代码，到基于 jQuery 的框架和组件——响应式框架、材料框架、平面设计、固定和流动布局、基于组件的框架等。在 Android 平台上，推荐的 UI 概念至少改变了 3 或 4 次。

我想知道，你是否尝试过语音技术或其他非传统的用户与软件和硬件之间的交互，如果有足够的时间来发展、教育用户、改进软件和硬件，可能会使传统的 GUI 过时？嗯，会看到的。

在那之前，我们仍然有工作可以养活我们的家人，而不需要彻底更新我们的思维方式和技能。

在我进入什么是渐进式 Web 应用程序之前，我将介绍一点历史。

在过去的二十年里，互联网有了很大的发展。20-25 年前，网络看起来非常不同。大多数互联网站点/网页是同步工作的——需要后端交互的每个小变化都需要重新加载整个页面。

*   全页面重载的第一个解决方法是使用 iframe 标签——将不同的 URL 放入“src”属性；
*   大约在那个时候，异步 HTTP 请求背后的思想首先是由微软通过 ActiveX 对象植入的。ActiveX 对象失败了，目前作为一种技术已经停止使用，但是部分页面重载(AJAX)的思想已经被所有的浏览器所实现，并且现在已经成为一种公认的标准。

在客户机上存储一些东西而不在服务器上持久化的选项非常少，这就是 HTTP Cookie。这种持久性选项有副作用，比如使请求变慢——因为它是随每个请求一起发送的，而且互联网速度还处于初级阶段，仍在发展中(仍然如此，但水平要高得多)。另一种方法是使用请求参数，但是——这对用户来说是可见的，并且不那么用户友好。

大约 10 年前，HTML5 首次发布。HTML5 是 web 标准、API 和可能性的永无止境的发展，它使转变 Web 站点功能成为可能。决定性的一点是，苹果在“I”设备上抛弃了 flash，转而使用 HTML5，迫使整个行业更快、更彻底地跟进——脸书、Twitter、YouTube、LinkedIn 和许多其他公司并不需要太多时间就可以迁移到 html 5，并开始在工具、框架、建议、源代码和改进的 Web API 实现方面做出贡献。谷歌创建了 Chromium，购买了 WebM 视频格式/编解码器并将其开源，chrome 使用的 V8 JavaScript 引擎迁移到了服务器上，Twitter 创建了 Bootstrap，苹果创建了 WebKit，这是 Chromium 最初的核心，等等。

回到今天——几乎在任何地方都可以使用的最通用的平台是网络。在一些小型特殊类型的设备上，它可能不带有浏览器，而是带有一些自定义套接字或 HTTP 协议，但它们大多是特定的和自定义的。绝大多数设备都有浏览器——智能手表、功能手机、智能手机、平板电脑、笔记本电脑和装有任何操作系统的台式机。谷歌甚至投资将他们的浏览器做成操作系统外壳——打造互联网第一笔记本电脑。世界各地都可以访问互联网，即使在供电和互联网连接速度不太好的第三世界国家也是如此。部分由于这个原因，有一些倡议将流行的西方服务打包成使用更少存储和数据的精简版本。

从开发者的角度来看——我们有 html 5——它不仅仅是集成音频和视频的几个标签，或者给页面结构带来更多的语义。HTML5 本身包括 JavaScript (ECMAScript)和许多 API 的发展，这些 API 可以通过 Web 社区接受的特殊类来访问，这些类为浏览器带来了许多以前只能通过本机 API 和应用程序访问的功能。

Progressive Web Application 或 PWA 简而言之就是 HTML5(正常网页范围内的 API)+**Service Worker 特性**(API——在正常网页工作流之外运行的代码中可访问)。

你可能会在网上发现 ServiceWorker 特性带来了“进步”，但我认为如果没有 HTML5 APIs，它就不会有这么丰富的特性。API 在缩写中带来了“应用”的意思。

PWA 与安全性有关，这就是为什么大多数更高级的功能只能在 HTTPS 上使用。

PWA 讲的是速度。有上百万种方法来调整速度，甚至是视觉上的速度感。我会指出其中的一些。

PWA 是关于应用程序可靠性的——它将离线工作，并且在发生时会优雅地失败——给出有意义的消息。

PWA 是关于参与的。带回的往往是更多的心理因素，但无论有什么技术方法可以让用户回来，通知、离线工作、速度等等。

总的来说，它们是一个 API、工具和最佳实践的包，考虑了当前公认的 Web 标准，还考虑了 HTTP 的工作方式，试图在应用程序大小、速度、可用性、响应能力、软件和用户界面质量等方面尽可能提供最佳的用户体验。

PWA 的理念是试图满足用户，并带来网页所能提供的最佳用户体验——即使在离线状态下也能工作，吸引用户使用应用程序，并且在大多数情况下，它高于具体的专有技术，高于具体的软件包、库、框架、语言和浏览器(当它们实现了标准时)。

不管你使用普通的 JavaScript 或 Dart、TypeScript 或任何 XtoJS 技术，你的 GUI 前端代码是由某种工具或某种框架生成的，还是你的后端——PHP、Java。Net，Go，就是在你编程的时候把代码中的后端和前端严格分开等等，只要整件事情，最后的结果就是遵循当前的最佳实践。

这是一种权衡，因为并非所有东西都适合 web，而且有许多小东西需要调整，以解决 Web 在速度、性能、用户体验和每年创建的原生功能方面的限制。但它让你真正有可能为所有用户和潜在的所有类型的设备拥有一个代码库(或至少一种类型),而不是浏览器、Android、iOS、Windows、桌面等的代码库。拥有各种各样的原生应用程序的成本肯定会比网络高得多，因为它们是为适应不同的屏幕尺寸和设备而设计的。

速度的一个关键是–**只要不损害用户体验，尽可能让每一件事情都无阻塞(异步)。**互联网上有关于页面加载后加载 JavaScript 和 CSS 的脚本，因此，为了获得良好的用户体验——页面正在加载的感觉——最基本的样式、图像和脚本可以包含在最开始，其他内容可以在那之后加载。除了启动速度之外，这还推动了功能模块化。

**最小化图像尺寸**–使用 SVG，(CSS 功能–对条纹、颜色、变换、动画重复)，使用 CSS 背景“抓取”的连接图像，使用缩略图版本(图像大小与显示的完全相同，必要时请求更大的图像)，使用更好的压缩(渐进式 JPEG)，使用服务器 HTTP 功能在一个连接中下载多个图像(HTTP 管道)

另一个带来速度的是最小化 JavaScript 创建的增加内存需求的对象数量。在 Android 上这样做的一种方法是 ListView/RecycleView 模式。它是——在内存中有足够的小部件可以在视图中显示。我听说这个概念在 Angular 7 中被实现为虚拟滚动。在其他地方可能还有这种模式的其他实现。

Google 从过去的经验中学到的另一个视觉方面的东西(也可能没有)是将 UI 创建委托给做得最好的层。它是 UI 与逻辑的分离。这个想法已经在 Google 的几个孩子身上实现了——Google Web Toolkit，Angular，Android，可能还有很多其他的(不知道为什么没有——至少还没有——在 Flutter 中实现)。

根据应用程序的需求加载适当的内容——高优先级离线:
使用 Web Worker 从文件系统/应用程序缓存加载。
没有请求或带有缓存 HTTP 头——发出非常小的请求——关于任何更新需求。
没有缓存项目——例如检查更新的端点。

离线功能——你是否了解目标客户，用户的需求等，知道使用什么方法。有些情况下(商业应用),离线功能是不必要的，甚至是不好的——比如 B2B 软件。但大多数情况下是好的。手机浏览的比例在 50%左右。从桌面设备来看，也有很多笔记本电脑非常真实地在非桌面环境中使用，并高速连接到互联网。

因此，从实用的技术角度来看，有几种可用的 API:

*   这样开发者可以选择在具体的地方做什么。
*   互联网连接监听器
*   如果没有网络，服务人员可以做些事情。这就是——当用户在一个地方离线/在线时，使用拦截器/过滤器/控制器模式来分离操作的选择，而不是在每个执行后端调用的地方。

存储信息选项

*   cookie–在客户端和服务器键值存储之间共享
*   文件(文件系统)(仍然是 W3C 草案)
*   本地/会话存储–离线键值存储
*   web SQL(W3C 已停止使用)
*   Indexed db——基于事务的、索引的、类似 Sql 的 NoSQL——服务人员甚至可以离线访问。索引的值可以是字符串、对象、blobs 等。

绘图 API

*   2D 画布——画简单的 2D 作品——线条、正方形、圆形等。
*   用于 2D 和 3D 的 WebGL–用于更复杂的形状，这些形状使用 OpenGL 的浏览器实现提供的，可以使用硬件加速等潜在的速度提升。

推送通知–Web 推送通知从原生移动推送通知中汲取经验，并将其带到浏览器中。工作流程是相同的——获得用户接收通知的权限——保存注册——当出现新情况时，服务器通过带有注册令牌的消息网关发送一条经过修整的信息，消息网关将其传输到设备——仅使用浏览器作为应用程序接收器。

后台获取或后台同步(仍是 W3C 草案)–它注册了一些要在正常页面/窗口范围之外执行的工作。它可以用于下载电影，当用户在线时用手机发送一些信息等等。它遵循移动平台——iOS 和 Android——中引入的相同限制，不会永远在后台运行并耗尽电池，而是在某些事件发生时唤醒。

媒体设备 API:

*   访问相机-用于图像/照片处理，甚至条形码读取(quaggaJS)等。
*   使用麦克风–例如用于浏览器内语音识别

访问 GPS 位置–用户可以向 web 应用程序提供他们的 GPS 传感器的值。从移动设备运行时非常有用。

Web Assembly 的本机性能–Web Assembly 是基于堆栈的虚拟机，它将提供的字节码(WASM)转换为更接近硬件的机器代码。对游戏、繁重的数学计算等有用。应用程序开发人员(像我一样)，使用一些更高级的编程语言，抽象出所有硬件和软件的复杂性——有时会忘记，最终所有的一切都归结为数学运算。

Web Assembly 只是一个从一开始就设计得不太复杂的 VM，因此它可能更容易保护，并且具有一些适应互联网时代的基本思想。它不是一种新语言，而是一种编译/反编译目标，已经瞄准了高级语言，如 C++、C#、Go、Java 和其他框架，以从客户端的本机性能中获得提升，而不是解释的 JavaScript。

目前，它更多地面向工具包、框架开发人员和一些特定应用程序的特定开发人员，他们可以通过直接使用它来获得提升(尽管很脏)。正如我在
[https://www.nerds2nerds.com](https://www.nerds2nerds.com/)播客中所听到的，Web 组装也被移植到浏览器之外，这可能会促进 JavaScript 的后端使用(或其他一些用法)。

正常打开时——浏览器的导航栏可视，用户可随意访问。PWA 的一个功能是为你的网站创建一个桌面快捷方式，它将在浏览器会话中启动你的网站，但它将删除地址栏，使它看起来更像一个应用程序。

需要指出的一点是——将 PWA 功能引入到设计成具有应用程序外观和感觉的网站是一个好主意。否则他们看起来会像涂了口红的黑猩猩。

权限 Android 6.0 及更高版本，以及 iOS(不确定从什么时候开始)都有。每次使用更私密的传感器或功能都需要得到用户的许可。使用更多存储/内存、摄像头、麦克风、推送通知、GPS 定位等。这是应该的。

在 Web 上请求权限具有相同的用户体验，这是在本地应用程序上的最佳实践，在需要时请求权限，而不是在第一页加载时！今天有大量的东西戳到了用户的眼睛——Cookie 隐私政策、广告、登录、“立即支付”功能。对于已经不堪重负的用户来说，权限、推送通知和添加到主屏幕只是多了一些东西。在你正式求婚之前，应该有一些浪漫和约会。

所以，如果有人告诉你他用 HTML(5)/PWA 编写商业应用程序，不要嘲笑他，因为你可能还没有更新你关于浏览器中可供开发者使用的信息。它不仅仅是简单的标记。是的——你可能不会黑掉 NASA，不是所有的东西都完美，不是所有的地方都支持所有的东西，沙盒和保证软件安全不是一件容易的事，甚至是无止境的任务。但是时间不会倒流，例如，我们可能不需要支持 Internet Explorer 6。

例如——苹果在实现一个部分的所有 PWA 功能方面落后——它们不是全部——通过了 W3C 标准和其他标准——这将损害他们的收入(应用程序商店),而网络的开放性使从内容废弃中赚钱的谷歌在访问内容和应用程序方面的技术壁垒会少一些。

你可以检查一下 https://caniuse.com/上有什么可用的，但是作为一个防御性的开发者，你可以看看你当前的浏览器是否提供了一些功能，比如:
if(navigator 中的‘service worker’){
……
if(navigator . geolocation){
……
，并相应地做一些变通或添加一些消息。

我目前还不能向你展示一个完全可用的 PWA，因为我的个人项目还在使用中——还没有为广大公众做好准备。我已经实现了一些最佳实践，并在浏览器中使用了一些类似应用程序的功能。

*   在我的博客中:[https://tomavelev.com/blog/](https://tomavelev.com/blog/)我实现了 CSS 和 JavaScript 的异步加载。
*   我曾尝试用 2D 图片库来可视化与食物和健康相关的不同东西之间的关系[http://kakvoiadesh.com/app/graph/index.jsp?bodyPart=4&l = en _ US](http://kakvoiadesh.com/app/graph/index.jsp?bodyPart=4&l=en_US)
*   我曾使用谷歌语音文本识别来输入食品原料[https://tomavelev.com/app/index.jsp?addbarcode=](https://tomavelev.com/app/index.jsp?addbarcode=)。该功能目前不从应用程序的域工作，但将在我完成 PWA 转换后。

所以基本上这就是为什么浏览器使用并将使用更多的内存、存储和资源，并将继续朝着这个方向发展。我希望将来网站的浏览器会话与应用程序分开，这样将来可以更容易地清理一些特定网站的数据，而不是将所有资源放在一个地方。

我演讲的视频可以在 YouTube 上看到:[https://www.youtube.com/watch?v=N9Ti2Taa8iQ](https://www.youtube.com/watch?v=N9Ti2Taa8iQ)