# 如何合并多个 GitHub 账户+转移提交历史

> 原文：<https://dev.to/scottmathson/how-to-merge-multiple-github-accounts-transfer-commit-history-4k45>

管理多个 GitHub 账户可能会令人沮丧。让我们通过将两个(或多个)GitHub 帐户合并为一个来改变这种情况。

多年来，我有多个 GitHub 用户账户，并且知道其他人也有。这通常是个人和工作项目分离的不同账户。最近我改变了这一点，合并成一个，我认为你也应该这样做。

你有两个或更多的个人推特账户吗？多个，*个人* Instagram 账号？也许你有，这并没有什么坏处！但是，为了使 git 工作流更易于管理，您应该考虑合并到一个 GitHub 帐户中。

下面是如何做到这一点！

**目录**:

*   合并两个(或多个)GitHub 帐户
    *   如何在 GitHub 上转移存储库所有者
    *   GitHub + Git 提交历史上的帐户电子邮件管理

## 合并两个(或多个)GitHub 账户

转移回购所有权、管理(添加/删除/更改)帐户电子邮件地址以及*最重要的是:*将任何提交历史记录保存/转移到配置文件的贡献图，这是一个相对快速的过程。

在整篇文章中，我将引用`scottmathson-auth0`用户帐户(“a”)，合并为`scottmathson`用户帐户(“b”)。

在我的用户账户合并过程中，我和 GitHub 的支持团队保持联系，他们很好沟通，我会记下他们分享的一些内容。再次感谢马特！

* * *

### 如何在 GitHub 上转移仓库所有者

GitHub 中没有自动的账户数据“导出”功能，所以你需要从手动转移账户到账户的存储库(repo)所有权开始。账户之间的回购转移包括所有问题、拉请求、分叉、星号，以及为 git 和 http*设置的自动重定向。

> “对于合并两个用户帐户，恐怕没有任何自动化的过程，所以您已经在正确的轨道上(手动)转移您的存储库。”- GH 支持

这只需要几个步骤。

在转移回购帐户到帐户的过程中，它自然有助于清理门户和删除任何停滞或未维护的存储库。现在是这么做的最佳时机。

*   从 Repo >设置页面>选项>滚动到底部，在“危险区域”内寻找红色的“转移”按钮:

[![GitHub repository Settings Options panel](img/13583c13eed318ee43f9ec1e03bcda02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mvnEN-hU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/github-repository-settings-options-panel.jpg)

[![GitHub repository Settings Danger Zone - How to Transfer a repo](img/31d14f459fd02a58dcd0cb4e668c323d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I1aeCSLA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/github-repo-danger-zone-how-to-transfer-repo-user.jpg)

*   填写必填字段，键入存储库的名称进行确认，并输入新所有者的 GH 用户名:

[![GitHub how to transfer a repository required form](img/c4860da8ae20349950610319ba428757.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M7euCXLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/github-transfer-repo-form-username-confirmation.jpg)

*   新所有者会收到一封带有授权令牌的电子邮件，该令牌将在 24 小时后过期。请务必检查您要转入的帐户的电子邮件，并按照链接完整地完成任何回购转让。

*尽管它会自动重定向，但建议您在传输完成后更新遥控器的原始 URL:`git remote set-url origin new_url`

如果你想转让一个组织拥有的回购协议，请参考 GitHub 的文档。

* * *

### GitHub 上的账户邮箱管理+ Git 提交历史

Git 提交历史、SAML 和其他项目与 GitHub 和远程本地设置中的电子邮件相关联。当将多个 GitHub 账户合并为一个账户时，这是一个必不可少的步骤，以确保您*保留任何提交历史* +将它添加到用户资料的贡献图中。

帐户电子邮件管理还确保您能够完全接受某些组织的邀请。

> “…实际上，一旦您完成数据传输，一旦在`scottmathson`上添加(并验证)了`scottmathson-auth0`上的电子邮件地址，您的提交将被重新归属。对于相关贡献，也将根据特定标准进行更新”- GH 支持

拉动式请求和问题属于这个“特定标准”灰色区域。一旦撰写这些提交的电子邮件地址被添加到您的帐户，大多数提交历史将会转移。除了丢失拉请求和发布提交历史的例外/警告之外，下面将详细介绍。

如何向您的 GitHub 帐户添加/验证一个(或多个)电子邮件地址:

*   从 GH 仪表板>点击您的个人资料照片下拉菜单>设置页面:

[![GitHub dashboard profile Settings dropdown](img/1de462b7eb71944bbd5bec9a6303589e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AvA-N88c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/github-account-dashboard-profile-settings-menu.jpg)

*   点击左侧边栏中的电子邮件>添加电子邮件地址字段>添加新电子邮件:

[![GitHub user account add new email address](img/da1b31d5339d35ecd873c0a5bb3b6ac0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jwtykmmi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/github-user-account-add-new-email-address.jpg)

*   在您的收件箱中查看您刚刚添加的电子邮件地址，然后单击包含的链接。你应该登陆你的 GitHub 仪表板，看到一个电子邮件验证确认横幅。

[![GitHub email address verification confirmation](img/96b470cb24f7b3d3b08832b0b6055a49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L8tHUhVP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://scottmathson.com/assets/img/blog/github-new-email-address-verification.jpg)

*关于 PRs 和问题的更多信息*:账户“a”`scottmathson-auth0`的 PRs 和问题内的任何捐款/承诺未转移。GitHub 支持部门对此的解释是，“……一旦账户被删除，将归属于我们的“幽灵”用户，尽管问题/PRs 本身不会受到影响。”。

一定要记住这一点！虽然您可以将任何未结 PRs/问题的受分配人添加到您要将数据转移到的帐户，但某些贡献不会转移。

您在本地 git 配置中经常使用的任何电子邮件都应该添加到您的 GitHub 帐户电子邮件设置中，以便将提交的内容重新归类并添加到 graph 中。

添加了新验证的电子邮件地址后，在从冻结帐户“a”`scottmathson-auth0`中删除任何与 SSO 和 SAML 相关的电子邮件后，您现在可以验证并接受组织邀请*。*

给 GitHub 一点时间在账户之间传播历史和贡献。

* * *

再说一次，管理多个 GitHub 用户帐户可能会令人沮丧，我个人已经将帐户合并为一个更有效的工作流程和投资组合。在开始使用 GitHub 后不久，为了保持我的用户名/电子邮件在不同平台上的一致性(在社交网站上使用 [@scottmathson](https://dev.to/about/) ),我彻底删除了一个 GitHub 账户，并创建了一个新账户。

回过头来看，我可以也应该简单地更改用户名并添加一个新的电子邮件来实现我追求的目标。我可以通过上述流程转移回购，而不是在本地下载/组织/重新进行，然后创建新的回购来源。显然，我有效地清除了第一个用户帐户的所有公共捐款和历史记录。

希望这篇文章有所帮助，如果有请转发。感谢阅读！

* * *

如果你有多个 GitHub 账户，并希望保持一切*不变*，而这篇文章不适合你，那么感谢你阅读这篇文章，看看这篇来自 freeCodeCamp 的文章，这篇文章展示了[如何用 SSH 密钥](https://medium.freecodecamp.org/manage-multiple-github-accounts-the-ssh-way-2dadc30ccaca)在一台机器上管理多个 GH 账户。

* * *

有什么问题吗？欢迎在 Twitter 上联系[@ Scott mathson](https://twitter.com/intent/user?screen_name=scottmathson)和/或从 GitHub: [阅读更多信息为什么我的贡献没有显示在个人资料图表上？](https://help.github.com/articles/why-are-my-contributions-not-showing-up-on-my-profile)