# 在角度开发场景中使用代理来支持热加载。

> 原文：<https://dev.to/asparallel/using-a-proxy-in-favor-of-hot-loading-in-angular-development-scenarios-43mg>

## 触发警告

> 这是我的经验，也是我的看法。在这篇文章中，我将讨论我是如何从 A-B 来的，以及引导我去那里的经历。尊重，热加载工具没有任何问题，许多人在这方面做了很多工作。它们是特定工作流程的出色工具。
> 
> 如果您喜欢热装载机，并且它们能满足您的所有需求，那真是太棒了！你可以节省自己 7 分钟的阅读时间。然而，你也可以自由地做任何你想做的事情，以你认为合适的方式执行你的代理。

## 一首热负荷的颂歌

> 注意事项:
> 
> *   在这篇文章中，我将热加载和自动刷新互换使用。
> *   本主题将直接引用 angular 的 ng 发球
> *   我不是要拿普利策奖，只是记录我的想法
> *   根据要求，全额退还您的 0 英镑报名费

热加载是目前 FE web dev 的许多本地开发和测试策略的一部分。要点是:

*   每当受影响区域中的文件发生变化时，就会运行一次增量构建
*   一些正在运行的代码/触发器/观察器等通知本地服务器新的构建输出
*   服务器将更新有效负载传递给侦听 websocket
*   考虑到潜在的管道、配置、操作系统、常旅客状态、月相，该页面可能会也可能不会自动加载

这种描述掩盖了一些冰山，但对我们的目的来说足够准确，因为这不是一篇关于这个主题的论文。这种有棱角的味道是这道菜的特色。如果您想了解更多，您的本地库有一个。关于这个的 md 文件你可以在闲暇时浏览。

