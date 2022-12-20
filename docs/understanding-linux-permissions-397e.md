# 了解 Linux 权限

> 原文：<https://dev.to/k_penguin_sato/understanding-linux-permissions-397e>

# 目录

*   [简介](#introduction)
*   [文件权限](#file-permissions)
*   [文件类型](#file-type)
*   [文件模式](#file-mode)
*   [改变文件模式](#change-file-mode)
    *   [八进制模式](#octal-mode)
    *   [符号模式](#symbolic-mode)

# 简介

类 Unix 系统的多用户能力是深深植根于操作系统设计中的一个特性。

# 文件权限

在 Linux 系统上，每个文件和目录都被分配了文件所有者的访问权限。

您可以使用`ls -l`检查权限设置。

```
$ ls -l 
drwxr-xr-x. 13 root  root  1027 Jan  3 12:32 bin/cat 
```

Enter fullscreen mode Exit fullscreen mode

下面我们就来一一探究一下`drwxr-xr-x. 13 root root 1077 Jan 3 12:32 bin/cat`是什么意思。

| 命令 | 意义 |
| --- | --- |
| d | 文件类型 |
| rwxr-xr-x。 | 文件模式 |
| Thirteen | 链接数量 |
| 根 | 文件的所有者 |
| 根 | 文件所属的组 |
| One thousand and twenty-seven | 文件的大小 |
| 1 月 3 日 <u>12:32</u> | 时间戳 |
| 垃圾箱/猫 | 文件/目录的名称 |

## 文件类型

| 命令 | 文件类型 |
| --- | --- |
| - | 文件 |
| d | 目录 |
| l | 符号链接 |

## 文件模式

`r`字母表示用户有权`read`文件/目录。`w`字母意味着用户有权限`write`文件/目录。而`x`字母意味着用户有权限执行文件/目录。

| 命令 | 意义 |
| --- | --- |
| r | 阅读 |
| w | 写 |
| x | 执行 |
| - | 不允许 |

让我们看看命令中的 9 个字母。
前 3 个字母表示文件所有者的权限，后 3 个字母表示组所有者的权限，最后 3 个字母表示其他用户的权限。

```
rwx/ r-x/ r-x/

Owner: rwx
Group: r-x
Users: r-x 
```

Enter fullscreen mode Exit fullscreen mode

# 改变文件模式

`chmod`命令用于改变文件或目录的权限。

## 八进制模式

每个权限可以用一个八进制数字来指定:`read = 4`；`write = 2`；`execute = 1`；`no permission = 0`。

| 意义 | 数字 |
| --- | --- |
| 读者 | four |
| 写(w) | Two |
| 执行(x) | one |

### 举例

下面的命令是将`read(4)`、`write(2)`、`execute(1)`的权限授予所有者，将`read(4)`、`execute(1)`的权限授予集团用户，将`read(4)`的权限授予其他用户。

```
chmod 754 myfile 
```

Enter fullscreen mode Exit fullscreen mode

## 符号模式

下面是`chmod`的基本语法。

```
% chmod who operator permission filename 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用以下命令来更改模式。

| 命令 | 意义 |
| --- | --- |
| u(用户) | 用户访问 |
| g(集团) | 群组访问 |
| o(其他) | 其他访问 |
| (所有) | 用户、组和其他访问 |

| 命令 | 意义 |
| --- | --- |
| + | 添加指定的权限 |
| - | 删除指定的权限 |
| = | 设置指定的权限 |

### 举例

在下面的示例中，其他人的读取权限被取消。

```
% chmod o-r filea 
```

Enter fullscreen mode Exit fullscreen mode

在以下示例中，为用户、组和其他人添加了读取和执行权限。

```
$ chmod a+rx fileb 
```

Enter fullscreen mode Exit fullscreen mode

在以下示例中，读取、写入和执行权限被分配给该组。

```
$ chmod g=rwx filec 
```

Enter fullscreen mode Exit fullscreen mode

# 参考文献

*   [学习 shell -第 9 课:权限](http://linuxcommand.org/lc3_lts0090.php)