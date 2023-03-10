# 使用 Hover & Netlify 进行网站部署

> 原文：<https://dev.to/terabytetiger/website-deployment-with-hover-netlify-1b7j>

**这个帖子不是赞助的，我只是真的很喜欢 Nelify & Hover**

# [T1】简介](#intro)

你已经工作了几个星期，利用一切空闲时间编写代码。但是现在你准备好了。准备好向所有人展示你建的惊人的新网站。

但是怎么做呢？

在这篇文章中，我将介绍通过 GitHub 在 Netlify 上创建站点的过程，从 Hover 添加一个自定义域，并设置子域(如果您不熟悉子域，不用担心！我们稍后将讨论这一点)。

您可以直接从 Netlify 购买域名。就个人而言，我更喜欢悬停，所以这就是我将在这里演示的。任何域提供商都应该提供类似的过程。

# 先决条件

*   [Github 账户](https://github.com/)(或者 GitLab 或者 bit bucket——随便你选)
*   [净值账户](https://app.netlify.com)
*   [Hover 帐户](https://www.hover.com/) -只有当你通过 Hover 购买和连接自定义域名时才需要。
*   为你的站点创建一个 GitHub 存储库，并将你的文件上传到那里。

## 如何 Github

如果您在最后一个先决条件方面需要帮助，请随时联系我，或者看看这些文章是否有帮助:

[![mollynem](img/8fe7a578854aa9a7ad1fc660b804fce6.png)](/mollynem) [## Git、GitHub 和工作流基础知识

### 莫莉永远永远

#beginners #webdev #git #github](/mollynem/git-github--workflow-fundamentals-5496)[![terabytetiger](img/9b521f2f5d325eb374d94ba7438d1bee.png)](/terabytetiger) [## 速成班:git 行话

### 泰勒诉(他/他)5 月 1 日 195 分钟阅读

#github #git #beginners #codenewbie](/terabytetiger/crash-course-git-lingo-1enj)

# 部署到网络

第一步是让你的网站上 Netlify。从[app.netlify.com](https://app.netlify.com)，从 Git 点击**新站点按钮。**

[![Netlify's green button labeled "New site from Git"](img/809f6c3c1926580b390a34cbbde1f1c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ROUWCQb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/35jurllgogd130u0sgxm.PNG)

在下一页的“持续部署”下，选择“GitHub”(或者您选择的 git 服务)。

[![Screenshot of the continuous deployment section on Netlify. Options for Github, GitLab, and Bitbucket are shown.](img/c29b40a7a41dc382fa393dd610060773.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0qWIBDNB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/catyyjqhml8qulyr4fas.PNG)

这将打开一个新窗口来验证您的 GitHub 帐户。登录并批准连接(当然是在阅读了细则之后)。现在，您将选择要从哪个存储库进行部署。对我来说，我将使用我的“非常糟糕的设计”库。

[![Wizard step for selecting which repository on Github to select. Shown, a search for "Really" has returned a single repository.](img/8cad917845257625b4801b1fde03339b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PWcqftXf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ilrqzolgkw4o8k9wk82l.PNG)

现在，您将通知 Netlify 您想要从哪个分支进行部署，以及如果您使用静态站点生成器(如 [Gatsby](https://www.gatsbyjs.org/) 或 [Gridsome](https://gridsome.org/) )时的构建过程。

在我的例子中，我只部署了`index.html`和`index.css`文件，所以我没有构建过程。一旦准备好，点击**部署站点**按钮，你将被引导到一个大致如下的页面:

[![Deploy settings page. Options for selecting the owner, branch, build command, and publish directory are shown, as well as a "Show advanced" button. Below all of this, an aqua "Deploy Site" button.](img/e0165a7e06344cc873c0a50f8807368f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6im-Seeo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/guyjuuusur9kxy1oz3wh.PNG)

此页面上重要的一点是，最初您会看到“站点部署正在进行中”。这意味着 Netlify 正在部署您的站点。对于未来的部署，您的站点在 Netlify 构建时仍然可用(这是 Netlify 如此出色的众多原因之一！)并且您可以从这个页面跟踪构建过程。

[![Netlify Dashboard with yellow indicators that the site is in the process of being deployed.](img/89ee1132fc9291e216f77282b54bf73e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o0YZSpC6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j4z8xbqpnssnilvf775r.PNG)

一两分钟后，刷新您的页面，您会看到“站点部署正在进行中”消息已经变成了一个 URL。如果你点击/导航到这个网址，你会看到你的网站是活的，并向世界开放！

# 应用自定义域

虽然[激动人心-凯勒-fb140b](https://agitated-keller-fb140b.netlify.com/) 很酷，但我真的很想把它更新到我的自定义域- [reallybad.design](https://reallybad.design) ！

在应用自定义域之前，我们必须确保我们拥有该域。如果有人能把 google.com 路由到他们的个人网站，想象一下会有多混乱！

## 从 Hover 购买域名

悬停使寻找和购买域名的过程变得容易！从 [Hover 的主页](https://www.hover.com/)，在搜索框中输入你想要的名字，它会向你显示可用的域名(或类似的域名)以及它们的价格。

[![Hover.com search for "reallyBad" and a list of available domain names.](img/48186f9a3d933346914ca0627f7628f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0q7IfYjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dk7lo2z8vs36so86dftm.PNG)

一旦找到您喜欢的，点击 **+** 图标将其添加到您的购物车，然后点击右侧的**进入购物车**(手机标题中的购物车图标)。从这里，添加您的付款信息，并继续通过结帐。

[![Hover.com search for "reallyBad" and a list of available domain names. A cart summary is shown on the right with a "Proceed to cart" button.](img/b6411615944e375d22c4c330e6f4c534.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--22NYDZFy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t2d5ms1oo0erqj88en7g.PNG)

恭喜你。你刚买了你的域名！🎉

## 连接网络生活

一旦购买，前往[www.hover.com/control_panel/domains](https://www.hover.com/control_panel/domains)，点击**你的域名**。这将带您进入以下类似仪表板的页面:

[![Hover.com dashboard. Key points include tabs for settings pages, the annual renewal price, and easy connection options for select sites such as Etsy or Squarespace.](img/e48d3c11907133c50c7e729ed4ab5d24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kbf9iZhA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h5yf6yh26y5jp13w4wgc.PNG)

您将点击 **DNS 选项卡**，这将指向一个记录列表。

[![DNS listings for reallybad.design. Two A records, an MX record, and a CNAME record exist, all added by Hover upon domain purchase.](img/0915eb398399475dd256b1b7c6069310.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MKzP6Ft_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fz0z0o072q4bwysg1uwd.PNG)

> *可选步骤:*您可以删除 2 条默认的“A”记录(类型 A，值 64.98.145.30)。我选择这样做是为了让列表不那么混乱，但是如果你决定保留它们，应该没有任何区别。

现在回到 Netlify，你将在“开始”卡下点击**建立一个自定义域**。这将把你带到下面的页面，在那里你将输入你的新域名(对我来说，`reallybad.design`)。输入后点击**验证**，当提示验证您是否拥有该域名时，点击**是，添加域名**。

[![Netlify add custom domain wizard, step 1\. A textbox for the domain name and a "Verify" button are shown. "Reallybad.design" is typed in the box.](img/9eb16fb5e1a8f31702cd7fc497158bb9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yMuzSiZv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ze21wjqnwop7kxhpavle.PNG)

这将把您重定向到 Netlify 中的域设置。你会注意到在列表的顶部有关于“检查 DNS 配置”的警告。点击其中一个(我从非`www`开始)。

[![List of custom domains configured for the site. Shown are a Netlify default domain, reallyBad.design, and www.reallyBad.design. The really bad design sites have a warning to check DNS configuration.](img/e1b5462b03eb5eced46dd46d179d9f46.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nBulV2SM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l29igv3ajf2c6k75977n.PNG)

Hover(目前)不支持名称或别名记录，所以我们将使用替代选项，将 A 记录指向列出的 IP 地址。在悬停域列表中，点击**添加一条记录**，输入`*`作为主机名，输入 Netlify 提供的 IP 作为值。重复使用`@`作为主机名。

真糟糕:
[![Creating a DNS record on Hover. Type is A, Hostname is *, IP address is 104.198.14.52, and Time to Live (TTL) is Default 15 minutes.](img/eb5b11aecc04abf6995c3efe5a465882.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3CEIdpAr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/un56w55dac5mq9ufz0by.PNG)

[www . reallybad . design:](http://www.reallybad.design:)
[![Creating a DNS record on Hover for www subdomain. Type is CNAME, Hostname is WWW, Target name is a default Netlify subdomain, and Time to Live (TTL) is Default 15 minutes. ](img/bb2b2309a9524eaf4d54f4b56c09f731.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ImWpBwdd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g3g3apglxi7vrd0ux54u.PNG)

*注意:生存时间(TTL)是您的更改生效所需的时间。此外，传播 www 地址可能需要 24 小时。*

## 配置 HTTPS

一旦您不再看到“检查 DNS 配置”错误(可能需要刷新和耐心)，滚动到域管理页面的底部，您将看到“HTTPS”的一部分，最初看起来像这样:

[![HTTPS section of Netlify's Custom Domain settings page. The status is "Waiting on DNS propagation" and buttons are available to verify DNS configuration or Provide your own certificate.](img/2ba2c76ff84e2ac5f8911a71cd9d0394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IPT1WDdo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d394rl74cc3q30uwmpup.PNG)

Netlify 最酷的地方在于，您不需要做任何事情来配置它(除非您想添加自己的自定义 SSL 证书)！可能需要一天的时间才能看完 HTTPS 的表演，但是设置好之后应该是这样的:

[![SSL/TLS Certificate showing under HTTPS section of Netlify's Custom Domain settings page. Details about the certificate are shown including when it was created, when it was updated, and when it auto-renews before.](img/579cfa06bdbad5c0de8e6033cdb0f24b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZUifaewf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ylqu30mtc0005sp1v6zr.PNG)

## 现场直播！

在这一点上，你的主要网站是设置和生活- HTTPS 和所有！

悬停: [![List of DNS records on Hover's DNS listing tab. Records include the default Hover MX and CNAME records, a CNAME record for www, and two A records for * and @.](img/fca5b0329040f802cd18e515f264273f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hCxHMyj7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9jrjn0z5dl0xknoku0m.PNG)

Site: [reallybad.design](https://reallybad.design)

# 部署到子域

所以现在我们已经部署了`reallybad.design`和`www.reallybad.design`，但是如果我们想要部署`really.reallybad.design`呢？这就是所谓的子域——让我们来完成这个过程！

## 什么是“子域”？

我不会在这里做太多，但是从高层次来说，子域是`othersite.yourwebsite.com`。如果你想更多地了解他们，我推荐这些很棒的帖子:

[![joshhadik](img/c1ba8c2096bb2041a61b5c564a9f7f1e.png)](/joshhadik) [## 用一个域来组织你的副业项目！

### 乔希·哈迪克 2 月 5 日 193 分钟阅读

#devtips #webdev #domains](/joshhadik/true-your-side-projects-with-a-single-domain-3716)[![flaque](img/a4e9661280171230049cb16977d24d60.png)](/flaque) [## 什么等等为什么:赫罗库不想让你裸体

### 埃文 11 月 19 日 184 分钟阅读

#webdev #devops #learning #meta](/flaque/what-wait-why-heroku-doesnt-want-you-to-be-naked-1j0h)

## 部署新站点

首先，我们将设置一个新的 Github 库，并遵循上面的**部署到 Netlify** 步骤，一旦点击**应用自定义域**部分，就跳回到这里。

## 点子域到新站点

此时，您应该在 Netlify 仪表板上，它看起来应该类似于这样:
[![Netlify dashboard for a new site that has just been deployed from a Github Repository.](img/81473220c5391b5218f41f7b595a043c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2bGoGENC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ftjw9fkhv7wn4c846xfg.PNG)

我们将再次点击**设置自定义域名**链接。在新页面上，您将键入您想要设置的子域。对我来说，那将是`really.reallybad.design`。

[![Netlify add custom domain wizard, step 1\. A textbox for the domain name and a "Verify" button are shown. "really.reallybad.design" is typed in the box.](img/8a612215350b3c033d5732ba4b774a84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CUMfBx9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fek0ydbmx4v78sku8lc3.PNG)

然后点击**验证**和**是，添加域名**一旦确认你拥有你的域名的消息出现。

**就是这样！**您的网站现已在您指定的子域上线！

## HTTPS 为子域

此时，您可能会看到这条消息，提示您没有为 HTTPS 进行设置:

[![The HTTPS section of Netlify's Custom Domain Settings page with an error that "We could not provision a Let's Encrypt certificate for your custom domain."](img/376ab9ad80a433e0d4595194aec51e9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_g_t81HK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uv967ypj4hjlsu9ipymd.PNG)

在后台，Netlify 正在为您处理这个问题，可能需要几分钟的处理时间，但是不需要您进行任何额外的输入。

如果大约 30 分钟后您仍然看到此错误，您可以点击通过**验证 DNS 配置**提示。如果一天左右后错误仍然存在，我建议联系 Netlify 支持部门。过去，我不得不解决一个证书问题，他们让事情变得非常容易，而且非常友好！

# 关闭

感谢阅读 Hover + Netlify 部署网站！如果你用它来部署什么东西，请告诉我——我很乐意看到！

另外，reallybad.design 和 really.reallybad.design 都是开源的，所以可以随意帮忙把网站变得更差(更好？)!

真正糟糕的设计:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [太字节](https://github.com/TerabyteTiger) / [真烂设计](https://github.com/TerabyteTiger/really-bad-designs)

### 演示如何将悬停域名部署到 Netlify 的网站。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 非常糟糕的设计

演示如何将悬停域名部署到 Netlify 的网站。

</article>

[View on GitHub](https://github.com/TerabyteTiger/really-bad-designs)

非常非常糟糕的设计:

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[非常非常糟糕的设计](https://github.com/TerabyteTiger/really-really-bad-designs)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 非常非常糟糕的设计

一个网站，展示部署一个悬停域名 Netlify。

</article>

[View on GitHub](https://github.com/TerabyteTiger/really-really-bad-designs)