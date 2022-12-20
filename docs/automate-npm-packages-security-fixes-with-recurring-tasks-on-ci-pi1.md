# 通过 CI 上的重复任务自动执行 NPM 软件包安全修复

> 原文：<https://dev.to/alex_barashkov/automate-npm-packages-security-fixes-with-recurring-tasks-on-ci-pi1>

在 Node.js 上开发时，我们的[团队](https://pixelpoint.io/)使用了很多开源的 NPM 包。它们中的每一种都有自己的优点和缺点。在本文中，我们将讨论:

*   漏洞测试的免费选项 NPM 依赖项
*   用于运行定期检查的无人机 CI 配置
*   使用固定程序包自动创建拉式请求

### NPM 审计与更

当我们谈论漏洞审计时，首先想到的是 NPM 审计工具。该工具使用公开可用的漏洞目录来检查您的项目，并建议库版本更新以修复发现的任何问题。你可以在 NPM 官方博客阅读更多内容。

另一个仍然使用现成可用选项的好的免费报告是 **npm 过时的**。该报告使用命令检查注册表来查看当前是否有任何已安装的软件包过期。这些信息不一定对日常工作有用，但从长远来看是有好处的，所以你不会轻易放弃一个项目。

```
$ npm outdated
Package      Current   Wanted   Latest  Location
glob          5.0.15   5.0.15    6.0.1  test-outdated-output
nothingness    0.0.3      git      git  test-outdated-output
npm            3.5.1    3.5.2    3.5.1  test-outdated-output
local-dev      0.0.3   linked   linked  test-outdated-output
once           1.3.2    1.3.3    1.3.3  test-outdated-output 
```

Enter fullscreen mode Exit fullscreen mode

### 自动生成 npm 过期报告

这些工具非常有用，但是，当然，自动化报告更好。为此，我们使用 [Drone CI](https://drone.io/) (免费开源)和新特性 Cron Jobs 来设置重复任务。但是，您可以自由使用您喜欢的任何其他配置项，它们可能会支持相同的功能。对于那些不熟悉无人机 CI 的人，请在这里阅读我的[入门文章](https://dev.to/alex_barashkov/getting-started-with-open-source-drone-ci-4pgc)。

由于 Drone CI 支持多个管道，每个报告都有自己的管道，不会影响主管道。从更广的角度来看，请看这里的例子。同时，让我们从 npm 过时开始。

```
kind: pipeline
name: npm outdated

steps:
- name: outdated
  image: node:10-alpine
  commands:
    - npm outdated

- name: slack_notification
  image: plugins/slack
  settings:
    webhook: https://hooks.slack.com/services/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
    template: >
      NPN detected outdated packages at *{{repo.name}}* for *{{build.branch}}* branch. 
      Report available by the link {{build.link}}
  when:
    status:
    - failure

trigger:
  cron: [ weekly ] 
```

Enter fullscreen mode Exit fullscreen mode

我们认为 yaml 语法本身就很好。在第一步中，我们使用 node:10-alpine 作为基础映像，并运行 npm outdated。在第二步中，仅当有内容要更新时，才会执行 Slack 通知(npm 已过期，并带有错误退出代码)。要获取 Slack webhook URL，请访问此[页面](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks?next_id=0)

在最新的代码行中，整个管道是由标记为“过时”的 Cron 作业触发的。对于我们的项目，我们将该任务设置为每周执行，因为我们不打算在每次新版本发布时更新包。

要在无人机中定义任务，进入项目->设置。

[![](img/667d9362f517ccdc2c3b961ac3948b9c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rqQlh6HD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hf3icfj8rdh56sgkruoo.png)

通过此界面，您可以选择作业的名称(用于管道过滤)、分支和间隔，可以是每小时、每天、每周、每月或每年。

[![](img/384c6cc50b86213fa6feef93a0a00fc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8_soy9Ru--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ec3bp3kmrrwmx5bu5tve.png)

### 自动 npm 审计和修复 PR 创建

npm audit 命令将检查您的应用程序是否存在漏洞，并根据需要将软件包更新为任何版本的当前版本。这条管道与前一条非常相似，但是有一个额外的步骤涉及公关创建。

```
kind: pipeline
name: npm audit

steps:
- name: audit
  image: node:10-alpine
  commands:
    - set -o pipefail && npm audit --force 2>&1 | tee audit.log

- name: audit fix
  image: node:10-alpine
  commands:
    - npm audit fix
  when:
    status:
    - failure

- name: create_fix_pr
  image: lnikell/github-hub:2.11.2
  environment:
    GITHUB_TOKEN:
      from_secret: github_token
  commands:
    - git config --global user.email "email@example.com"
    - git config --global user.name "example"
    - git checkout -b drone/npm-audit-fix-${DRONE_BUILD_NUMBER}
    - git add package.json package-lock.json
    - git commit -m 'npm audit fix'
    - git push origin drone/npm-audit-fix-${DRONE_BUILD_NUMBER}
    - hub pull-request -m "[Security] NPM Audit Fix" -m "$(cat audit.log | tail -2)" -m "${DRONE_BUILD_LINK}"
  when:
    status:
    - failure

- name: slack_notification
  image: plugins/slack
  settings:
    webhook: https://hooks.slack.com/services/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
    template: >
      NPN detected vulnerable packages at *{{repo.name}}* for *{{build.branch}}* branch. 
      Report available by the link {{build.link}}
  when:
    status:
    - failure 
```

Enter fullscreen mode Exit fullscreen mode

在第一步中，我们使用相同的节点:10-alpine 映像并运行 NPM 审计。我们还保存了一个包含结果的 audit.log 文件，以便以后输出到 PR。如果在 npm 审核期间发现易受攻击的程序包，下一步将会失败，触发 nmp 审核修复过程并创建拉请求。

```
-name: audit fix
 image: node:10-alpine
 commands:
   - npm audit fix
 when:
   status:
   - failure 
```

Enter fullscreen mode Exit fullscreen mode

为了创建一个 pull 请求，我们使用 hub——处理 Github API 的命令行工具。我们需要生成一个 Github 个人令牌来使用它进行 API 调用。转到此页面并创建一个新页面:[https://github.com/settings/tokens](https://github.com/settings/tokens)

[![](img/6026a3f555b8fbd3cab68bd5d49f1c19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KI_3kqMK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/el4882i4ecq1uxs6lh5i.png)

选择“repo”权限范围，然后将您生成的令牌添加到 Drone 中的 secrets，名称为“github_token”。

[![](img/89175109f7c2dd50d4b820605c24685c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LvwbLlbN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kxi2csgitp97mb88bmnt.png)

这在下面的步骤中用作环境变量。

```
- name: create_fix_pr
 image: lnikell/github-hub:2.11.2
 environment:
   GITHUB_TOKEN:
     from_secret: github_token
 commands:
   - git config --global user.email "lnikell@gmail.com"
   - git config --global user.name "drone"
   - git checkout -b drone/npm-audit-fix-${DRONE_BUILD_NUMBER}
   - git add package.json package-lock.json
   - git commit -m 'npm audit fix'
   - git push origin drone/npm-audit-fix-${DRONE_BUILD_NUMBER}
   - hub pull-request -m "[Security] NPM Audit Fix" -m "$(cat audit.log | tail -2)" -m "${DRONE_BUILD_LINK}"
 when:
   status:
   - failure 
```

Enter fullscreen mode Exit fullscreen mode

在这一步中，我们声明分支创建的模式，并用 audit.log 中的最后两行创建一个 pull 请求。

[![](img/f525114290f49c3066c0d6202a2130d5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VVkT-ywF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d0efbo8r24jgxvji68l3.png)

最后，我们需要看看管道的触发器部分。因为您只想将这些检查作为 Cron 作业的一部分来执行，所以您需要添加以下内容:

```
trigger:
 cron: [ name_of_the_job ] 
```

Enter fullscreen mode Exit fullscreen mode

然而，请记住，您仍然需要考虑您的主要渠道。为了防止它在 Cron 任务期间运行，您必须像这样使用 exclude 选项:

```
trigger:
 cron:
   exclude: [ name_of_the_job ] 
```

Enter fullscreen mode Exit fullscreen mode

在这里 看一个例子，给你一个所有管道 **[的有用概述。](https://gist.github.com/lnikell/b7caff94900bddf1a71f9e4543ecc787)**

### 结论

这只是一个例子，说明 CI 上的重复任务对于构建、测试和修复是多么有用。你只需要设置一次，每天/每周你都会得到项目安全性的通知。我们在示例中使用的方法应该很容易适用于 Travis CI 或 Gitlab 如果你这样做，请在这里分享你的管道。

如果你喜欢这篇文章，可以订阅我的 [Twitter](https://twitter.com/alex_barashkov) 或者 [DEV。到](https://dev.to/alex_barashkov)页面。