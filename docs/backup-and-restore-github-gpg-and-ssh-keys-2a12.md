# 备份和恢复 GitHub GPG 和 SSH 密钥

> 原文：<https://dev.to/mattdark/backup-and-restore-github-gpg-and-ssh-keys-2a12>

[![GitHub](img/6bedf17871e0dfc64920e7b1d6c1d8bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dZeEM8Yw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.githubassets.cimg/modules/logos_page/Octocat.png)

去年，我在我的宏基 Aspire E11 上重新安装了 [Manjaro](https://manjaro.org/) ，在使用[Xfce](https://xfce.org/)5 年后尝试了 [i3](https://i3wm.org/) 。之后，我为 Python 和 Rust 配置了我的开发环境，还恢复了一些配置文件。

在这样做的时候，我想起我必须配置 Git。我的许多存储库都在 GitHub 上，我通常使用 GPG 签署每个提交，并通过 SSH 推送更改。

以下是我备份和恢复 GPG & SSH 密钥时遵循的说明。如果您要移动到另一台计算机或重新安装操作系统，这可能会很有用。

## 备份

*   从~/中同时复制 id_rsa 和 id_rsa.pub。ssh/到 USB 驱动器。
*   通过执行以下命令来识别私钥。

```
$ gpg --list-secret-keys --keyid-format LONG 
```

Enter fullscreen mode Exit fullscreen mode

它会显示类似这样的内容。

```
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10] 
```

Enter fullscreen mode Exit fullscreen mode

斜杠后面的字符是私钥的 ID。

*   导出私钥。

```
gpg --export-secret-keys $ID > my-private-key.asc 
```

Enter fullscreen mode Exit fullscreen mode

*   将 my-private-key.asc 复制到 USB 驱动器。

## 恢复

*   将 id_rsa 和 id_rsa.pub 都复制到~/。ssh/
*   更改两个文件的文件权限和所有权。

```
$ chown user:user ~/.ssh/id_rsa*
$ chmod 600 ~/.ssh/id_rsa
$ chmod 644 ~/.ssh/id_rsa.pub 
```

Enter fullscreen mode Exit fullscreen mode

*   启动 ssh 代理。

```
$ exec ssh-agent bash 
```

Enter fullscreen mode Exit fullscreen mode

*   将您的 ssh 私有密钥添加到 SSH 代理中。

```
$ ssh-add ~/.ssh/id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

*   导入您的 GPG 密钥

```
$ gpg --import my-private-key.asc 
```

Enter fullscreen mode Exit fullscreen mode

现在您已经准备好使用 Git 并更新您的存储库了。