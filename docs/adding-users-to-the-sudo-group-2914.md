# 将用户添加到 sudo 组

> 原文：<https://dev.to/robertopreste/adding-users-to-the-sudo-group-2914>

在设置一个新的 Linux 服务器(或者接管一个现有的服务器)之后，最重要的事情之一就是创建一个新用户，可能拥有 *sudo 权限*。 [Sudo](https://en.wikipedia.org/wiki/Sudo) 是一个特殊的 Linux 命令，允许用户执行管理员任务，即使他们不是系统管理员。

使用 sudo 用户(或 *sudoer* )的主要原因是因为以 root 用户身份登录通常是不可取的，因为这通常会引起麻烦，但是我们可能仍然希望能够使用非 root 用户来执行管理员任务。此外，将一个或多个用户添加到 sudo 组可以避免传播根凭据的需要，因为 sudo 命令将需要用户自己的密码，而不是根用户的密码。

sudo 组的所有成员及其限制和权限都在`/etc/sudoers`配置文件中。解释这个文件以及 sudo 的一般用法是一个非常广泛的话题，所以我们将只讨论我们想要创建一个新用户(或者我们已经有了一个用户)并将它添加到 sudoers 的情况。

## 创建新用户

如果您已经有了一个功能完整的非 root 用户，并且您只想授予它 sudo 权限，那么您可以跳到下一部分。

首先，我们可能想要创建一个新用户，稍后我们会将它添加到 sudoers 中。为了做到这一点，我们可以在终端中使用下面的命令:

```
adduser <username> 
```

Enter fullscreen mode Exit fullscreen mode

将创建一个名为`<username>`的新用户，以及他自己的主文件夹，通常位于`/home/<username>/`中。这个新用户当然需要一个密码，我们需要输入两次；出于安全原因，密码将不可见。

该命令还会提示我们输入新用户的一些基本信息，如姓名、电话号码等。可以将这些字段留空，但建议至少填写名称字段。

## 向 sudo 组添加用户

可以将用户添加到 sudo 组中，而不必摆弄`/etc/sudoers`文件。这可以使用以下命令完成:

```
usermod -aG sudo <username> 
```

Enter fullscreen mode Exit fullscreen mode

这个命令会将用户`<username>`添加到 sudo 组，就这样。

从现在开始，`<username>`用户只需在任何命令前加上 sudo，并提供自己的密码，就可以访问管理员权限。