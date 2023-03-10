# Ploi.io -服务器管理工具

> 原文：<https://dev.to/dennis_smink/ploi-io-server-management-tool-4o8p>

我是一个喜欢尝试新工具的人，我想我已经看到了太多的面板(Plesk，DirectAdmin，cPanel，VestaCP 等等),我可能已经尝试了所有的面板。可悲的是，事实上我对这些都不满意。

用 PHP 开发是我喜欢做的事情，我一直在使用 Laravel，我是它的忠实粉丝。因此，试图找到一种方法来轻松地为我的客户部署我的 Laravel 应用程序并不容易，我已经厌倦了所有我必须做的手动终端工作。

我开始思考；“如果我将所有需要的东西都整合到一个工具中，会怎么样？”

**进入概念**

这就是 Ploi 从一个问题开始的地方。我在 2017 年 5 月开始开发，但很快就放弃了，这是太多的工作，无法在短时间内完成。半年过去了，我对这个应用程序做了一些基本的事情，但它从来没有准备好使用。

然后在 2018 年 2 月，我注意到一个同事-程序员也构建了类似的东西，这激励了我很多。(他制作的这个工具非常小，基本上它所做的就是在服务器上建立一个虚拟主机环境，我们可以用 FTP 上传，或者一键安装 WordPress)。

然后，我满怀动力地在 Ploi 上重新开始开发(这也是这个名字的由来，它源于‘Deploy’)。2018 年 3 月推出的第一个测试版有如此多的动力，另外两个同事程序员帮助我开始，这给了我更多继续前进的动力。

这是目前 Ploi 的仪表板的样子；

[![Dashboard](img/5c68c7325e3e0533c796573e8e19fa20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_xTQBRRd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ploi.img/screenshots/panel-impression/panel-1.png)

我个人低估的是测试、测试、测试的重要性。供应某人的服务器是一项负责任的工作，必须正确执行，这有时会导致一些麻烦。

来封装 Ploi 实际做了什么；

Ploi 使得为你的网站和应用程序提供服务器变得非常容易。您可以使用安装服务器；DigitalOcean、Vultr、Linode 或 Custom VPS(与您喜欢的供应商一起，确保您选择 Ubuntu 18.04)。所有使用 DigitalOcean、Vultr 或 Linode 创建的服务器都配有 Ubuntu 18.04。这是因为我对这个操作系统的了解最多，可以完全理解和支持它。

您可以创建网站并安装 Github、Bitbucket 或 Gitlab 存储库。你也可以一键安装 WordPress 或者用 SFTP 上传你的网站。

默认情况下安装的软件包；

*   Nginx
*   NodeJS
*   MySQL 5.7
*   PHP 7.1(可以通过服务器面板升级到 7.x)
*   雷迪斯
*   监督者
*   UFW(简单的防火墙)和更多处理小事情

配置好服务器后，您就可以开始工作了🚀

[![Site](img/b60a624f24c45bacee3217bb43b1cdff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lg1qOuOn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ploi.img/screenshots/panel-impression/panel-3.png)

正如你在上面的图片中看到的，只要输入你的网站域名并添加它，你马上就有了一个在线网站(你必须将你的 DNS 记录指向你的服务器)

更多信息请查看 [https://ploi.io](https://ploi.io)