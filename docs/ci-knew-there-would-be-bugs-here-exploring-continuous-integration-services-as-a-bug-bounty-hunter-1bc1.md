# “CI 知道这里会有 Bug”——作为 Bug 赏金猎人探索持续集成服务

> 原文：<https://dev.to/edoverflow/ci-knew-there-would-be-bugs-here-exploring-continuous-integration-services-as-a-bug-bounty-hunter-1bc1>

当谈到昆虫赏金猎人和寻找令人兴奋的探索领域时，熟悉供应商和公司所依赖的技术是至关重要的。引起我们注意的一个特别有趣的环境是各种开源项目使用的流行集成，主要作为它们开发生命周期的一部分。一些持续集成服务(“CI 服务”)的主要例子，包括 [Travis CI](https://travis-ci.org/) 、 [Circle CI](https://circleci.com/) 和 [GitLab CI](https://about.gitlab.com/product/continuous-integration/) ，对我们这些 bug 赏金猎人来说，是非常值得的。

[![image](img/a82c3713c5e499005024c6102a895d1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IkuNDaVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/56799241-d3987a80-6818-11e9-9f31-4696e56bac02.png)

我们开始从这些 CI 服务中自动获取和搜索大型数据集。这篇技术文章将涉及我们面临的众多挑战，我们如何减少搜索中的假阳性数量，值得注意的发现，最后，列出我们在这一过程中发现的一些技巧。

参与这项研究的团队包括贾斯汀·加德纳( [@Rhynorater](https://twitter.com/Rhynorater) )、科本·利奥([@黑客 _](https://twitter.com/hacker_) )和埃多弗流([@埃多弗流](https://twitter.com/EdOverflow))——卡里姆·拉哈尔([@卡里姆普恩兹](https://twitter.com/KarimPwnz))、[@斯特雷阿克](https://twitter.com/streaak)、 [@d0nutptr](https://twitter.com/d0nutptr) 和 BBAC 提供了一些测试和有用的建议。

## 持续集成服务简介

持续集成(CI)是提交代码变更并自动构建和测试每个变更的实践。如今，在开发周期的某个阶段，很少会遇到不使用持续集成服务的开源项目。各种服务提供了简单的设置配置步骤和漂亮的界面，用于快速测试和持续构建代码。

<figure>[![](img/44af4cc1cee2b1b0e0a1e610f3783ffd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WZkRvNHQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/56799279-e6ab4a80-6818-11e9-8933-6d010c0f09fb.png)

<figcaption>/r/dataisugly/候选人就在这里。</figcaption>

</figure>

例如， [security.txt 项目](https://github.com/securitytxt/security-txt)在提交时使用 Travis CI 构建新的草稿文档。这使得团队可以快速确定对规范的任何进一步更改是否可能在使用`kramdown-rfc2629`编译时破坏互联网草案——这是一种工具，使人们能够以 Markdown 编写所有内容，然后将其转换为 XML2RFC XML 标记。

## 我们脑子里都在想什么

读者过去要求的是有一个指定的部分，关于作者如何提出研究主题，比如我们在这里介绍的工作类型。这一节将有希望说明这个想法最初是从哪里产生的。

这篇文章的所有作者都有在 GitHub 上从事开源项目的丰富经验，并且在过去的几年中学习了简化开源项目维护者开发过程的技术。GitHub 在[https://github.com/marketplace](https://github.com/marketplace)提供了大量的集成，其中有一个特别的类别很突出:[【持续集成】](https://github.com/marketplace/category/continuous-integration)。这就是我们发现，由于许多开源团队在开发过程中努力实现完全透明和开放的方式，项目不愿意在持续集成平台上隐藏构建日志数据。诚然，像 Travis CI 这样的集成确实提供了私有配置文件作为 travis-ci.com 的高级功能，但在我们的研究中，绝大多数项目似乎只使用了公共实例 travis-ci.org——请注意。org”顶级域名。

值得注意的是，持续集成服务在过去已经成为 bug 赏金猎人和第三方获取敏感信息的目标，如[“一名 HackerOne 员工的 GitHub 个人访问令牌在 Travis CI 构建日志中暴露”](https://hackerone.com/reports/215625)和[“API 受到攻击”](https://www.traviscistatus.com/incidents/3f8mwqxbh127) Travis CI 事件报告所示:

> “我们目前正在遭受针对我们公共 API 的分布式攻击，我们认为该攻击旨在泄露 GitHub 认证令牌。对策正在进行，我们将相应更新。”
> 
> —Travis CI(2015 年 9 月)

以至于 Travis CI 等平台引入了内置的秘密检测来防止敏感信息的意外泄露，如下所示。[【1】](https://docs.travis-ci.com/user/best-practices-security/)

[![image](img/28403f7d15880ba355c79f061be25fe6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fDoDTTYo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/56799298-f32fa300-6818-11e9-88db-274d183cd1be.png)

Travis CI 在运行时用关键字`[secure]`替换潜在的敏感信息。

> 为了防止这些组件泄漏，我们在运行时自动过滤安全环境变量和超过三个字符的令牌，有效地从构建日志中删除它们，显示字符串`[secure]`。

## 将枯燥的任务自动化

手动接近大型攻击面，例如 Travis CI 展示的攻击面，将是一项极其乏味的任务。因此，我们必须使用这些 CI 供应商提供的可用 API 文档，并开发工具来快速自动获取构建日志。

为了更好地说明从 bug bounty 程序到用于进一步调查的大量数据集的过程，我们将使用 Travis CI 的 API 文档作为例子。

我们过程的初始阶段是获取 bug bounty 程序的 GitHub 组织。有多种方法可以做到这一点，但为了获得最佳结果，简单的谷歌搜索“公司名称”和“GitHub”就可以了。接下来，我们必须检查 GitHub 句柄是否在 Travis CI 上。为了使这个过程更加顺畅，我们使用了一个浏览器书签小程序，它会使用 GitHub 句柄将我们从 GitHub 重定向到 Travis CI。

```
javascript:window.location="https://travis-ci.org"+window.location.pathname; 
```

<figure>[![](img/80a274d5a17423dbdc8f34c3603aca22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lo87LCVO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/56799313-fd51a180-6818-11e9-9518-4b06e81de2e9.png)

<figcaption>bookmarklet 将从 GitHub 页面重定向到 Travis CI。</figcaption>

</figure>

如果目标出现在 GitHub 上，我们点击 Travis CI 上的项目 API 端点并检索所有项目的列表。

```
https://api.travis-ci.org/owner/%s/repos?limit=100&offset=%d 
```

Travis CI 的 API 区分大小写；幸运的是，bookmarklet 确保您在发出 API 请求时使用正确的句柄。为了收集构建日志的内容，我们需要点击 build IDs API 端点，然后点击`/log.txt`。

```
https://api.travis-ci.org/repo/%s/builds?limit=100&offset=%d
https://api.travis-ci.org/job/%d/log.txt 
```

现在，属于目标的所有构建日志的内容都存储在本地，我们可以开始 grepping 了。由于我们正在分析的数据的规模，当在本地筛选日志时，我们求助于 [`ripgrep`](https://github.com/BurntSushi/ripgrep) 。

```
$ rg -ia "$1" -j 12 --no-filename --no-line-number --pretty 
```

除了 bug bounty 程序的 GitHub 账户，作者还收集了属于 GitHub 组织所有成员的构建日志。原来一些成员在他们的帐户上运行构建，没有意识到他们的秘密在构建日志中暴露了。

```
#!/bin/bash

users=$(curl -s -H "application/vnd.github.hellcat-preview+json" -H "Authorization: token $GH_TOKEN" https://api.github.com/orgs/"$1"/members | jq -r .[].login);

while read -r hehe; do 
    secretz -t "$hehe"; 
done <<< "$users" 
```

这个项目背后的团队已经决定不发布任何用于获取构建日志的工具，因为我们不想对 CI 平台性能的任何中断直接负责。这篇文章将不可避免地将更多的注意力吸引到一些 CI 平台存在的大的攻击面上；因此，作者想提醒读者，当使用平台的 API 时，请注意不要一次用大量请求淹没每个端点。我们非常谨慎，在整个过程中不关闭任何服务，并建议其他人也这样做。

## 结果和显著的发现

总体而言，最有影响力的发现主要是 GitHub 访问令牌泄漏。在这一节中，我们将介绍我们团队提交的四个值得注意的报告。

在查看 Travis CI 公共程序上一个员工帐户的构建日志时，我们发现了一个 GitHub 访问令牌，可以读写 GitHub 组织。这个令牌将允许我们将代码推送到 GitHub 组织下列出的任何库。根据他们的黑客“程序统计”，该程序授予我们迄今为止最高的支出。

为了扩大我们的范围，我们考虑了多种平台。在 2013 年一个私人 Bugcrowd 程序的 Travis CI 构建日志中，我们发现了一个 GitHub 访问令牌，并获得了 P1 严重性赔付——bug crowd 上最高的严重性分数。[【2】](https://bugcrowd.com/vulnerability-rating-taxonomy)

在我们报告发现的所有供应商中，最令人惊讶的反应是 Discourse 的 bug bounty 计划。Karim Rahal 发现了一名员工的 GitHub 访问令牌，该令牌可以读写 Discourse GitHub 组织下的所有公共存储库。为了展示这个问题的潜在影响，我们将一个无害的文件推送到该组织最不活跃的存储库中，以避免引起太多的注意。该文件随后被从存储库中删除[。](https://github.com/discourse/errorpages/commit/09dc71dc6e20b192a54d9ede0e3ff2c08aa3e400)

[![image](img/aa100d7cf96d36188fb63046c898ab3a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xra_XBSr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/56799351-15c1bc00-6819-11e9-8cdb-70e1e119f2c9.png)

话语给了卡里姆最低的赏金 128 美元。我们要求进一步澄清该小组是如何确定奖金数额的，但我们还没有收到对话小组的回复——已经 60 天没有回复了。[【3】](https://hackerone.com/reports/497669)

另一个严重的错误是在 HackerOne 上的一个公共加密货币程序中发现的。这个程序使用 Travis CI 中的秘密变量来创建 SSH 密钥。这种配置的细节可以在[这里](https://github.com/dwyl/learn-travis/blob/master/encrypted-ssh-keys-deployment.md#5-encrypt-the-private-key)和[这里](https://github.com/nelsonic/hello-world-node-http-server/blob/master/.travis.yml#L7-L16)找到。在挖掘了成千上万的日志后，贾斯汀·加德纳写的工具发现了下面一行:

```
-----BEGIN RSA PRIVATE KEY----- 
```

一名开发人员在他们的 CI 配置文件中添加了`cat deploy_key`,该文件在日志中输出 SSH 密钥。使用 SSH 密钥，攻击者可以登录到程序基础结构中的几个部署服务器。因为服务器是非生产性的，所以奖励了 1000 美元。

## 提示和窍门

通常，对构建日志中的`export`语句进行简单的 grep 是一个很好的起点。`export`命令用于在日志提示符中设置环境变量，因此可能会暴露敏感信息。

```
$ rg -ia "export " -j 12 --no-filename --no-line-number --pretty 
```

当然，人们不应该仅仅局限于使用`export`命令设置的变量；将搜索词细化为“令牌”、“密钥”、“密码”和“秘密”有助于发现具体的漏洞。为了减少误报的数量，我们建议在你的搜索词后面加上`=`和`:`。

我们鼓励读者用关键字`[secure]`创建一个所有变量的列表，然后在所有项目中使用这些变量名进行搜索。这将帮助您使用常见的变量命名约定找到敏感数据的不安全实例。Karim Rahal 从 5，302，677 个构建日志中收集了`[secure]`变量，其中最常见的 50 个如下所示。

<figure>[![](img/728b273dc40a2181a1f9496dbb3ec505.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VDuP4nUa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/56803383-c4b6c580-6822-11e9-9807-6ee57563024c.png) 

<figcaption>完整列表可在[这里找到](https://gist.githubusercontent.com/EdOverflow/8bd2faad513626c413b8fc6e9d955669/raw/06a0ef0fd83920d513c65767aae258ecf8382bdf/gistfile1.txt)。</figcaption>

</figure>

此外，设置对您最喜欢的 bug bounty 程序的 CI 构建的持续监控，并在团队每次向 GitHub 提交新的提交时运行您的工具，这是在团队有时间采取行动之前实时捕捉暴露的秘密的好方法。

不要把自己局限于密钥和令牌；竞争情报平台也是侦察的重要信息来源。筛选日志以找到属于目标的隐藏端点和 URL。

检查 GitHub 上的 CI 配置文件，以确定您的目标正在使用什么 CI 集成。可能还有其他 CI 平台没有在这篇揭露秘密的文章中涉及。

我们在 grep 流程中包含的一个有趣的任务是找到通常与缺失或损坏的依赖项相关的字符串和错误消息。由于缺少 npm 软件包，这有时会导致通过在远程注册表上声明软件包名称来执行代码，如[https://hackerone.com/reports/399166](https://hackerone.com/reports/399166)所示。一些错误消息示例包括:

*   不在 npm 注册表中( [npm](https://www.npmjs.com/) )
*   “没有匹配的分布”( [PyPI](https://pypi.org/) )
*   “找不到有效的宝石”( [RubyGems](https://rubygems.org/) )

## 结论和进一步研究

这项研究帮助我们更好地理解了持续集成服务所呈现的巨大攻击面——几乎隐藏在众目睽睽之下——并且在寻找 bug 赏金时证明是非常有成效的。

由于本次研究中包括的平台数量相当有限，因此未来的研究和项目可以考虑覆盖更多的 CI 平台和集成。

我们赞扬像 Travis CI 这样的平台，它们允许用户在日志中隐藏敏感的环境变量。在我们看来，这是朝着防止我们遇到的安全漏洞的正确方向迈出的一步。

这项工作不仅为我们提供了许多成功的昆虫赏金故事和有效的发现，而且也表明了合作，正如这个项目中所看到的，在昆虫赏金狩猎中可以走很长的路。■

* * *

[![](img/51fe031f4ec0984b3b5291dd63f30cad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9KuxpUjs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-images.githubusercontent.com/18099289/38743788-4217a52e-3f40-11e8-9880-6c0ef4c8e4e7.png)

如果你喜欢阅读我的文章，并愿意支持我的工作，请查看我的“给我买杯咖啡”页面。

<center> [# 给我买杯咖啡](https://www.buymeacoffee.com/edoverflow) </center>