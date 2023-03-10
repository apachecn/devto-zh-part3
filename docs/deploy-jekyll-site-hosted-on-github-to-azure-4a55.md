# 将 GitHub 上托管的 Jekyll 站点部署到 Azure

> 原文：<https://dev.to/azure/deploy-jekyll-site-hosted-on-github-to-azure-4a55>

*我们选择了由[迈克尔·克伦普](https://twitter.com/mbcrump)创造的我们最喜欢的技巧和诀窍，并在整个四月在 Azure 上提供新的技术内容！错过一天(或更多)？[赶上系列](https://dev.to/t/azureapril)。*

**没有天蓝色？** [抢免费订阅](https://azure.microsoft.com/en-us/free?&WT.mc_id=azureapril_devto-blog-cxa)。

* * *

我们中的很多人在个人网站和/或博客上使用 Jekyll。

如果你已经有一个现有的基于 Jekyll 的站点托管在 GitHub 上，你可以很容易地将该站点部署到 Azure App Services。

但是为什么呢？如果 GitHub Pages 是*免费的*，那么为什么要付费呢？您可能想要:

*   将你的网站推向私有回购(而不是公共回购)。
*   与变通办法相比，建立“真正的”SSL。
*   利用部署插槽。

我肯定还有更多。无论如何，我认为这是一个有趣的练习，使用一个非常普通的工具。

# 收集所有的碎片📎

我假设你已经有一个 GitHub Pages 站点，它使用托管在 GitHub 上的 Jekyll。如果这是真的，那么你要做的第一件事就是获取这三个文件。

*   `deploy.cmd`:一个 [Kudu](https://github.com/projectkudu/kudu) 部署脚本，处理网站的设置和部署，并确保安装了 Ruby
*   确保安装了最新版本的 Ruby 并确保 Jekyll 已经构建好的站点
*   `.deployment`:Kudu 理解的调用 deploy.cmd 脚本的配置文件
*   你可能已经有了这个，但要确保它在那里，如果没有，就复制我的。

一旦你有了这三个文件，确保它们在你的公共 GitHub pages 站点的根目录下(例如`something.github.io`)。

# 前往蔚蓝门户🖱️

你需要进入你的 [Azure 门户](https://azure.microsoft.com/en-us/features/azure-portal/?&WT.mc_id=azureapril_devto-blog-cxa)(或者使用 CLI 工具)并在 App Service 下创建一个 Web 应用。一旦站点被部署，那么进入部署选项，选择 GitHub，你的项目，然后按下`OK`。

[![app service -> web app](img/0af51cdb6a01ab83cb007d6262707bbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GZP_HB14--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://d33wubrfki0l68.cloudfront.net/c392bee1f1c497df1c946b17178a94905c5df36d/04b41/files/azuretip16.gif)

您应该会在通知窗口中看到`Setup up Deployment Source…`。你可能需要等待 15 到 20 分钟，让 Azure 完成所有设置。您可以停留在`Deployment Options`刀片上，您应该可以看到部署的状态。

[![deployment options](img/152982a511314275af28d7da299986f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L5ubvy8Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/1137122492a967ccb08ac2b945919f3c8ce8400c/e4c05/files/fetchanddeploy.png)

# Success! ✅

过一会儿，您会看到一个复选标记，表示它已成功完成。现在您可以导航到`Overview`刀片上列出的 URL。

[![overview](img/e67ba875141321a77ca41cc450238c32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ajY1ZL5z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/3bfda6e0d26208be6969b5b1eee21f55f00ea3e4/f5ca0/files/fetchanddeploy1.png)

和...你完了！看看这个。很简单，对吧？！

**准备好超越哲基尔了吗？** [查看 Azure 的应用服务文档，了解特定语言的快速入门。](https://docs.microsoft.com/azure/app-service/?WT.mc_id=azureapril_devto-blog-cxa)

* * *

我们将在四月份每天发布文章，敬请关注！或者跳到前面，查看更多提示和技巧[现在](http://azuredev.tips)。