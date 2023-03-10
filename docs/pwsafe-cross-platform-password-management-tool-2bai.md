# 跨平台密码管理工具。

> 原文：<https://dev.to/lucasepe/pwsafe-cross-platform-password-management-tool-2bai>

跨平台简单和安全的命令行密码管理。

[https://github . com/lucasepe/pwsafe](https://github.com/lucasepe/pwsafe)

*   免费[开源](https://github.com/lucasepe/pwsafe)软件
*   在 [Linux](https://github.com/lucasepe/pwsafe/releases/download/v1.0.0/pwsafe-linux-amd64) 、 [Mac OSX](https://github.com/lucasepe/pwsafe/releases/download/v1.0.0/pwsafe-darwin-amd64) 、 [Windows](https://github.com/lucasepe/pwsafe/releases/download/v1.0.0/pwsafe-windows-amd64) 上工作

*   只有一个可移植的二进制文件

由于密码安全[文件格式](https://raw.githubusercontent.com/jpvasquez/PasswordSafe/master/docs/formatV3.txt)是开源的并且被广泛使用，因此也有兼容多种平台的[客户端](https://pwsafe.org/relatedprojects.shtml)。

您可以选择将所有密码存储在一个加密的主密码数据库中，或者使用多个数据库来进一步组织您的密码(例如，工作和家庭密码)。

## 创建新的密码存储(`init`)

```
| => pwsafe init -file test.dat
Secret phrase: *****
Secret phrase again: ***** 
👍 password store 'test.dat' successfully created 
```

## 添加新账户信息(`push`)

```
| => pwsafe push -file test.dat -url http://www.mysecretsite.com -user pinco.pallo@gmail.com -pass abbraadabbra "My Cool Site"
Secret phrase: *****
👍 record successfully pushed to store 'test.dat' 
```

## 显示所有记录的汇总(`list`)

```
| => pwsafe list -file test.dat
Secret phrase: *****
+-------------------------------------------------------------------------------+
|                         /Users/lucasepe/Temp/test.dat                         |
+--------------+----------+-----------------------+-----------------------------+
| TITLE        | CATEGORY | USERNAME              | URL                         |
+--------------+----------+-----------------------+-----------------------------+
| My Cool Site |          | pinco.pallo@gmail.com | http://www.mysecretsite.com |
+--------------+----------+-----------------------+-----------------------------+ 
```

## 编辑/更新一条记录(`push`

```
| => pwsafe push -file test.dat -category Bank "My Cool Site"
Secret phrase: *****
👍 record successfully pushed to store 'test.dat' 
```

## 按标题查看记录(`pull`)

```
| => pwsafe pull -file test.dat "my cool site"
Secret phrase: *****
+--------------------------------------------+
|       /Users/lucasepe/Temp/test.dat        |
+--------------------------------------------+
| TITLE        : My Cool Site                |
| GROUP        : Bank                        |
| URL          : http://www.mysecretsite.com |
| USERNAME     : pinco.pallo@gmail.com       |
| PASSWORD     : abbraadabbra                |
| NOTES        :                             |
| LAST UPDATED : 2019-29-05                  |
+--------------------------------------------+ 
```

## 将特定字段值复制到剪贴板(`clip`)

有用的，如果你想抓住密码，而不显示记录内容。

```
| => pwsafe clip -file test.dat "my cool site"
Secret phrase: *****
👍 check your clipboard for the content of the field 'pass' 
```

*   默认情况下，密码值被复制到剪贴板(`-pass`)
    *   也可以指定`-url`或者`-user`。