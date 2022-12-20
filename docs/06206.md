# SELinux 和 AppArmor

> 原文：<https://dev.to/rodrigosyscop/selinux-and-apparmor-4n7k>

它们都是被归类为 MAC 强制访问控制的安全相关技术。SELinux 是由 NSA(美国国家安全局)开发的，而 AppArmor 在被 Ubuntu Linux 采用后变得流行起来。

这里的主要思想是基于`ugo` / `rwx`创建扩展基本权限模式的机制。其中，它们提供了限制系统进程访问文件、目录、网络端口等的能力。

AppArmor 提供了一个自动学习模式，它能够知道系统应该如何操作，而 SELinux 从 CentOS 6 开始默认提供了一个强制模式。

## SELinux

SELinux 代表安全性增强的 Linux，它可以在两种模式下运行:

*   SELinux 将基于它的策略规则拒绝访问，策略规则是一组控制其引擎的准则。
*   SELinux 不会拒绝访问，但是如果在强制模式下运行的话，拒绝会被记录下来。

您可以选择完全禁用 SELinux，但是，现在不建议这样做！不如学习一下如何利用这个优秀的工具。

您可以使用`getenforce`命令来验证 SELInux 的当前运行模式，并且您可以通过`setenforce 0` -许可模式-或`setenforce 1` -强制模式来改变它。为了在重启后保持这种变化，你需要在`/etc/selinux/config`文件中设置`SELINUX`变量。

> 要将 SELINUX 设置为`disabled`模式，您必须编辑上述文件并重启系统。

作为一个常见的用例，让我们看看如何将 SSH 守护进程端口从`22`更改为`2222`:

1.  确保您已经安装了`policycoreutils-python`包:

```
 yum install -y policycoreutils-python 
```

1.  告诉 SELinux 端口`2222`允许 ssh 进程使用:

```
 # check all ports managed by SELinux
  selinux port -l

  # Customize ssh to run on port 2222/tcp
  semanage port -a -t ssh_port_t -p tcp 2222

  # check all customized ports managed by SELinux
  semanage -lC 
```

另一个常见的场景是更改 web 服务器使用的默认允许的`DocumentRoot`文件夹。让我们将`/srv/www`添加到允许的目录列表中:

```
semanage fcontext -a -t httpd_sys_content_t "/srv/www(/.*)?" 
```

上述命令将授予 apache 对该目录及其内容的只读访问权限。

最后，应用 pilicy，使标签更改立即生效):

```
restorecon -R -v /srv/www 
```

如果事情仍然不像预期的那样工作，你可以在`/var/log/audit/audit.log`文件中寻找`AVC`字符串。

可以在 [SELinux 官方文档](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/index)获得更多帮助。

## AppArmor

AppArmor 使用文本文件中定义的配置文件，而不是由命令管理的策略。其中有几个是现成的。这些配置文件可在`/etc/apparmod.d`获得。

您可以通过运行
来检查设备的当前状态

```
apparmor_status 
```

要在`complain`和`enforce`模式之间切换配置文件，如`smbd`配置文件，我们可以运行:

```
aa-complain /etc/apparmor.d/usr.sbin.smbd
aa-enforce  /etc/apparmor.d/usr.sbin.smbd 
```

上述命令也接受 bash 通配符来一次更改多个概要文件。

要完全禁用一个概要文件，我们只需要在`/etc/apparmor.d/disable/`目录下创建一个到它的文件的链接，就像这样:

```
sudo ln -s /etc/apparmor.d/usr.sbin.mysqld /etc/apparmor.d/disable/ 
```

你可以在 Ubuntu AppArmor 社区文档中获得更多关于 AppArmor 的信息。