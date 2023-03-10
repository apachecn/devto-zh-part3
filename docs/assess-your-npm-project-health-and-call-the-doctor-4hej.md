# 评估你的 npm 项目健康状况，并打电话给医生！

> 原文：<https://dev.to/lirantal/assess-your-npm-project-health-and-call-the-doctor-4hej>

## 提示 4:评估 npm 项目健康状况(来自 [10 项 npm 安全最佳实践](https://snyk.io/blog/ten-npm-security-best-practices/)

### 过时的依赖

如果没有审查发行说明、代码变更，并且通常以全面的方式测试新的升级，匆忙地不断地将依赖项升级到最新版本不一定是好的做法。

也就是说，过时而不升级，或者过了很长时间也不升级，也是麻烦的来源。

npm CLI 可以提供您使用的依赖关系的语义版本偏移量的新鲜度信息。通过运行`npm outdated`，您可以看到哪些包是过期的:

```
$ npm outdated 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/c015573d99d4aad63c14600180a7c5ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--siLk1DtR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/snyk/image/upload/v1550482184/blog/npm-10-security-best-practices-CLI.png)

黄色的依赖项对应于 package.json 清单中指定的语义版本，红色的依赖项表示有可用的更新。此外，输出还显示了每个依赖项的最新版本。

### 叫医生

在各种 Node.js 包管理器之间，以及在您的路径中可能已经安装的 Node.js 的不同版本之间，您如何验证健康的 npm 安装和工作环境？

无论您是在开发环境中还是在配置项中使用 npm CLI，评估一切是否按预期运行都很重要。

叫医生来！npm CLI 包含一个运行状况评估工具，用于诊断您的环境，以实现良好的 npm 交互。运行`npm doctor`查看您的 npm 设置:

```
$ npm doctor 
```

Enter fullscreen mode Exit fullscreen mode

*   检查是否可以访问官方 npm 注册表，并显示当前配置的注册表。
*   检查 Git 是否可用。
*   查看已安装的 npm 和 Node.js 版本。
*   对本地和全局`node_modules`等各种文件夹以及用于包缓存的文件夹运行权限检查。
*   检查本地 npm 模块缓存的校验和是否正确。

-

我还在博客中介绍了一个完整的 [10 npm 安全最佳实践](https://snyk.io/blog/ten-npm-security-best-practices/),你应该在帖子中采用，其中包括一个高分辨率的可打印 PDF，如下图所示。

感谢阅读，也感谢 Verdaccio 团队的 Juan Picado 和我一起工作。[看看这个](https://snyk.io/blog/ten-npm-security-best-practices/)

[![Node Version](img/7581a36bdb7e2700216f6b77152f52fb.png)](https://snyk.io/blog/ten-npm-security-best-practices/)