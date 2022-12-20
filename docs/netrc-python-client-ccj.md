# Netrc python 客户端

> 原文：<https://dev.to/pratham/netrc-python-client-ccj>

### 什么是 Netrc 文件？

Netrc 文件包含用户凭证，用于自动登录。它通常位于用户主目录`.netrc`中，但是可以用`NETRC`环境变量覆盖该位置。Netrc 还支持宏`macdef`来自动化任务。Netrc 可以和 ftp、curl、git 等一起使用。

**Netrc 文件示例:**

```
 ~ $ cat .netrc
  machine product.company.com
  login first.last
  password secret-password

  macdef macro-name1
  command1
  command2 
```

Enter fullscreen mode Exit fullscreen mode

**获取客户端:**

```
 curl -o netrc_client.py  "https://gist.githubusercontent.com/pratz/789dc165c6d9f79be86608547b128c69/raw/41fb7796f6b6d0a09ac5ddeaf8c79de30a2387ed/NetRc%2520-%2520read,%2520write,%2520update" 
```

Enter fullscreen mode Exit fullscreen mode

**客户端兼容性:**

*   Linux/Mac
*   Python 2.7.x

**用法:**

```
 # create client
  netrc_instance = NetRc(
          "product.company.com",
          login="first.last",
          password="secret-password",
          account="", # account is optional
          )

  # create or update file content
  netrc_instance.create_or_update()

  # Custom file path (not recommended, as other programs might look for .netrc in users home directory)
  NETRC_FILE_PATH = /custom/file/path
  netrc_instance.create_or_update(path=NETRC_FILE_PATH) 
```

Enter fullscreen mode Exit fullscreen mode

**警告:**

*   Netrc 将凭证存储在计划文本中。这就是 netrc 的使命；)

**用例:**

*   当您有基于令牌的认证时，使用 netrc 是一个好主意。
*   通过基于令牌的身份验证实现服务帐户的自动化。