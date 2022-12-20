# 技巧 5——审计你的项目中开源依赖项的漏洞

> 原文：<https://dev.to/lirantal/tip-5-audit-your-project-for-vulnerabilities-in-your-open-source-dependencies-46e3>

npm 生态系统是所有其他语言生态系统中最大的应用程序库。注册中心和其中的库是 JavaScript 开发人员的核心，因为他们能够利用其他人已经完成的工作，并将其合并到他们的代码库中。

也就是说，越来越多的应用程序采用开源库带来了引入安全漏洞的风险。

许多流行的 npm 包被发现是易受攻击的，如果没有对项目的依赖项进行适当的安全审计，可能会带来很大的风险。

一些例子是 npm [请求](https://snyk.io/vuln/npm:request:20160119)、[超级代理](https://snyk.io/vuln/search?q=superagent&type=npm)、[猫鼬](https://snyk.io/vuln/search?q=mongoose&type=npm)，甚至像 [jsonwebtoken](https://snyk.io/vuln/npm:jsonwebtoken:20150331) 和 [npm 验证器](https://snyk.io/vuln/search?q=validator&type=npm)这样的安全相关包。

安全性不仅仅是在安装软件包时扫描安全漏洞，还应该与开发人员工作流一起简化，以便在软件开发的整个生命周期中有效采用，并在部署代码时持续监控。

## 扫描漏洞

用 Snyk 扫描安全漏洞，使用:

```
$ npm install -g snyk
$ snyk test 
```

Enter fullscreen mode Exit fullscreen mode

当您运行 Snyk 测试时，Snyk 会报告它发现的漏洞并显示易受攻击的路径，这样您就可以跟踪依赖关系树以了解是哪个模块引入了漏洞。

最重要的是，Snyk 为您提供了可行的补救建议，因此您可以通过 Snyk 在您的存储库中打开的自动拉入请求升级到修复版本，或者在没有可用的修复程序时应用 Snyk 提供的补丁来缓解漏洞。Snyk 通过为易受攻击的软件包推荐尽可能少的 semver 升级来提供智能升级。

## 监控开源库中发现的漏洞

安全工作并没有就此结束。

部署应用程序后，在应用程序的依赖关系中发现的安全漏洞怎么办？这就是安全监控以及与项目开发生命周期紧密集成的重要性所在。

我们建议将 Snyk 与您的源代码管理(SCM)系统(如 GitHub 或 GitLab)集成，以便 Snyk 主动监控您的项目并:

*   自动打开 PRs 为您升级或修补易受攻击的依赖项
*   扫描并检测开放源代码库中可能由拉请求引入的漏洞

如果您不能将 Snyk 与 SCM 集成，也可以通过简单地运行:
来监控从 Snyk CLI 工具发送的项目快照

```
$ snyk monitor 
```

Enter fullscreen mode Exit fullscreen mode

-

我还在博客中介绍了一个完整的 [10 npm 安全最佳实践](https://snyk.io/blog/ten-npm-security-best-practices/),你应该在帖子中采用，其中包括一个高分辨率的可打印 PDF，如下图所示。

感谢阅读，也感谢 Verdaccio 团队的 Juan Picado 和我一起工作。[看看这个](https://snyk.io/blog/ten-npm-security-best-practices/)

[![Node Version](img/7581a36bdb7e2700216f6b77152f52fb.png)](https://snyk.io/blog/ten-npm-security-best-practices/)