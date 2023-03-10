# 学习 Javascript、条形码扫描和进入私人杂货店 API

> 原文：<https://dev.to/mtownsend5512/learning-javascript-barcode-scanning-and-wiggling-into-private-grocery-store-apis-30bh>

我已经做了几年的后端 web 开发人员了。在数据库、面向对象编程和漂亮的框架(如 Laravel)的水域中游泳，为我工作的公司 DieselCore 创建一些相当健壮的 web 软件。但是现在，我意识到早就应该学习一门新的编程语言了。当然，我以前使用过 Javascript 插件和包，我认为自己有足够的能力解决实现部分，即使我并不真正理解语言本身。

我硬着头皮报名参加了[韦斯博斯](https://twitter.com/wesbos)‘免费 30 天 javascript 课程， [Javascript 30](https://javascript30.com) 。从我写这篇文章的那天到我注册已经整整 14 天了，而我仍然只完成了一半。事实上，这大概是我的 javascript 培训的范围，只是顺便介绍了一些其他的语言。

这就是为什么我为在如此短的时间内创造了我所做的一切而感到自豪。一个工作的 web 应用程序，它扫描条形码，与杂货连锁店的非公共 API 对话，将产品添加到列表中，计算总额和销售税因素。

#### 第一世界的问题

让我回到几天前。我的妻子 Kaitlynn 和我正在吃晚饭，而我们的孩子在玩耍，她给我出了一个现实的问题:“我讨厌去 T2 HEB T3(我们当地的杂货店)，因为直到我在收银台前，我都不知道我们的总数会是多少。到那时，我可能会认为我篮子里的东西不值得。”我咧嘴一笑，“哦，太可怕了。”她笑着继续说道，“但如果我用这个应用程序点餐，我会觉得被欺骗了，因为我们最终会专注于价格，一遍又一遍地吃同样的食物。如果我不在那里，我就会错过看到新食物的经历，这些新食物会激发出新的饮食创意。”

我承认，我个人喜欢 web APIs，我自己也构建过几个。我开始思考解决方案。“我敢肯定，总有一款应用能满足你的需求”，我说。我查了一下，确实有，但仅限于离我们很远的地方。即使是第一世界的问题，我也喜欢解决问题，我的妻子是一个了不起的家庭主妇。如果我的工作能让她轻松一点，我就上瘾了。

我查看了 HEB 的应用程序，果然:他们有条形码扫描。我查了一个条形码，试着在 HEB 的网站上搜索一下——不行，UPC 码没有结果。但是我**知道**有一个端点为 UPC 代码提供产品结果。但是我怎么知道它在哪里？

#### 巧妙的解决办法

我记得我公公一直讲的一件事:WiFi 蜜罐。有了合适的软件，你可以查看网络流量。所以，我策划了一个计划。我会在使用应用程序时监控我的家庭网络流量，并尝试捕捉传出的 API 调用。

过了一段时间，我最终选定了最简单的解决方案:一款名为 [Thor](https://itunes.apple.com/us/app/thor-http-sniffer-capture/id1210562295?mt=8) 的 iOS 应用。我启动网络嗅探器，打开应用程序，扫描。我交叉手指，开始浏览网络日志，希望能找到金子。

[![alt text](img/eaa3ec5834a839dd2225ee7d53882658.png "Thor, an iOS network sniffer helped me discover HEB's api endpoint")](https://res.cloudinary.com/practicaldev/image/fetch/s--DKPRSYVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/b4928Mp.png)

答对了！我有一个端点，更重要的是，我有一个 api 键。这开始看起来像一个真正的可能性。

我打了几个测试电话，效果非常好。我得努力构建界面。对于我的 UI，我选择使用奇妙的 CSS 框架， [Tailwind](https://tailwindcss.com) 。我用之前 14 天学到的知识连接了所有的 Javascript 组件，事情开始变得不错。

[![alt text](img/0c88793b7cadf736dbdf906b3d6a7804.png "Tailwind CSS and FontAwesome create some effortlessly stunning web apps")](https://res.cloudinary.com/practicaldev/image/fetch/s--TMNDawxx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/fD2whud.png)

在尝试使用 Javascript 的`fetch`函数时，我确实遇到了一些问题，即 [CORs](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) 错误。因此，对于远程 API 调用，我创建了一个快速的 PHP 脚本来代表我的 javascript 应用程序发送 cURL 调用。这就是 PHP 在这个项目中的参与程度！

#### 对着镜头微笑

现在，我所要做的就是找到一种方法，使用设备的摄像头来转换条形码，并将其传递给 API。我一直在尽可能避免项目的这一部分，因为我很清楚自己知识的局限性，知道条形码扫描比我现在能处理的更高级。

幸运的是，有一些非常可靠的 JS 框架可以处理这种事情。经过一番挖掘，我发现了奎加。我在配置时遇到了一点麻烦，主要是因为他们提供的例子与他们使用的源代码不是 100%匹配，但最终我让它工作了。

这里有一个有趣的事实，对于从来没有用 Javascript 处理过设备的摄像头权限的人来说:**你需要一个 SSL 证书**。如果您的脚本运行在不受保护的协议上，浏览器甚至不会请求访问。对我来说，解决这个问题是轻而易举的，因为我使用[代客](https://laravel.com/docs/5.8/valet)来运行我的本地开发服务器。`valet secure <folder>`大功告成。我运行的是 https 协议。就这么简单。

在不到两天的时间里，我就有了一个 web 应用程序的工作测试版，它有望治愈我妻子的购物烦恼。你自己看吧。点击下图观看它的运行。

[![View the HEB barcode scanning web app](img/b504610d44d78331602299ae4fab3767.png)](https://vimeo.com/328779418)

感谢你花时间分享我的故事。如果你是一名开发人员，我希望这能激励你去做一些伟大的事情。

#### 使用的技术

*   [托尔](https://itunes.apple.com/us/app/thor-http-sniffer-capture/id1210562295?mt=8) - iOS 网络嗅探器 app
*   用于 ui 开发的 CSS 框架
*   [字体牛逼](https://fontawesome.com/) -图标
*   [Quagga JS](https://serratus.github.io/quaggaJS) -摄像头接入和条形码扫描
*   [代客](https://laravel.com/docs/5.8/valet) -支持 ssl 的本地网络服务器
*   [HEB](https://www.heb.com) -感谢他们理解我借用了他们的 api😅

#### 对了...

HEB，如果你正在读这封信，我对承包网站开发工作持开放态度，我对 API 很在行😉

##### 我喜欢分享

我的 [Github 档案](https://github.com/mtownsend5512?tab=repositories)上有几个免费的开源包可供下载。看看他们，看看他们是否能在你的下一个项目中为你节省时间！