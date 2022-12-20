# sqlcmd 错误:“区域设置::方面::_S_create_c_locale 名称无效”

> 原文：<https://dev.to/nabbisen/microsoft-sql-server-client-troubleshooting-about-locale-facet-screateclocale-name-not-valid-5gmj>

如果您的操作系统的语言环境不是英语，当 [`sqlcmd`](https://docs.microsoft.com//sql/tools/sqlcmd-utility) 、一个[微软](https://www.microsoft.com/ja-jp/) [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) 的客户端命令行工具的安装已经完成，并且您尝试使用它时，您可能会遇到下面的错误。

```
$  /opt/mssql-tools/bin/sqlcmd
terminate called after throwing an instance of 'std::runtime_error'
  what():  locale::facet::_S_create_c_locale name not valid 
```

此外，您可以这样查看您的区域设置:

```
$  locale
LANG=ja_JP.UTF-8
LANGUAGE=
LC_CTYPE="ja_JP.UTF-8"
LC_NUMERIC="ja_JP.UTF-8"
LC_TIME="ja_JP.UTF-8"
LC_COLLATE="ja_JP.UTF-8"
LC_MONETARY="ja_JP.UTF-8"
LC_MESSAGES="ja_JP.UTF-8"
LC_PAPER="ja_JP.UTF-8"
LC_NAME="ja_JP.UTF-8"
LC_ADDRESS="ja_JP.UTF-8"
LC_TELEPHONE="ja_JP.UTF-8"
LC_MEASUREMENT="ja_JP.UTF-8"
LC_IDENTIFICATION="ja_JP.UTF-8"
LC_ALL= 
```

它们是我的 Debian 的。

好吧，让我们试着解决这个问题。
* *在[贴吧评论](https://dev.to/scientronic92/comment/hp5m)有更简单的方法。谢谢，[科学 92](https://dev.to/scientronic92) :)*

在 **`/etc/locale.gen`** 的底部加一行如下:

```
+ en_US.UTF-8 UTF-8 
```

然后运行:

```
$  sudo /usr/sbin/locale-gen 
```

希望流程结束后你的问题得到解决:)

感谢您的阅读。
快乐计算。