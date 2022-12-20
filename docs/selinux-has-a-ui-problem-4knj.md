# SELinux 有一个 UI 问题

> 原文：<https://dev.to/goober99/selinux-has-a-ui-problem-4knj>

如果您曾经在 Red Hat(或与之密切相关的发行版，如 Fedora 和 CentOS)上对某个问题进行过故障诊断，您会看到许多教程和文章告诉您通过禁用 SELinux 来解决问题。不仅仅是随机的博客帖子和有问题的 StackOverflow 答案:我在企业软件的文档和知识库文章中看到过这个建议。

不要[禁用 SELinux](https://stopdisablingselinux.com/) 。真的，不要[关掉 SELinux](https://blog.centos.org/2017/07/dont-turn-off-selinux/) 。

CentOS 官方博客上的一篇帖子问道:“为什么文章觉得有必要彻底停用 SELinux，而不是帮助读者解决他们可能遇到的任何问题？SELinux 有那么难吗？其实真的不是。”和 SELinux 太难没有关系。SELinux 有一个 UI 问题。

当你提到用户界面(UI)设计或用户体验(UX)时，大多数人会立即想到图形用户界面(GUI)。对于命令行应用程序来说，好的 UI 同样重要。如果一个命令需要很多麻烦的标志或难以记忆的选项，用户可能会使用另一个应用程序或 GUI 来完成任务。软件库也有用户界面。API 是开发人员与库进行交互的 UI。

虽然 SELinux 通常对用户来说是不可见的，但它确实有一个 UI。我不是在谈论各种工具，包括一些允许管理员设置策略的 GUI。我说的是当用户试图做一些被 SELinux 阻止的事情时，SELinux 如何给用户反馈，为什么会被阻止，甚至是 SELinux 阻止了它。

像任何优秀的系统管理员一样，我会定期运行`yum update`来确保我不会受到任何新发现的漏洞的攻击，但我这样做也不无担心。这种情况并不常见，但有时它会破坏一些之前运行良好的东西。

前几天我在运行 Tomcat 的服务器上做了`yum update`(幸运的是，我先在测试服务器上做了)。Tomcat 工作正常，但是部署到 Tomcat 的 web 应用程序已经停止工作。我尝试重新部署应用程序并重启 Tomcat。仍然没有，所以我检查了 Tomcat 日志:

```
org.apache.commons.dbcp.SQLNestedException: Cannot create PoolableConnectionFactory (IO Error: The Network Adapter could not establish the connection)
Caused by: java.sql.SQLRecoverableException: IO Error: The Network Adapter could not establish the connection
Caused by: oracle.net.ns.NetException: The Network Adapter could not establish the connection
Caused by: java.net.ConnectException: Permission denied (connect failed) 
```

Enter fullscreen mode Exit fullscreen mode

这里绝对没有任何东西表明 SELinux 是问题所在。是的，堆栈跟踪的结尾是`Permission denied`，但这听起来可能意味着 web 应用程序正在使用的数据库帐户被锁定了。我验证了我能够通过网络访问数据库(我在我的工作站上使用 SQL Developer 运行了一个简单的查询)，我验证了 Tomcat 所在的服务器能够通过网络访问数据库(我使用 SQL*Plus 运行了一个简单的查询)，并且我确保了 web 应用程序使用的数据库帐户没有被锁定。

然后，我求助于 web 应用程序供应商的支持网站。他们有一篇关于这个错误的知识库文章。原来在 RHEL 7.6 中增加了一个 SELinux 布尔值。RHEL 角释放没有什么特别的。它们基本上只是新的安装介质。你所需要做的就是安装 RHEL 7.x，一个`yum update`就会带你到最新的点发布。抛开人们不应该期望在一个点版本中有任何突破性的改变，当 Tomcat 不能连接到数据库时，用户应该已经非常清楚原因是这个新的 SELinux 布尔值。这是一个 UI 问题。

大多数服务器没有安装桌面环境。用户(这里我指的是服务器的用户，比如管理员，而不是 web 应用程序的用户)与服务器交互的主要方式是通过 SSH。对于服务器来说，日志通常就是用户界面。

Tomcat 是问题所在，那么我要去哪里寻找问题呢？当然是在 Tomcat 日志中。然后，一个用户友好的 UI 会在 Tomcat 日志中的数据库错误旁边放置某种指示，表明数据库连接已被 SELinux 阻塞。我确信 SELinux 在自己的日志中记录了违反策略的情况，但是只有当我知道 SELinux 是问题所在时，这才有帮助。这是 SELinux 糟糕的用户界面设计。

有些人可能会认为这应该归咎于 Tomcat。Tomcat 应该记录一个更有用的错误，指出问题是 SELinux。Tomcat 可能没有意识到问题在于 SELinux。Tomcat 运行在各种平台和 Linux 发行版上，其中许多没有 SELinux。Tomcat 开发人员可能不知道 SELinux 的变化会影响 Tomcat。另一方面，SELinux 知道这种变化可能会破坏许多 Tomcat 设置(`tomcat`是布尔值)。我也没有使用 Tomcat 的第三方版本；我使用直接来自 Oracle Linux(基于 RHEL) repo 和`journalctl -u tomcat`的 Tomcat 来查看日志。由于一切都是通过 systemd 集中记录的，因此 SELinux 似乎也应该能够在那里记录消息。否则为什么要通过 systemd 强制所有日志记录？(我并不讨厌 systd，实际上，我认为与 SysV 风格的 init 相比，用 systd 创建系统服务是轻而易举的事情，但是我并不喜欢 systd 处理日志的方式。)

为什么那么多人和厂商只是建议关闭 SELinux？因为 SELinux 的 UI 不够用户友好。事实上，我在供应商的支持网站上找到的知识库文章说解决问题的方法是编辑`/etc/selinux/config`并设置`SELINUX=disabled`。我没那么做。由于本文已经确定了布尔值，所以我用`setsebool -P tomcat_can_network_connect_db 1`打开了那个特定的布尔值(`-P`标志使更改在重启后保持不变)。

这不是一个孤立的经历。有一次，我试图配置 Apache 来服务主目录中的文件。Apache 日志中没有任何内容表明 SELinux 是问题所在。在发现 SELinux boolean `httpd_enable_homedirs`之前，我花了几个小时处理文件权限和目录。

我感觉 SELinux 的好处大于糟糕的 UI 带来的不便。当问题与数据库无关时，我花时间对数据库进行故障排除。我经常使用 UI 很差的开源项目，因为我觉得开源的好处超过了糟糕的设计(尽管有实例表明我认为开源解决方案实际上比其专有的替代方案有更好的 UI)。但是如果 SELinux 有一个更好的 UI 就好了，如果我们想争取新用户开源，就需要改进我们的 UI。SELinux 需要更好地在用户查找问题的位置(例如，Tomcat 日志中的 Tomcat 问题或 Apache 日志中的 Apache 问题)指示 SELinux 何时阻止了某些内容。