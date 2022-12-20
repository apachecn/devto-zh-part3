# 翻新符合位桶管道

> 原文：<https://dev.to/danielbayerlein/renovate-meets-bitbucket-pipelines-4ohk>

[![](img/515c2932661cc4a56ac8996ec4e6740a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6yrzsA0p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ii4j42xj46le1xh3eqjx.png)

## 翻新

[renewal](https://github.com/renovatebot/renovate)是一个开源工具，用于通过拉取请求自动更新依赖关系。它是[绿色守护者](https://greenkeeper.io/)的替代品，只能与 [GitHub](https://github.com/) 配合使用。

## Bitbucket 流水线

[Bitbucket Pipelines](https://bitbucket.org/product/features/pipelines) 为 [Bitbucket Cloud](https://bitbucket.org/product) 带来持续的集成和交付，使团队能够在 Bitbucket 中构建、测试和部署他们的代码。

## 但是 renewal 如何与 Bitbucket 管道一起工作呢？

我们首先需要一个带有`package.json` :
的新存储库

```
{
  "name": "renovate",
  "version": "1.0.0",
  "description": "Multi-language dependency update automation",
  "private": true,
  "scripts": {
    "start": "renovate"
  },
  "devDependencies": {
    "renovate": "^14.50.3"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 认证

建议创建一个单独的 Bitbucket 用户(如`renovate-bot`)进行更新。
之后[创建一个 app 密码](https://confluence.atlassian.com/bitbucket/app-passwords-828781300.html)并存储在`RENOVATE_PASSWORD` [环境变量](https://confluence.atlassian.com/bitbucket/variables-in-pipelines-794502608.html)中。

### 管道

在根目录下创建文件`bitbucket-pipelines.yml`，内容如下:

```
image: atlassian/default-image:2

pipelines:
  custom:
    renovate:
      - step:
        caches:
          - node
        script:
          - git config --global user.email "bot@renovateapp.com"
          - git config --global user.name "Renovate Bot"
          - npm install
          - npm start 
```

Enter fullscreen mode Exit fullscreen mode

使用`atlassian/default-image:2`图像，因为默认情况下它带有 Node.js 和 Git。

### 配置

翻新需要一个[配置](https://renovatebot.com/docs/self-hosted-configuration/)。在根目录中创建它。一个例子`config.js`看起来像这样:

```
module.exports = {
  platform: 'bitbucket',
  gitAuthor: 'Renovate Bot <bot@renovateapp.com>',
  username: 'renovate-bot',
  onboarding: true,
  onboardingConfig: {
    extends: ['config:base']
  },
  repositories: [
    'YOUR_USERNAME/YOUR_REPOSITORY',
    'YOUR_USERNAME/YOUR_REPOSITORY',
    'YOUR_USERNAME/YOUR_REPOSITORY'
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

非常重要的是`platform`和`username`键。`username`的值是来自步骤 1 的新用户。

### 试运行

现在可以从 Bitbucket Cloud 手动触发管道。

现在，在指定的存储库中创建了一个初始的拉请求。

### 预定管道

通过[计划管道](https://confluence.atlassian.com/bitbucket/scheduled-pipelines-933078702.html)功能，您可以在特定时间自动运行管道。