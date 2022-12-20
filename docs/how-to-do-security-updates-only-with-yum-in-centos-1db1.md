# 如何仅使用 CentOS 中的 Yum 进行安全更新

> 原文：<https://dev.to/rgeraldporter/how-to-do-security-updates-only-with-yum-in-centos-1db1>

这是一个相当基本的“只是安全更新，请！”CentOS 6+指南。

这假设你是`root`或者使用过`sudo su`。

首先，您可能需要确保您有 yum 安全插件:

```
rpm -qa | grep -i yum-plugin-security 
```

如果没有，可以用下面的更新。

```
yum install yum-plugin-security 
```

现在让我们检查是否有更新:

```
yum --security check-update 
```

如果有可用的更新，在这个命令之后会有结果。

要进行更新，请运行带有选项`--security`的更新程序，以仅指定安全更新。

```
yum update --security 
```

就是这样！

某样东西严重过时了吗？这对你有用吗？请发表评论，以帮助其他可能使用此指导的人。我最初是在 2013 年左右写的这些说明。