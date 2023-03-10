# 使用 Netrc 登录 Git

> 原文：<https://dev.to/pratham/git-login-with-netrc-2fbe>

#### 什么是 Netrc 文件？

Netrc 文件包含用户凭证，用于自动登录。它通常位于用户主目录`.netrc`中，但是可以用`NETRC`环境变量覆盖该位置。Netrc 还支持宏`macdef`来自动化任务。Netrc 可以和 ftp、curl、git 等一起使用。

#### Netrc 文件:

*   在主目录中创建名为`.netrc`的文件。
*   让我们假设您的 git 服务器托管在域`git.company.com`上

```
 ~ $ cat .netrc
  machine git.company.com
  login first.last
  password secret-password 
```

Enter fullscreen mode Exit fullscreen mode

#### 安全 netrc 文件:

*   因为`.netrc`用于存储凭证，所以让我们保护这个文件。

```
 ~ $ chmod 0600 ~/.netrc 
```

Enter fullscreen mode Exit fullscreen mode

就这样，下次你对域`git.company.com`使用 git 的时候，git 应该会代表你拿起凭证；)

#### 警告:

*   Netrc 将凭证存储在计划文本中。这就是 netrc 的使命；)

#### 用例:

*   当您有基于令牌的认证时，使用 netrc 是一个好主意。
*   通过基于令牌的身份验证实现服务帐户的自动化。