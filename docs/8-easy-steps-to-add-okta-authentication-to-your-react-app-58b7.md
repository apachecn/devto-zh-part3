# 将 Okta 认证添加到 React 应用程序的 8 个简单步骤

> 原文：<https://dev.to/rleija_/8-easy-steps-to-add-okta-authentication-to-your-react-app-58b7>

*原帖@ [Linguine 博客](https://linguinecode.com/post/add-okta-authentication-to-react-app)T3】*

自 2017 年以来，Okta 的年同比增长率超过 45%，而且似乎没有放缓的趋势。

大大小小的公司都在使用 Okta 作为他们的外部和内部软件工具。

Okta 是一个用户认证管理工具，帮助企业用他们公司的软件工具管理他们员工的软件账户。

例如，假设我拥有一家猫店，我使用 Slack、吉拉和 Github。

如果我雇佣了一名工程师，我必须为该工程师创建多个账户。

如果那个工程师离开了，我将不得不检查每个工具并删除那个人的帐户。这是一个问题。

您可以将所有这些软件工具集成到一个 Okta 帐户中，并为一个 Okta 用户分配一个软件工具，而不是做所有的手工工作。

这意味着你可以为你的 Okta 帐户创建一个 Okta 用户，Okta 将为你集成的所有软件工具添加和删除帐户。

Okta 的巧妙之处在于，您可以将它实现到自己的内部应用程序中。

在本文中，我将通过 8 个简单的步骤向 React 应用程序添加 Okta 身份验证。

## 第一步:创建 Okta 账户

第一步是前往[okta.com](http://okta.com)创建一个账户。

您可以创建一个 30 天的免费试用帐户进行测试。但是，如果您已经有一个，请转到步骤 2。

<figure>

[![](img/fdc28357a86bcdb1bba846693d66ec56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y99nes9C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02181543/creating-okta-account-1024x914.png)

<figcaption></figcaption>

</figure>

这很简单。

然后，您将收到一封电子邮件，其中包含您的登录凭据以及登录页面的链接。

继续登录，并设置您的新密码。

## 第二步:创建 Okta app

登录后，您需要前往应用程序页面。

你可以在应用菜单标签下找到它。

[![](img/62647e56da74472025bbf597911406af.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FOmyVyUw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02182431/okta-application-menu-1024x326.png)

右侧还有一个添加新应用程序的快捷链接。

但是，如果您在应用程序页面，然后点击**添加应用程序**下一步。

然后点击**创建新应用**。应该是左手边的绿色按钮。

[![](img/664524cfd6fd479730cd045e0f3810b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--daTX6IK1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02182759/okta-create-app-bttn-1024x412.png)

将出现一个弹出窗口，您需要添加以下配置来适应您的 React 应用程序。

[![](img/c6f9a126cff7e12aca663c6968306a6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dLZAXKOo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02182943/okta-app-platform-type-1024x427.png)

然后会提示您添加一些关于您的应用程序的基本设置配置。

[![](img/8dbb2ecd00653479dc34359c20e9fc83.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_PrNsU2J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02183336/okta-app-general-settings-1024x875.png)

现在，您可以为您的测试应用程序添加当前的本地主机和端口号。

添加完成后，点击**保存**。

在 Okta 应用程序的底部页面，您可以找到您的客户 ID。

[![](img/f57a7e2a72988f615110045d43eeac2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_s852xyZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02183559/client-id-okta-1024x382.png)

点击右边的按钮将其复制到你的剪贴板。

## 第三步:分配用户到 Okta 应用

下一步是为您的 Okta 应用程序分配一个用户。

如果 Okta 用户没有被分配到该应用程序，他们将被拒绝。

[![](img/58f8254106a5b750b9f31d53c59f457b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pesAic_C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/04131158/okta-user-denied.jpg)

如果你想的话，也没关系。但至少要加你自己！

返回申请页面，点击**分配申请**按钮。

[![](img/2726aadb559db4423bef9b7f59a05fa2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MET9rBiC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/04131458/okta-assign-user-1024x558.jpg)

选择您的应用程序和您想要分配的用户，然后点击**下一步**。

然后点击确认按钮。

## 第四步:向 Okta 添加可信来源

你需要在 Okta 账户中做的最后一个配置是添加你的可信来源。

这将确保我们可以避免任何 CORS 问题。

[![](img/9ecfd20de122785593a1eea4747bf673.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zWNrAmQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02183948/okta-api-menu-tab-1024x623.png)

将鼠标悬停在**安全**菜单上，选择 **API** 子菜单项。

您将在页面中看到的第一个选项卡是关于令牌的，但我们希望将其更改为可信来源选项卡。

[![](img/b44969b5b2dabad47d32421243ecb18c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TB2vs6ke--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02184157/okta-trusted-origin-page-1024x399.png)

然后选择**添加原点**下一步。

将以下字段值添加到表单中，并点击**保存**。

[![](img/fca480eaad943ea8ed190629abd177b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tG-HovLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/02184341/okta-add-trusted-origin-1024x642.png)

无聊的东西已经完成了。现在编码开始了。

## 第五步:创建 React app

为了使本示例指南简单，我将使用 Create React App 工具来帮助我们更快地了解本指南的重要部分。

让我们创建一个名为 **okta-sample** 的目录。

在我们的新目录中，创建一个 **package.json** 文件，并添加以下内容。

[![](img/5c1fddbc0a6a2095b3e2004380203247.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--011g7CXD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03150516/okta-react-package-json-602x1024.png)

现在，您可以通过运行 <mark>npm install</mark> 来安装这些依赖项。

让我们在项目的根目录下创建一个名为 **public** 和 **src** 的目录。

在 **public** 目录中，让我们添加一个 index.html 文件**。**

[![](img/8b89108b3f6a94de9176886b6f595e02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FlAalueU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03151139/okta-react-html-1024x802.png)

index.html 文件将作为应用程序的基本框架。

在 **src** 目录中，您所有的 React 代码都将存放在那里。

在 **src** 里面，创建两个文件，分别叫做 **index.js** 和 **App.js** 。您还需要在 **src** 目录中添加一个 **pages** 目录。

[![](img/342f533a888a08ee25787c3f594cd1b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Z0q3w3e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03152109/okta-react-folder-structure.png)

继续运行 <mark>npm start</mark> 。它应该会启动一个指向 localhost:3000 的浏览器窗口。

网页应该是空白的。

## 第六步:创建索引文件

在这个文件中，我们在这里做了一些动作。

[![](img/38c9ca06cda81b4b8c4635a985c54242.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yMeRROY8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03153508/okta-index-file1-1024x958.png)

我们正在初始化浏览器路由器并添加 Okta 的安全组件。

它允许您将 Okta 配置提供给 React 应用程序。

*另外，不要像本例中那样硬编码生产配置。*

**issuer** 属性只是 Okta 页面的基本 URL。别忘了在最后加上斜线 <mark>**/**</mark> 。

可以从 Okta 申请页面获取**重定向 uri** 和**客户端 id** 。

如果您在此步骤中在本地运行应用程序，您可能会看到应用程序被破坏。

这是因为还没有为路线创建 **App.js** 。让我们接下来做那件事。

## 第七步:创建 App.js 文件

[![](img/bd3b37bb79c780141a5f9f49aa4b06a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M0AyAE30--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03211817/okta-react-app-1024x786.png)

在这个文件中，您将看到如何将这些片段组合在一起。

第一步是导入 React、React 路由器 DOM 模块 Route 和 Link。

Route 将帮助我们定义公共路线。

我们将使用 Link 来帮助我们构建一个简单的 2 菜单项导航。

第一个链接将指向主页页面，这是登录页面。

第二个链接将帮助您转到管理仪表板页面。

这个文件的另一个重要部分是 Okta 提供的 <mark>SecureRoute</mark> 组件。

该组件帮助您处理是否允许用户进入 pag 的逻辑。这是由用户是否被认证来决定的。

这个文件的最后一个重要部分是<mark>implicit callback</mark>Okta React 组件。

该组件帮助处理 Okta 告诉 React 用户是否成功登录后的响应。

## 步骤 8:创建管理仪表板&登录页面

管理仪表板将是 pages 目录中一个名为 **AdminDashboard.js** 的文件。

[![](img/aa6d144bf94ff39970dd7046cf97f16a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oGMlwWKL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03212616/okta-admin-dashboard.png)

这是一个非常简单明了的功能反应组件。

下一个要创建的文件是我们的主页。这将显示一个登录按钮。

[![](img/cb45d03479eee88bce3db9e84b6b9e55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SX9jcug9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/03212804/okta-login-page-react-1024x975.png)

在登录中导入 Okta 的 HOC(高阶组件)调用 <mark>withAuth</mark> 。

我将它包装在 React 类组件 <mark>LoginPage</mark> 周围。

这将是必要的，因为没有提供

<mark>withAuth</mark> 向名为用户 **auth** 的 React 组件提供新属性。

在上面的例子中，你可以看到我在按钮的 <mark>onClick</mark> 事件中使用了一个名为 login 的方法。

在 <mark>componentDidMount</mark> 的生命周期中，你可以看到我正在测试带有 Auth 的<mark>附带的其他一些功能，比如检查用户是否经过身份验证，以及获取用户信息。</mark>

<mark>withAuth</mark> 还提供其他功能，例如:

*   getIdToken
*   注销
*   getAccessToken
*   处理身份验证

## 结论

恭喜您已经将 Okta 认证添加到 React 应用程序中！

这是源代码的链接。如果你觉得它有趣和有帮助，请开始吧！

[Github 源码链接](https://github.com/rleija703/react-examples/tree/master/examples/with-okta)