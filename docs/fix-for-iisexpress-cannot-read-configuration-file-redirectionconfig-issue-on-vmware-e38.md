# IISExpress 无法读取 VMWARE 上的配置文件 redirection.config 问题的修复程序。

> 原文：<https://dev.to/pryelluw/fix-for-iisexpress-cannot-read-configuration-file-redirectionconfig-issue-on-vmware-e38>

问题:当尝试在 Mac 上运行的 VMWARE VM 上创建 Visual Studio 解决方案时，出现以下错误:

`IISExpress 8 Cannot read configuration file redirection.config`

这个帖子提供了一些关于这个问题的见解。

有什么问题？

当创建一个新的 web 解决方案时，VS 检查所有的 IIS 配置文件。如果进程不能访问它，它就会失败。

解决方案:

原来，VMWARE 会将您主机的`Documents`目录映射到 Window 的`Documents`目录。这意味着 VS 不能访问这些文件。

步骤:

1.  关闭主机上的`Documents`目录共享。
2.  复制目录`C:\Program Files\IIS Express`。
3.  粘贴到`%userprofile%\Documents`
4.  重新启动 Visual Studio。