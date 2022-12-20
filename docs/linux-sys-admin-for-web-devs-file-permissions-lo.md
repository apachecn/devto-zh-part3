# Web 开发人员的系统管理(文件权限)

> 原文：<https://dev.to/allmanaj/linux-sys-admin-for-web-devs-file-permissions-lo>

# 系统管理员是一个棘手的问题

这是我们在某个时候都会遇到的事情，所以我想为什么不试着解开一些基本的谜团呢。

## 基本面

归根结底，服务器上的所有文件和目录都有一些共同点:

*   它们属于一个用户
*   他们属于一个团体
*   他们有访问权限

对于文件/目录，用户可以拥有 3 种类型的权限。

*   读取-文件可以被读取
*   Write -文件可以被写入
*   执行-用户可以执行文件

根据用户与文件的关系，还有 3 个不同的用户组。

*   文件的*所有者*
*   可以访问该文件的用户的组
*   世界其他地方

## 这究竟意味着什么

在几乎任何目录中键入`ls -la`都会显示所有这些信息。它看起来会像这样:

```
-rwxrwx--- 2 admin dev-team 4096 Apr 23 06:22 file-for-my-group
drwx------ 2 admin admin    4096 Apr 23 06:21 my-private-files
drwxrwxr-x 2 admin admin    4096 Apr 23 06:21 public-files 
```

Enter fullscreen mode Exit fullscreen mode

我设置了这个例子来显示文件/目录权限和用户/组。

这一长串字母分解了每个用户对该目录拥有的权限(在本例中)。

这分为 4 组文件权限。作为一个例子，我们来看看`public-files`目录的权限，并对其进行分解:

`drwxrwxr-x`

*   `d`表示该文件是一个目录(一个普通的文件在第一个位置只有一个破折号)
*   以下 3 个字母(`rwx`)表示拥有文件的*用户可以读取、写入和执行该文件*
*   第二组 3 个字母(同样是`rwx`)表示与该文件属于同一组的所有用户都可以读取、写入和执行该文件
*   最后 3 个字符(`r-x`)表示所有其他用户(不是所有者或组中的用户)只能读取和执行该文件。

简而言之，它看起来像:

[![File Permissions Breakdown Diagram](img/9c0a5b60ca6f12fdf0cec2a1ac0c5278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7ug6fAfR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wbegzc9vjahyhs6nhy87.png)

## 改变权限

现在我们知道了服务器如何知道我们能访问什么和不能访问什么，让我们来看看如何改变它。

还记得之前的例子吗？

```
-rwxrwx--- 2 admin dev-team 4096 Apr 23 06:22 file-for-my-group
drwx------ 2 admin admin    4096 Apr 23 06:21 my-private-files
drwxrwxr-x 2 admin admin    4096 Apr 23 06:21 public-files 
```

Enter fullscreen mode Exit fullscreen mode

假设我现在想让不在正确组中的任何人访问`file-for-my-group`文档。现在，您可以看到组外的人不能读、写或执行这个文件，但是我现在想允许任何人都可以读它。

我将使用`chmod`命令来做这件事。该命令有几个参数，如下所示:

```
chmod [options] [permission] [file to change] 
```

Enter fullscreen mode Exit fullscreen mode

我说的`permission`是指一个由 3 个数字组成的序列，它表示访问级别和我们在上面已经讨论过的信息。我们通过给每个不同的权限(读、写和执行)分配一个数字来制作这个代码

*   阅读- 4
*   写- 2
*   执行- 1

如果我要读、写、执行文件，用的数字会是`7`
(4 + 2 + 1)。如果我只想允许读取和执行，这个数字将是`5` (4 + 1)，从逻辑上讲，如果我不想允许以上任何一个，这个数字将是`0`。

知道了这一点，我们现在可以集合我们需要的三个数字来表示这些新的权限。如果我希望所有者拥有完全访问权限(`7`)，组拥有完全访问权限(`7`)，而世界上的其他人只拥有读取权限(`4`)，那么数字将是`774`。

要在 chmod 命令的情况下使用它，它应该是这样的:

```
chmod 774 file-for-my-group 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们做到了！服务器权限如何在 Linux 系统上工作的简要概述。还有更深入的内容(安全风险，使整个目录不可访问，以及更改整个文件目录的权限等。)但是这只是一个入门，让你开始使用 Linux，以及它的文件是如何工作的。任何问题或意见都请写在下面！