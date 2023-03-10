# 给被宠坏的人的微软 Azure

> 原文：<https://dev.to/azure/microsoft-azure-for-spoiled-people-5bgh>

我被伟大的软件宠坏了。真的被宠坏了，因为作为一名开发者倡导者和独立应用开发者，我有幸选择了我最喜欢的平台并部署到这些平台上。我已经习惯于创建快速部署的可爱演示来展示这种或那种技术。为了做到这一点，我有非常非常好的软件平台，但我的许多非常可爱的应用程序从来没有接近生产发行。

> 披露一下，我现在是微软的员工。显然是时候认真对待了。

我第一次被 Parse 宠坏了，发布了一堆小应用程序，它们使用 Parse 的数据库和甜蜜的推送通知服务，用于我的网络和移动后端。然后 Parse 的官方服务在被脸书收购后于 2017 年*被关闭。

[![tantrum](img/1059ad5ce32e7e014f05bedb383f2f01.png)](https://i.giphy.com/media/gwK8djpnM8lt6/giphy.gif)

在造成一个最好被遗忘的场景后，我搬到了 Firebase。对于实时数据库更新、推送通知和分析来说，它确实是下一个最好的东西。最终，随着 ML Kit 的出现，它甚至包括了非常酷的机器学习集成。你瞧，Firebase 提供了虚拟主机！太好了！我可以用它作为一个甜蜜的 MBaaS**和网站托管。

就像~~糖果店~~巧克力工厂里的孩子们一样，开发人员通常会被这些为他们的小应用程序设计的精美平台所吸引，只要它们具有成本效益，展示效果好，并且能够尽快获得项目并运行。

[![choco factory](img/5ee6b93c5aac8ba449acbe8632b1128f.png)](https://i.giphy.com/media/slkPSzf2jgWqs/giphy.gif)

然而，在某些时候，你可能需要从小型项目扩大到中型项目。如果您的项目超出了 Firebase 等优秀平台所提供的资源或功能，那该怎么办？在这一系列教程中，我将讨论如何在微软健壮的、企业友好的云产品 Azure 上部署一个 web 项目。在以后的教程中，我将向您展示如何通过添加一个数据库等来扩展它。所以让我们开始吧。

> 🍬🍫🍭如果你想了解 Azure，看看它的众多产品🍬🍫🍭

在本文中，我将带您通过最简单的方法在 Azure 上设置一个 Vue.js CLI 构建的 web 应用程序，并通过 GitHub 进行持续集成，因为像我们这样被宠坏的人没有时间浪费。

注意，有很多关于如何在 Azure 上托管静态站点的很棒的教程，比如[这个](https://medium.com/microsoftazure/deploying-create-react-app-as-a-static-site-on-azure-dd1330b215a5)。这包括在 Azure 存储中发布静态站点文件。事实上，我不希望主持一个静态网站；相反，我想使用 Vue CLI 构建一个 Vue 应用程序，并将其推送到 GitHub，每当您将更改推送到 GitHub 时，构建过程就会获取它。

理想情况下，Azure 风格的 web 托管看起来不像 Firebase 风格的 web 托管，后者需要在本地构建并使用 Firebase CLI 来上传和部署您的内容，而更像 Netlify 的 Vue 应用程序超级简单的连续部署，其中自动从 GitHub 中提取更改，构建并部署。一旦虚拟主机处理好了，我就有了一个更新网站的干净流程，以后我可以用 Azure 产品组合中的其他工具来增强它。

## 第一步:创建 Azure 帐户

出于测试的目的，为了启动并运行你的新 web 应用，你可以通过注册 Azure 来创建一个免费帐户。

[![sign up for Azure](img/2a90d37416c2e6c27dcd23077c4f64c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sYogkPqH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kda5lf6gi6rnq5bjkwxr.png)

一旦你注册了 Azure，你就会找到 Azure 门户。在这里，您可以管理应用程序的创建、部署和其他实现，如 ML 和数据库管理。你也可以通过 Azure CLI 或 VS Code 应用程序扩展(如下所述)来做很多这种管理，但门户允许你直观地跟踪你的应用程序，我发现这很有帮助。

> 有用提示:当你在 Azure 门户中创建资产时，通过点击右上角的图钉图标将它们固定到你的个人仪表板上。以后会更容易找到他们。

## 第二步:构建你的 Vue.js app，配置 VS 代码

创建 Vue.js 应用程序最简单的方法就是使用 Vue UI，这是一个帮助您可视化 CLI 流程的 GUI。一旦你安装了 [Vue CLI](https://cli.vuejs.org/) ，打开你的终端并输入`vue ui`来见证这一荣耀:

[![vue ui](img/6e3c30e22fe0396cd83db30d894c1a06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m-D85ZLc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lpm1freym8w7pqhz6nar.png)

完成一个新的 Vue CLI 构建的应用程序的创建过程将会搭建出一个基本的 web 应用程序，您可以在 Visual Studio 代码或任何您喜欢的编辑器中开始工作。然而，我推荐 VS Code，因为有一些令人惊奇的 Azure 扩展，当你使用 Azure 时，它们使你的生活变得更容易。如果您还没有安装这些扩展，我鼓励您这样做。有一堆:打开命令面板，在 VS 代码市场搜索`Azure`。

[![azure extensions](img/17a622ab4e973a01c9cd2812b9f58c6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6ybpPGso--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uwkoq4d2d55tf9q1ldri.png)

最起码要安装 Azure 账号，Azure 工具和 Azure App 服务扩展。一旦安装了这些，当编辑器提示时，你应该能够从 VS 代码登录到你的 Azure 帐户。一旦登录，你就可以访问 Azure 中为你的团队创建的所有各种元素；要查看它们，你可以点击 VS 代码侧边栏中的 Azure logo。随着你的进步，这些会派上用场。

## 第三步:准备部署

现在，您需要准备好您的宝贝 web 应用程序，以便从 GitHub 进行部署和持续集成。Vue 应用通常从它们的构建目录部署，通常是`/dist`文件夹。在本地构建您的 Vue 应用程序(`npm run build`或在 Vue UI 中运行任务)。

接下来，确保您的应用程序。gitignore 文件不包含您新创建的`dist`文件夹:删除。gitignore 对`/dist`的引用。继续将您的代码库推送到 GitHub repo。

然后，进入 Azure 的[门户，开始为你的应用创建一个环境。](https://portal.azure.com?wt.mc_id=devto-blog-jelooper)

> 注意:您需要为您的应用程序订阅和资源组。在创建新的 web 应用程序之前，您可能需要在门户中创建新的资源组。

1.  单击门户左上角的“创建资源”
2.  点击中间面板的“网络应用”
3.  在“基本”选项卡中，选择要添加 web 应用的订阅和资源组。对于一个全新的帐户，您可能需要在门户中创建它们。
4.  为您的 web 应用程序命名
5.  选择用“代码”发布
6.  在运行时堆栈下拉列表中选择您喜欢的节点版本。
7.  在平台中选择“Windows ”(这很重要，因为我们将创建一个 IIS 用来设置默认文档的配置文件)
8.  为您的资源选择一个离您最近的位置

[![creation of a web site](img/d9b1fc42561ccefd9bd4bbe838f4bd11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9M5LlXe8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l7be3a6ojztis63n7don.png)

然后，保存配置并部署应用程序。Azure 将在`azurewebsites.net`为您创建一个网站，您可以从概述选项卡访问该网站。如果不将它连接到您的代码，它看起来就像这样:

[![burner app](img/beadbd14b046262611f0e1c8e3a0228d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GdQfG38z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c1v6ctm0917rkeoc7s2o.png)

> 提示:您可以将此资源保存到仪表板上；这样做是个好主意，因为在 Azure 中很容易迷路。

## 第四步:将你的 Azure 应用连接到你的代码库

现在，你需要告诉 Azure 在哪里可以找到你的代码库。因为你的婴儿 Vue 应用程序现在在 GitHub 中是安全的，所以进入门户的部署中心选项卡，点击“GitHub”。

> 请注意，您需要将 GitHub 连接到您的 Azure 帐户

[![connect to GitHub](img/a1d852b0f013106e6b590ce3ddebae1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---yACnYqp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f3aiffxjwjkcwyczhuuh.png)

单击“继续”，然后选择“应用服务构建服务”。这是 Kudu，一个构建和部署你的代码的引擎。

[![kudu](img/ed096e97735d0d5d56ed9f832013c963.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jacknt0X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bht6qijnuuxjhb0h8yts.png)

再次点击“继续”，并选择 GitHub 的适当区域来连接您的应用程序。当一切都匹配时，单击“完成”运行您的第一次部署。

[![connect GitHub](img/872481604713a9374aec3e708116790b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S09pb1OK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/62vuevuaa8meyhui3r2z.png)

您可以在它运行时检查日志，看看发生了什么。如果有问题，这很有帮助。您还可以确保构建过程按预期运行。

[![watch the logs](img/cd6a60c97f495c08173bb05791938124.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5qW7RkrU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qjj76721j0jmh9133ny5.png)

如果你现在访问你的网站，不幸的是，你会看到它抛出一个错误。浏览一下日志就能发现问题所在:

[![](img/5b4df8081ca135d0259dd42d420f6fec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7FRfYwzT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d9ht89e7wme5u83avv1r.png)

注意到“缺少 server.js/app.js 文件，没有生成 web.config”消息吗？这是我们的线索，我们需要向应用程序根添加一个 web.config 文件，以向 Azure 显示在哪里可以找到 index.html(提示:它是`dist/index.html`，我们可以看到它是由构建过程复制的)。

此时，您所要做就是创建`web.config`文件，枚举以下设置:

```
<?xml version="1.0" encoding="utf-8"?>
<!--
     This configuration file is required if iisnode is used to run node processes behind
     IIS or IIS Express.  For more information, visit:
     https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/web.config
-->

<configuration>
  <system.webServer>
    <webSocket enabled="false" />
    <rewrite>
      <rules>
        <!-- First we consider whether the incoming URL matches a physical file in the /dist folder -->
        <rule name="StaticContent">
          <action type="Rewrite" url="dist{REQUEST_URI}"/>
        </rule>
      </rules>
    </rewrite>

    <!-- 'bin' directory has no special meaning in node.js and apps can be placed in it -->
    <security>
      <requestFiltering>
        <hiddenSegments>
          <remove segment="bin"/>
        </hiddenSegments>
      </requestFiltering>
    </security>

    <!-- Make sure error responses are left untouched -->
    <httpErrors existingResponse="PassThrough" />
  </system.webServer>
</configuration> 
```

Enter fullscreen mode Exit fullscreen mode

还记得我们如何设置我们的主机在 Windows 上运行吗？使用 Windows 打开 IIS，iisnode 用于路由，这个文件告诉 IIS 在哪里可以找到默认的 index.html 页面。将这个 web.config 文件放到你的应用程序的根目录下，然后推送到 GitHub。应用程序应该会自动重建和重新部署。如果一切顺利，您将在 azurewebsites.net 域上拥有一个漂亮的 Vue.js 入门网站！

[![a fresh web site](img/177ee4459df50bb96e5afdfb25d0dbf9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--76Jd9ste--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1mzjkzyizyndsi3a5g6p.png)

在下一个教程中，我将构建我正在工作的网站并添加一个数据库。剧透一下，它将被称为 Azure 鸡尾酒，数据将由我的波士顿鸡尾酒先生数据集驱动，从 Firebase 导出并导入 Azure，网站上的所有鸡尾酒都将是蓝色的。

> 对于我的第一个技巧，我将使用我的波士顿鸡尾酒先生数据集来驱动一个新的 web 应用程序 Azure Cocktails。只有蓝色鸡尾酒。因为。[pic.twitter.com/OOj3Ebzax9](https://t.co/OOj3Ebzax9)
> 
> — Vx. Jen Looper ([@jenlooper](https://dev.to/jenlooper)) [May 17, 2019](https://twitter.com/jenlooper/status/1129476283853869056?ref_src=twsrc%5Etfw)

敬请期待！

* [Parse](//parseplatform.org) 作为一个开源的、基于社区的项目而存在，并且有更多的力量！

**MBaaS 的意思是“移动后端即服务”。

> 有用链接:
> 🕴 [蔚蓝门户](https://portal.azure.com?wt.mc_id=devto-blog-jelooper)T3】🔌 [Azure Extensions for VS 代码](https://code.visualstudio.com/docs/azure/extensions?wt.mc_id=devto-blog-jelooper)
> 💚[vue . js 入门](https://vuejs.org/v2/guide/)T9】🎨[Vue CLI 及其用户界面](https://cli.vuejs.org/)