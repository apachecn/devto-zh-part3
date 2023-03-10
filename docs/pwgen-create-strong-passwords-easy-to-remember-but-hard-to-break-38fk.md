# PWGen -创建易于记忆但难以破解的强密码

> 原文：<https://dev.to/lucasepe/pwgen-create-strong-passwords-easy-to-remember-but-hard-to-break-38fk>

跨平台简单安全的密码生成器/管理器，不需要文件/云/任何存档。

*   自由软件
*   在 [Linux](/downloads/pwgen/pwgen-linux-amd64) 、 [Mac OSX](/downloads/pwgen/pwgen-darwin-amd64) 、 [Windows](/downloads/pwgen/pwgen-windows-amd64.exe) 上工作
*   只有一个可移植的二进制文件

密码变得复杂(一个大写字母，一个数字，一个标点符号，一个表情符号，等等。)...

*   你不可能为你所有的账户使用并记住不同的密码？
*   你经常在不同的账户上重复使用相同的密码？

使用`PWGen`,您需要记住的是:

1.  用户名
2.  作用域——可以是网站 URL 或服务名称(任何您认为容易与该帐户关联的名称)
3.  一个秘密短语——任何你容易记住的东西！

而用户名和范围必须因服务而异(如 GMail、Twitter 等)。)，密语可以一直一样。

使用这些参数`PWGen`将生成一个密码:

*   独一无二的
*   复杂且安全
*   符合所有验证标准(大写、小写、数字和特殊字符)

创建易于记忆但难以破解的强密码。

使用`PWGen`并不能阻止你使用安全的“钱包”管理你的密码，事实上<u>这是被鼓励的</u>。

两种工具的结合将会胜出。

*   为此，我来介绍一下 [PWSafe](https://www.lucasepe.it/downloads/pwsafe/) ...看看吧！

`PWGen`给你的是:

*   为网站和其他服务生成优质安全密码的简单方法
*   一个安全的机制来找回你的密码，即使你不在家
*   一个管理密码的简单系统

**警告！**

> **要为给定帐户重新创建相同的密码，您必须确保没有忘记您使用的原始密码(第 1-2-3 点)**
> 
> **如果输入不同的值，PWGen 会产生不同的密码！(大小写的区别也很重要)**

## `PWGen`是如何工作的？

*   由(用户名+范围)组成的文本使用机密短语加密。

> [基于密码的密钥导出函数 2](https://en.wikipedia.org/wiki/PBKDF2)

*   这种加密的结果被用来提供给一个确定性的随机数生成器(让我们称之为 RNG)。

*   密码由随机选取的字符组成，这些字符由 RNG 生成的整数组成。

所有这些计算都是在您每次请求时“即时”完成的。这就是为什么`PWGen`不需要任何存档。

### 用法

```
| => pwgen -h
PWGen - Create strong passwords that are easy to remember but hard to break.

A password manager that does not requires a file/cloud or whatever archive.

  - crafted with passion by Luca Sepe

Usage: pwgen [OPTIONS] <scope>

  Example: pwgen "pinco.pallo/twitter.com"

Options:
  -secret string
        used for encryption - if not provided pwgen will ask for it interactively
  -specials
        allow special characters (default true) 
```

一个简单的例子:

```
| => pwgen "pinco.pallo@gmail.com/instagram"
Secret phrase: ***** 
Secret phrase again: ***** 
M+Jj=hrH04 
```