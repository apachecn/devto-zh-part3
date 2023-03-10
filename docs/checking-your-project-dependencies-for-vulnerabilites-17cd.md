# 检查项目依赖项中的漏洞

> 原文：<https://dev.to/erikpischel/checking-your-project-dependencies-for-vulnerabilites-17cd>

鉴于最近通过 npm 上一个流行的 JavaScript 模块引入恶意代码的[案例，我想提一下](https://arstechnica.com/information-technology/2018/11/hacker-backdoors-widely-used-open-source-software-to-steal-bitcoin/) [snyk.io](http://snyk.io) 。

在一个简单、免费的场景中，snyk.io 扫描 github 或 gitlab 项目上的构建或依赖文件，**定期报告漏洞**。Snyk 支持 Node、Ruby、Java、Scala 和 Python 项目。

如果你为 snyk.io 付费，你会得到更多的集成、CLI 和 API 访问等。

在我自己的试验中，我发现即使是最近的 spring boot 和 apache camel 依赖树也有十几个高等级的漏洞！(其中很多是通过使用“com . faster XML . Jackson . core:[Jackson-databind @ 2 . 9 . 1](mailto:jackson-databind@2.9.1)”)。所以下一个问题是**升级到一个安全补丁**是否明智，比如说——Jackson-databind，虽然我只是间接使用它——换句话说:依赖的框架是否仍然可以与安全补丁版本一起工作？

一个开源的选择是 [OWASP-Dependency-Check](https://www.owasp.org/index.php/OWASP_Dependency_Check) 。它扫描 Java 和。Net 依赖项，对 Python、Ruby、PHP (composer)和 Node.js 应用程序有实验性的支持。该工具似乎是基于 JVM 的。有一个[sonar cube 插件](https://github.com/stevespringett/dependency-check-sonar-plugin)。我自己没有试过。