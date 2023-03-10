# 我们用 Python 做事情的 6 种方式

> 原文：<https://dev.to/salesforceeng/6-ways-we-get-things-done-with-python-4n0l>

Python 编程语言与工程和科学学科有着紧密的联系，这使得它的用户可以访问大量的库来解决实践和理论问题。我们在 Salesforce(我们的非盈利产品部门)、Heroku、Salesforce Einstein、行业和服务云、内部 devops 团队等部门实施了该计划。

**下面是我们用 Python 做的 6 件事(你也可以！)通过我们已经开源的项目:**

1.  通过执行十个不同的自然语言任务来征服自然语言十项全能。

    *深度学习已经显著提高了自然语言处理(NLP)任务的最新性能，但每一项都是典型的孤立研究。自然语言十项全能(decaNLP)是研究通用 NLP 模型的新基准，该模型可以执行各种复杂的自然语言任务。通过要求单个系统执行 10 个不同的自然语言任务，decaNLP 为多任务、转移和持续学习提供了独特的设置。*

2.  创建 SSL/TLS 客户端指纹，这些指纹易于在任何平台上生成，并且可以轻松共享用于威胁情报( [JA3](https://github.com/salesforce/ja3) )。

    *JA3 收集客户端 Hello 数据包中以下字段的十进制字节值；SSL 版本、接受的密码、扩展列表、椭圆曲线和椭圆曲线格式。然后，它将这些值按顺序连接在一起，使用“，”分隔每个字段，使用“-”分隔每个字段中的每个值。*

3.  在代码中使用类似表格的 Google Sheets([pygsheetsorm](https://github.com/salesforce/pygsheetsorm))。

    *有没有想过能够在你的代码中使用像表格一样的谷歌表单？如果您可以获得一个自动将列标题映射到属性的对象列表，会怎么样？那么这个项目就是给你的！这是 pygsheets 上的一个简单接口。*

4.  消除 Perforce syncs 中的无声错误( [o4](https://github.com/salesforce/o4) )。

    *在 Salesforce，我们大规模使用 Perforce。一个暴露了 p4 本身一些缺点的尺度。o4 的创建是为了提高同步的可靠性，并增加我们超大规模 CI 的可扩展性。归结起来就是一个相当可怕的事实，p4 sync 对您的本地文件进行了大部分的更改。o4 允许你继续使用 Perforce 和所有相关的工具和 IDE 插件，没有同步的不确定性。每次同步都保证完美。在极少数情况下，同步不能达到 100%, o4 将大声失败。坠毁和燃烧。不再有无声的错误！除此之外，o4 允许对 CI 进行一些显著的改进。*

5.  通过 HackerOne ( [AutoTriageBot](https://github.com/salesforce/AutoTriageBot) )对漏洞报告进行自动验证、去重复和建议支出。

    *这个 bot 可以自动验证关于 XSS、SQLi、开放重定向漏洞的报告(通过 GET 和 POST)。此外，它是以模块化的方式构建的，因此可以很容易地扩展，以添加针对其他类别漏洞的测试。*

6.  从命令行为 Salesforce 受管软件包应用程序运行持续集成( [CumulusCI](https://github.com/SFDO-Tooling/CumulusCI) )。

    *CumulusCI 提供了一个现成的完整的最佳实践开发和发布流程，该流程基于 Salesforce.org 用于构建和向成千上万用户发布受管软件包的流程。它为运行任务(单个操作)和流程(任务序列)提供了一个灵活的可插拔系统，以及一个基于 OAuth 的组织钥匙串，允许轻松连接到 Salesforce 组织并使用 AES 加密存储在本地文件中。*

    还想要更多的 Python？在 GitHub 上查看所有用 Python 构建的 [Salesforce 开源项目。](https://github.com/salesforce?utf8=%E2%9C%93&q=&type=&language=python) *[最初由 Salesforce Engineering 在媒体上发布](https://engineering.salesforce.com/6-ways-we-get-things-done-with-python-ea68ec091b41)*