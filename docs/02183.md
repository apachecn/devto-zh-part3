# Debian 上的 Microsoft SQL Server 客户端

> 原文：<https://dev.to/nabbisen/microsoft-sql-server-client-on-debian-1p3o>

作为安装[微软](https://www.microsoft.com) [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) 的服务器的一种方式，我们可以在 [Debian](https://www.debian.org) 上安装名为 [`sqlcmd`](https://docs.microsoft.com/sql/tools/sqlcmd-utility) 的客户端，一个命令行工具，作为 [mssql-tools](https://docs.microsoft.com/sql/tools/overview-sql-tools) 的一部分。

安装很简单:

```
$  curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list | sudo tee /etc/apt/sources.list.d/msprod.list
$  sudo apt update
$  sudo apt install mssql-tools 
```

仅此而已。
则可用:

```
$  /opt/mssql-tools/bin/sqlcmd 
```

当然，您可以将`/opt/mssql-tools/bin`添加到您的`PATH`环境变量中。

用法是这样的:

```
$  /opt/mssql-tools/bin/sqlcmd
Microsoft (R) SQL Server Command Line Tool
Version 17.3.0000.1 Linux
Copyright (c) 2012 Microsoft. All rights reserved.

usage: sqlcmd            [-U login id]          [-P password]
  [-S server or Dsn if -D is provided] 
  [-H hostname]          [-E trusted connection]
  [-N Encrypt Connection][-C Trust Server Certificate]
  [-d use database name] [-l login timeout]     [-t query timeout]
  [-h headers]           [-s colseparator]      [-w screen width]
  [-a packetsize]        [-e echo input]        [-I Enable Quoted Identifiers]
  [-c cmdend]
  [-q "cmdline query"]   [-Q "cmdline query" and exit]
  [-m errorlevel]        [-V severitylevel]     [-W remove trailing spaces]
  [-u unicode output]    [-r[0|1] msgs to stderr]
  [-i inputfile]         [-o outputfile]
  [-k[1|2] remove[replace] control characters]
  [-y variable length type display width]
  [-Y fixed length type display width]
  [-p[1] print statistics[colon format]]
  [-R use client regional setting]
  [-K application intent]
  [-M multisubnet failover]
  [-b On error batch abort]
  [-D Dsn flag, indicate -S is Dsn] 
  [-X[1] disable commands, startup script, environment variables [and exit]]
  [-x disable variable substitution]
  [-? show syntax summary] 
```

例如，可以这样执行一个 SQL 文件:

```
$  /opt/mssql-tools/bin/sqlcmd -H <db-host> -d <database> -U <db-user> -i ./xxx.sql
$  # You will be asked about <db-password>. 
```

如果您的语言环境不是英语，请在运行之前检查它:

[![nabbisen image](img/ecd1c4f3cf2c812c88a0591eeac73411.png)](/nabbisen) [## sqlcmd 错误:“区域设置::方面::_S_create_c_locale 名称无效”

### hed di nabbi sen 5 月 23 日 1 分钟阅读

#microsoft #sqlserver #sqlcmd #troubleshooting](/nabbisen/microsoft-sql-server-client-troubleshooting-about-locale-facet-screateclocale-name-not-valid-5gmj)

感谢您的阅读:)
快乐计算。