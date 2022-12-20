# 如何使用 CircleCI 将静态网站部署到 Github 页面

> 原文：<https://dev.to/andreagrandi/how-to-deploy-a-static-website-to-github-pages-using-circleci-2bfe>

自从我用静态页面生成器创建我的博客以来，我一直使用 TravisCI 来自动化页面构建和部署。我想学习新东西的愿望(我们在工作中使用 CircleCI，但我从未从头开始配置它)和最近关于
TravisCI [收购](https://blog.travis-ci.com/2019-01-23-travis-ci-joins-idera-inc)和员工[裁员](https://twitter.com/alicegoldfuss/status/1098604563664420865)的新闻，让我考虑转向不同的服务。

## Github 页

Github 上的每个帐户都可以使用一个特殊的存储库来发布静态页面。在我的例子中，由于我有`github.com/andreagrandi`，我的特殊存储库是
，命名为`github.com/andreagrandi.github.io`。一旦我在那里发布了我的页面，就可以从`https://andreagrandi.github.io`访问它们。

您将需要直接使用特殊存储库的**主**分支，而不是每个存储库都可用的 **gh-pages** 分支。

## 循环

CircleCI 是一个非常灵活和强大的持续集成工具，对于开源项目来说也是**免费的**。只要你的静态网站位于 Github 上的公共存储库，你就不用支付任何费用来使用它。就我而言，这个网站的源代码可以在 https://github.com/andreagrandi/andreagrandi.it[的](https://github.com/andreagrandi/andreagrandi.it)找到

### 配置

你可以在这个地址的[找到完整的配置。
你唯一找不到的价值就是 **GH_TOKEN** 。您需要在 Github 上生成这个令牌，地址是:](https://github.com/andreagrandi/andreagrandi.it/blob/master/.circleci/config.yml)[https://github.com/settings/tokens](https://github.com/settings/tokens)。给它一个漂亮的描述，比如“CircleCI 部署令牌”，选择 **repo** scope，最后点击 **Generate token** 按钮。这个令牌将被用来`git push...`
你的页面一旦建立。请记住对这个令牌**保密**，不要在任何地方发布。

在我的配置中，你可能会注意到我正在使用[Pelican](https://blog.getpelican.com/)static websites generator，但是除了一些变化，配置的结构应该非常相似，即使你使用 Jekill、Hugo 等...如何生成页面并不重要，**部署阶段将是相同的**。

### 部署脚本

您会注意到 CircleCI 配置中嵌入了一个完整的 bash 脚本。这个脚本配置 git，获取现有的`andreagrandi.github.io`存储库，
并将构建的页面与现有页面同步(这避免了创建包含所有页面的提交，因此它将只包含添加的内容)。提交完成后，脚本最终会将更改推送到存储库。

**请注意:**无论 CircleCI 设置如何，只有当我们将 push(或合并一个 pull 请求)发送到 **master** ( `if [ "${CIRCLE_BRANCH}" = "master" ]; then`)时，部署才会发生，并且只有当有新内容要提交时，它才会实际提交和推送页面(`if git commit -m "CircleCI build $CIRCLE_BUILD_NUM pushed to Github Pages" ; then`)。例如，如果我只是更新了 CircleCI 配置中的一些内容，而没有改变任何内容，那么这些页面将不会被再次部署。

## 结论

我对 CircleCI 的第一印象是它比 TravisCI 更快，这意味着我可以更快地发布我的内容。使用 Docker 容器作为基础映像的可能性非常强大，在更复杂的场景中，我们可以在我们的机器上本地复制建筑环境。如果您对如何改进我的构建脚本有任何建议，请在这里留下您的评论。

**注:**本帖最早出现在我的个人博客[https://www . Andrea grandi . it/2019/02/24/how-to-deploy-static-website-github-pages-circle ci/](https://www.andreagrandi.it/2019/02/24/how-to-deploy-static-website-github-pages-circleci/)