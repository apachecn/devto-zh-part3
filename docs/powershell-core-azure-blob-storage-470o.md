# PowerShell 核心| Azure | Blob 存储

> 原文：<https://dev.to/ewertonjordao/powershell-core-azure-blob-storage-470o>

### PowerShell Core | Azure | Blob 存储

#### 设置存储帐户

[![](img/01f10edf292e6c7bd2e282bd168a3bbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s56afcg---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ArkuCI6Gl1ZvQx24vkxNAUA.jpeg)

怎么样，伙计们？又一篇文章，再次表明 **PS 是生命**。我将演示在不进入蓝色门户网站的情况下调配存储解决方案是多么简单。

(enable-anaria)记下我们将在此演示中使用的内容:Visual Studio 代码、PowerShell 核心和蓝色帐户。

首先，我们要安装蓝色模块。

<figure>[![](img/4e888cb952dd43e1fb117ca9c9315642.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ceBfOAS5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcDF6-lmmblc6QWKIZ_kBGQ.png) 

<figcaption>安装模块与蓝方进行交互并认证我们的客户。</figcaption>

</figure>

现在，我们将通过访问地址[https://Microsoft . com/device log in](https://microsoft.com/devicelogin)连接我们的 Azure 帐户。

<figure>[![](img/091573cb15f238b1e88f82acb5338909.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IwkxQW9k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADvGfbjJ7TL3udOu4EfAFsw.png)

<figcaption></figcaption>

</figure>

验证后，我们收到以下消息:

<figure>[![](img/8a76f98b6a9e3b1ac03a5c3d16c58ea2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jhlkjWTX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A8prVkmhvoW4qZFStE6H0Mg.png) 

<figcaption>注册我们的蓝色机器</figcaption>

</figure>

后

<figure>[![](img/163f32f7a3471acd626199755ec0b2ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1Yu_jHLA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/883/1%2AUtZ5C7iwHe3tuDCnayAuVA.png) 

<figcaption>登记认证后返回</figcaption>

</figure>

市场是连接的，我们暂时还是没有蓝色的资源。

<figure>[![](img/090738620e6b3fd9b89d2062b4eaceb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bYmPrm4i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/937/1%2AFCiWGTpOiICeCFD4OakYsQ.png)

<figcaption>Criando Grupo de recurso。</figcaption>

</figure>

<figure>[![](img/b05ce48760011ee402deb5c9b9c744ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X1lWdhkh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AuLpgiJOHYLyRusNJBheRtA.png) 

<figcaption>蓝色可视化。</figcaption>

</figure>

现在，我们将设置存储帐户。

**tip≤ctrl+space 快捷键带来了具有预定义值列表(使用 PowerShell Core 终端)的参数中可用的选项。**

<figure>[![](img/331ad25bf7a6d75459554e365bb92ef2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b6-Qs1U1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/948/1%2ArbI-Yl_PcbII_VQQhAgFMQ.png)

<figcaption>【ctrl+空格】</figcaption>

</figure>

<figure>[![](img/7694e1289fb6f4daea40ebe0f03fc023.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2mkxfKCo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Amy4CF3ZK867Gq9aJwWATeQ.png) 

<figcaption>。</figcaption>

</figure>