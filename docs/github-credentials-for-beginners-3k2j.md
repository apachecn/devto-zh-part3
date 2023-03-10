# 适合初学者的 Github 凭证

> 原文：<https://dev.to/paulc_creates/github-credentials-for-beginners-3k2j>

[![Control Panel](img/e88d02d60bd12427e3b8a483d4823357.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TUsDUso7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1532200547849-1e9352d72b98%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1650%26q%3D80)

###### Unsplash 上 Jp Valery 的照片

今天我将记录如何在一段时间内只输入一次你的 GitHub 凭证。这样可以避免每次推送至 GitHub repo 时输入用户名和密码的繁琐操作。当您在远程计算机或云中工作时，这尤其有用。我不想将我的密码存储在不属于我的计算机中。我重视安全性，通过将它只缓存一段时间，让我更加放心。

我们将使用 Git CLI 中的一个工具，称为凭证助手。Git 凭证通过非 SSH 协议验证连接。它通知 Git 记住你的用户名和密码。Git 附带了一些默认的助手，可以用来实现这个过程，避免了每次推送 Github repo 时出现提示时繁琐的输入用户名和密码。

*   存储–将您的凭据存储在仅受文件权限保护的磁盘上。
*   缓存–将您的凭据在内存中存储一段时间。
*   OS x Keychain–如果可用，将使用 OSX 钥匙串应用程序来获取凭证。我相信这在 Mac 上用得最多。

我将只提供缓存指令，因为这是我更喜欢使用的。我将尝试分解传递的每个参数。你也可以参考这个 [Github 帮助文档](https://help.github.com/articles/caching-your-github-password-in-git/)在 Git 中缓存你的 Github 密码。

让我们开始吧。

```
$ git config --global credential.helper 'cache --timeout=3600'
# Set the cache to timeout after an hour 
```

**$ git config** =用于在全局或本地级别设置 git 配置值的命令。

**-全局** =表示您希望将此配置应用于用户的操作系统。全局配置值存储在隐藏文件中。这样做的一个好处是，当您在电脑上处理另一个项目时，您不必再次设置它。

**credential.helper** =告诉 Git 在每次与 Github 对话时记住你的用户名和密码。

**' cache–time out = 3600 '**=将存储您的凭据一小时。

好了，现在就这些。我一定会回来，直到我记住这个代码。