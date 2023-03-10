# 基于 Travis、EL7 和 Docker 的测试

> 原文：<https://dev.to/ferricoxide/travis-el7-and-docker-based-testing-4415>

正如在[之前的一篇文章](https://www.blogger.com/blogger.g?blogID=3054063691986932274)中提到的，我的许多面向客户的活动都支持在与公共互联网部分或完全隔离的网络中进行部署。昨天，作为支持一个这样的客户的一部分，我提出了一个[新项目](https://github.com/plus3it/priv-repo-rpm-builder)，帮助自动创建私有网络的 yum 存储库配置 rpm。现在，我不得不手工插入这样的文件两次，并且在两次插入会话之间有不必要的增量(在我的辩护中，它们之间相隔了将近三年的时间跨度..而且，每一次，我都认为存储库的所有者有足够的责任心来创建和分发他们自己的配置 rpm。所以，我认为是时候实现标准化和自动化了。

通常，当我着手一个项目时，我喜欢包含我希望交付的内容的测试。因为我的大多数项目都是在公共 GitHub 库中完成的，所以我通常使用 [TravisCI](https://travis-ci.com/) 来自动化我的测试。然而，在这个项目之前，我并没有试图通过 Travis 自动化 RPM 食谱的有效性测试。通常，当自动创建我希望保留或交付的 rpm 时，我会设置一个 Jenkins 作业，获取生成的 rpm 并将它们存储在 Artifactory 中——两者都是私有托管的服务。我之前的大部分 Travis 工作都很简单，语法检查(使用像 [shellcheck](https://www.shellcheck.net/) ，JSON 验证器， [CFn 验证器](https://github.com/aws-cloudformation/cfn-python-lint)等工具)。)而不是功能检查器。

然而，这一次我试图交付一个功能(RPM spec 文件，它将用于从模板生成源文件并将结果打包)。因此，我需要能够测试一组规范文件和源模板能够可靠地用于生成 rpm。这意味着，我需要我的 TravisCI 工作来从项目文件中生成“一次性的”rpm。

TravisCI 系统的测试主机是基于 Ubuntu 的，而不是基于 RHEL 或 CentOS 的。虽然有一些工具可以让你在 Ubuntu 上生成 rpm，但是在它们的可靠性和/或代表性上有一些历史警告。因此，我的偏好是能够为我的测试打包使用基于 RHEL 或 CentOS 的上下文。幸运的是，TravisCI *确实提供了在他们的测试主机上使用 Docker 的能力。*

一般来说，建立一个面向码头工人的工作是相对简单的。事情变得“有趣”的地方是，如果 Enterprise Linux 7 附带的版本`rpmbuild`不能解析它试图构建的文件的 uid 和 GID，它会有点恼火(不要介意运行 Docker-container 中的 build-user 是“root ”)...并且在该容器内具有无限制的访问权)。如果 it *不能*解决它们，rpmbuild 任务就会失败，并显示大量不太有用的“`error: Bad owner/group: /path/to/repository/file`”消息。

在 googling 了一下之后，我最终发现我需要确保项目内容的 uid 和 GID 需要分别存在于 Docker-container 的`/etc/passwd`和`/etc/group`文件中。注意:谷歌返回给我的大多数“顶级”搜索结果表明项目文件需要进行`chown`编辑。然而，仅仅是*可映射*被证明是足够的。

完成最后冲刺...

为了解决这个问题，我需要确定 Docker 容器中的项目内容有哪些 uid 和 GID。这意味着推送一个 Travis 作业，其中包括一个(临时)诊断块，用于统计相关文件并返回给我它们的 uid 和 GID。一旦确定了 uid 和 GID，我需要更新我的 Travis 作业，将相关的 groupadd 和 useradd 语句添加到我的容器准备步骤中。我最后得到的是。

```
 sudo docker exec centos-${OS\_VERSION} groupadd -g 2000 rpmbuilder
    sudo docker exec centos-${OS\_VERSION} adduser -g 2000 -u 2000 rpmbuilder 
```

此时天色已晚，所以我简单地假设 IDs 是稳定的。我运行了十几次测试迭代，它们保持稳定，但这可能只是“运气”。如果我遇到未来的“`Bad owner/group`”错误，我将更新我的 Travis 作业定义，以扫描存储库内容的当前 uid 和 GID，然后根据*和*设置它们。但是，现在，我的测试工具起作用了:我能够知道对现有规格/模板或附加规格/模板的更新将在它们被带到需要使用的地方时创建工作 rpm。