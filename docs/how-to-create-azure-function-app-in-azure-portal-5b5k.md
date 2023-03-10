# 如何在 Azure 门户中创建 Azure 功能 App

> 原文：<https://dev.to/nilameganathan/how-to-create-azure-function-app-in-azure-portal-5b5k>

如何在 Azure 门户中创建 Azure 功能 App

本文帮助你学习如何在 Azure Portal 中创建 Azure Function App。

Azure Function Apps
Azure functions 是一个在云中执行小行代码或功能的解决方案。我们也可以根据自己的需要选择编程语言。我们只为你的代码执行的时间付费，也就是按使用付费。它支持多种编程语言，如 C#、F#、Node.js、Python、PHP 或 Java。它支持持续部署和集成。Azure functions 应用程序让我们开发无服务器应用程序。请参阅 Azure Functions 的官方页面了解更多详情

先决条件
微软 Azure 账户。
按照以下步骤创建 Azure 函数。
第 1 步
在仪表板中登录到【https://portal.azure.com/】的
，选择创建一个资源并点击 compute。现在选择功能 App，

[https://thepractical dev . S3 . Amazon AWS . com/I/gu6ar 65 eqj 2 wl 786 vbbz . png](https://thepracticaldev.s3.amazonaws.com/i/gu6ar65eqj2wl786vbbz.png)

点击创建

[https://thepractical dev . S3 . Amazon AWS . com/I/faksq 2 C4 F6 gcdg 6 VOB GB . png](https://thepracticaldev.s3.amazonaws.com/i/faksq2c4f6gcdg6vobgb.png)

第二步

输入要分配给逻辑的名称，选择订阅和组织相关资源的资源组，托管计划(该计划基于执行和资源消耗。它还有每月 100 万个请求的免费限额和每月 4，000，000 GB-s(千兆字节秒)的资源消耗。)和存储您的功能应用程序的位置。然后，点击“创建”。

[https://thepractical dev . S3 . Amazon AWS . com/I/c8 sl 3 wgllpp 1 daepew2 I . png](https://thepracticaldev.s3.amazonaws.com/i/c8sl3wgllpp1daepew2i.png)

这是仪表板。点击功能应用。

[https://thepractical dev . S3 . Amazon AWS . com/I/u 9 cgyb 6h P3 klebscho 2 q . png](https://thepracticaldev.s3.amazonaws.com/i/u9cgyb6hp3klebscho2q.png)

第三步

创建功能应用
现在，点击功能创建一个新的功能应用。

[https://thepractical dev . S3 . Amazon AWS . com/I/czn 5 D1 m2 cgvvdfo 5t 8 w . png](https://thepracticaldev.s3.amazonaws.com/i/czn5d1mj2cgvvdfo5t8w.png)

接下来，我们必须选择门户。

[https://thepractical dev . S3 . Amazon AWS . com/I/9 bn 7hz 2 kjfxzz 23 BF kgk . png](https://thepracticaldev.s3.amazonaws.com/i/9bn7hz2kjfxzz23bfkgk.png)

接下来，我们选择 Webhook + API。

[https://thepractical dev . S3 . Amazon AWS . com/I/w6 rutcj 2d 1 UCA ev 11 c8 t . png](https://thepracticaldev.s3.amazonaws.com/i/w6rutcj2d1ucaev11c8t.png)

接下来，我们有一个编辑器，它预加载了 run.csx 文件，您可以在其中找到一个预定义的方法，当 API 被调用时将触发该方法。“运行”按钮用于构建和执行代码，“保存”按钮用于保存在代码编辑器中所做的更改

[https://thepractical dev . S3 . Amazon AWS . com/I/h4l 0 osligk 9 suls ed 68 o . png](https://thepracticaldev.s3.amazonaws.com/i/h4l0osligk9sulsed68o.png)

步骤 4
测试功能 App

为了测试应用程序，我们在屏幕的右上角提供了测试功能

[https://thepractical dev . S3 . Amazon AWS . com/I/MW 35h 47 dqon jomo 33 ADI . png](https://thepracticaldev.s3.amazonaws.com/i/mw35h47dqonjomo33adi.png)

点击测试。

[https://thepractical dev . S3 . Amazon AWS . com/I/DG C2 zohckth 7 HC 1 sgeb 0 . png](https://thepracticaldev.s3.amazonaws.com/i/dgc2zohckth7hc1sgeb0.png)

我们会有产出。

[https://thepractical dev . S3 . Amazon AWS . com/I/PE 2 O2 k 4 Yb y4 jfe h0 LWC 8 . png](https://thepracticaldev.s3.amazonaws.com/i/pe2o2k4yby4jfeh0lwc8.png)

第五步
运行功能 App
让我们在浏览器中测试 Azure 功能 App。现在，我们保存并运行工作流，然后运行它。现在点击获取函数 URL

[https://thepractical dev . S3 . Amazon AWS . com/I/Pb 5 keha 2r 3 qwea 333841 . png](https://thepracticaldev.s3.amazonaws.com/i/pb5keha2r3qwea333841.png)

复制网址

[https://thepractical dev . S3 . Amazon AWS . com/I/t 0 LV 60 w4 M9 br 5 Bao 6 lpj . png](https://thepracticaldev.s3.amazonaws.com/i/t0lv60w4m9br5bao6lpj.png)

用&name=Alagunila 粘贴到浏览器上。

[https://thepractical dev . S3 . Amazon AWS . com/I/xe 5 no QL 10 dl 4x JC 7 meiv . png](https://thepracticaldev.s3.amazonaws.com/i/xe5noql10dl4xjc7meiv.png)

输出

[https://thepractical dev . S3 . Amazon AWS . com/I/TT 5 JH 2 jtp 7 f 7 p 4d 8 qh6m . png](https://thepracticaldev.s3.amazonaws.com/i/tt5jh2jtp7f7p4d8qh6m.png)

我希望你明白如何在门户网站上创建一个 Azure 功能应用。敬请关注更多 Azure Function App 文章。