[![and so it begins](img/94e434fbb7676d1631a87a81705239fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Omp6VxA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/FFuq6A0VvWNBPdPFQRf0dwyCdhEX6hUXaabQpoDeXtKuHiBaEeT8FdCORw2u0AdMjFOg1OJnmnN-ag%3Dw2648-h1754)

<figcaption>How the sausage is served</figcaption>

## 龟吃龟；有点讽刺的异常

我不想再背诵那些有棱角的文件(院子)，我只想讨论是什么让我基本上放弃了上述话题:

### 域是问题

#### 这是一个很好的操作状态。如果有人...

通常，当热加载任何显著复杂性的代码时，应用程序的当前状态要么不适合在热上下文中打补丁，要么应用程序配置/引导/依赖性将导致无效状态，这在生产中或在不在热加载配置下运行的任何系统上都是不可能的。这是许多热加载场景的摘要。

#### 你要吃那个吗？

第二个经常困扰我的场景是当我在 UI 或调试中检查我的代码的最终输出时:修改一些少量的代码，然后砰的一声，页面刷新(或者我的代码的一些秘密部分在幕后被更改，这真的不会更好)。这充其量是不和谐的，很可能是令人抓狂的，因为您不仅丢失了调试上下文，还可能丢失了您正在测试的精心构建的边缘案例，该案例可能不容易恢复。

当然，这在很大程度上是因为我喜欢 Webstorm/IntelliJ 等工具的自动保存功能。不过，我认为这个特性比预测重载更有价值。

#### “通过从源代码编译并编写自己的驱动程序，你可以很容易地解决这个问题”——尊敬的 CCNA 约翰·q·尼克比尔先生

在 Angular 中，开箱即用的配置通常是好的。您还可以使用 cli 配置覆盖来配置特定行为:

```
ng serve // serve vanilla dev env build
ng serve --aot --prod // fully production compatible
ng serve --live-reload=false // as on the tin

/// And most other basic args 
```

以及 angular.json，您可以对其进行更改以进一步修改默认行为和某种程度的附加行为。如果你想过危险的生活，你可以在从属配置中做一些低级的改变。

当然，理解所有这些意味着相当多的努力和精神负担，需要在你的团队中复制的多样性是有用的。

#### 时间假装；你的差异将以里程为单位

所有这些都归结于时间。花在猜测你的工具上的时间，花在摆弄配置上的时间，花在生产产品之外的事情上的时间。你的经历很可能基于你所做的事情；与编写管道和结构代码的人相比，设计人员和测试人员更有可能发现这个特性很有用。

## 自我感知自我参照；客观地具体化你的需求

对我来说，问题不在于功能不起作用。它客观上做了我不需要它做的事情。**它做多了**，多了就是默认。少了什么都是工作。这就是问题所在:

> 这个工具做得太多了。

当我查看我的工作流程并了解我实际需要什么时，我只剩下以下内容:

1.  增量构建
2.  观察者自动累积那些增量构建
3.  有效载荷的再服务
4.  读我该死的思想，以确定我什么时候需要重新加载东西

事实上，我需要的是能够按需提供资源的软件。别无其他。现在来看看如何进行:

1.  完成-已经得到“ng 构建-观察”的支持
2.  第二节，和第一节一样
3.  我们需要一些中间软件来实现这一点
4.  被称为刷新按钮的现代奇迹/cmd|ctrl+r/ctrl-f5 ![Refreshing](img/4e210464d1aa3bb335bed122dfc29636.png)

    <figcaption>权力与荣耀</figcaption>

### 最近少走的路

> 警告:以下内容仅在以下情况下有用:
> 
> 1.  您将应用程序作为静态资源提供服务，就像报告层次结构中没有性别限制的权威人士。
> 2.  你想在本地测试本地的东西
> 3.  你想在本地测试远程的东西
> 4.  你有时间，你很无聊

在缠绕领域，有两类工具可以相对容易地用于缠绕:

1.  一个服务器(express、IIS、tomcat、jetty、node、kestrel、python、两个罐和一个字符串等)
2.  代理人(nginx、charles、fiddler 等)

出于这个例子的目的，我将使用一个代理，特别是 charles，因为我今天早上在 mac 上，设置 nginx 可以是一个 PITA。请注意，我没有从制造查尔斯的家伙/女孩/精灵那里得到报酬，但如果这能产生销售，我也不会拒绝接受回扣。此外，请注意，除了在 Windows 上容易和免费之外，您可以使用 nginx 作为配置免费完成此操作。# MacTax # FinishFiddlerForMacTelerik
[查尔斯代理人](https://www.charlesproxy.com/download/)

#### 做着事情

我们寻找的架构过程基本上是这样工作的:
[![The thing in question](img/4291f2fe3c6aad487956a3a81b45dd46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HRLIMKv8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/Dg3L2yOjHsc44NbhjqIy0T8RpO_ppy4rcbGY2FnfvuOBkGp-s-fqbmM3g-kMwZda_vRALG46-hAfIg%3Dw2648-h1754)

<figcaption>Occam's Razor Inaction</figcaption>

为此，我们将使用一个名为 map local 的功能。地图本地有文档，你可以阅读。简而言之，您提供了一个请求配置，并告诉它当出现野生请求时，您希望得到什么服务，EZPZ。

首先使用 ng build - watch 构建您的应用程序(gulp/NPM/无论您做什么，只要它生成一个您可以服务的输出文件夹):

[![image-20181201112307594](img/f5631529781cc121e7ab56169bd4b85f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DeOrzdHo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/CJGOeXMBPpU4DLYcW8oX_JbNMC1S3_VluiC26VBDQqK5DldYpmwiZiFhFkGWISB5XiW1C7l-lmEaiw%3Dw2648-h1754)

接下来，我们将添加指向该位置并以我们的本地 env:

[![image-20181201112652193](img/6987514b654f9b8523dd0fd864bd3990.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--93LWHC_S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/J94qnkZsCzBaevRub5CPMUj_eRkP6W0bPGaXEzXRDkgQoGkpE7NBhBR0FdB8R1LHeZJwEBJjSpt4Dg%3Dw2648-h1754)

<figcaption>Look ma, no hostfile muckery</figcaption>

上面的配置在 serval.local 上创建了一个相当于 DNS 的绑定，它可能是 google，也可能是任何其他的域名。我们一会儿会谈到为什么这很有用。

第一个配置值使用/*将所有路径条目映射到我们的零之旅应用程序的 dist 文件夹。第二，确保对域的直接调用将返回 index.html 文件。这可以很容易地用于命名空间，例如，如果我们想在 serval.local/toz/,找到我们的本地实例，只需将它添加到路径中即可。

> 我们完成了什么？
> 
> 此时，我们有一个正在运行的本地“服务器”,它知道我们的应用程序的 dist 文件夹，可以拦截对已知域的调用，用本地资源进行回复。我们完全控制刷新，因此我们的调试上下文受到保护。

#### 不断给予的东西

所以，这很酷，现在我们有了一个简单、持久的方法来提供我们的内容，它不是构建管道的一部分，并且非常容易设置。但是我们还能做什么呢？

如果你们有注意的话，可能会注意到下面这段文字:

> 并且可以拦截对已知域的呼叫，用本地资源进行回复

确实是的。不仅是虚拟域和本地域，还有远程域。告诉我，我亲爱的程序员，你是否经常发现自己将 console.debug 输出或日志工具部署到非本地环境中，只是为了弄清楚发生了什么？嗯，有了这个方法，你就有了一个真正的电气化六车方法，可以在任何环境下安全地运行调试代码，包括生产环境。当然，前提是代码不是以一种不容易被模仿的方式在服务器上动态生成的。但这就是我们为什么要做水疗和水疗的原因，不是吗？这是整个关注点分离橡胶接触现实世界路面的地方。

让我们设置一个示例，我的虚构组织具有以下简单 CI/CD 的域构成:

```
beta.servalcorp.io -> dev
gamma.servalcorp.io -> test
servalcorp.io -> prod 
```

让我们设置一些配置规则来表示我们的环境覆盖，我们将暂时禁用这些规则:

[![image-20181201115934772](img/bfbfef66396b46761031ae960ad2e56a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0P3LgVvw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh5.googleusercontent.com/5wbS-bX26Nwe8W0RT-DJ7gGOrDQsWcxO8jFz9P0cj5xmKgawB77eTzCH4UkRKBg-jSbbjO6eP9m4Iw%3Dw2648-h1754)

首先让我们看看 prod 环境。在 chrome 开发工具中访问 servalcorp.io，我们看到了预期的生产负载:

[![image-20181201115703344](img/8fa01dadd80aa43908ec7bfad9cd8d82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sMbHe4th--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh6.googleusercontent.com/WYfxdbR6Ok3gmA7CAeeVop5JJfZjFmGDmyFjdLTg_oQHD0JWj-6FdIjoi40KH1UAaGw6wPrKOQScaw%3Dw2648-h1754)

<figcaption>100% martian</figcaption>

因此，让我们使用 ng build(开发构建)进行重建，在 map local 中启用 serval.io 生产规则并重新加载:

[![image-20181201120050713](img/f982056dd6e18c05be94d21a20a852cf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ud1ylbGS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh6.googleusercontent.com/bcP1y3x3RGalGm7HWyqt5sDbrsNFZiS1TiJfbhndFExRL2qYEK-MV1HWH-zOyGjTxI0ZPrvy1VNbQw%3Dw2648-h1754)

现在，就像变魔术一样，我们有了我们想要看到的可调试源代码:

[![image-20181201120131815](img/77df1381daa0b6e2b50e505705fc656a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1jYMWna6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://lh4.googleusercontent.com/rlgdB1y3TanMovhxDiTxW9qAeip2jKuoCWWEM_zNwls8OBoyOuyM7DrKpSTyBblJkiIxy-fNSMPxBw%3Dw2648-h1754)

#### 但等待还有更多

API 调用呢？我们也可以对此应用相同的模式(配置因平台而异)。这不仅对于模拟数据、处理出错的服务、进行 E2E 测试(这里推荐 nginx)以及总体处理网络问题非常有用。这很简单，只需准备好您的有效负载，配置您的映射条目，然后就到此为止。

发挥你的想象力！如果不行，就借别人的。

### 就这么定了！

这很大程度上代表了我到达现在这个位置的心路历程。一圈又一圈，用我的头撞墙，意识到我可以绕过墙，实际上由于它的优点而结束在某个更好的地方。我认为热加载框架有它的位置，只是不在我的开发过程中。

TL；dr summary 看起来会像这样

* * *

| 使用代理的好处 | 使用代理的缺点 |
| --- | --- |
| 无热加载 | 无热加载 |
| 更简单、隔离的体验 | 更难打包以便与源代码一起发布 |
| 对提供的内容进行更精细的控制 | 没有单一的通用跨平台工具 |
| 没有默认，选择自己的冒险 | 没有默认，选择自己的冒险 |
| 能够在上层环境中轻松使用调试版本的代码 |  |
| API 仿真 |  |
| 即兴负载测试 |  |
| 强大的请求分析 |  |
| 所有环境下的一致模式 |  |