# 在 Windows 上设置 git+ssh+gpg

> 原文：<https://dev.to/qm3ster/setting-up-gitsshgpg-on-windows-5c85>

有时我们最终都不得不使用 Windows。我试图避免这种情况，但它还是悄悄靠近了你。
那么，如何为 VS 代码和命令行设置带有 SSH 验证和 GPG 提交签名的基本 git 呢？
也许我被 Linux 社区文档宠坏了，但我基本上没有得到答案。所以，今天我们*调查*！

**不在范围内**:我们不会:

*   使用`ssh-keygen -t rsa -b 4096 -C "YOUR.ACTUAL@MAIL.HERE"`生成 SSH 身份
*   或者 GPG 键加上`gpg --generate-key`，
*   也没有把他们加到[https://github.com/settings/keys](https://github.com/settings/keys)里。

本文假设您希望使用现有的密钥。但这些都是有据可查的。如果你不同意，并希望我发表一篇评论，请留下你的评论。

我们将使用`scoop`而不是`chocolatey`，因此我们的设置是无特权的，仅限于本地用户。

我们假设您没有安装任何东西。
那么让我们打开 PowerShell ( **而不是作为管理员的**)并开始:

## 勺

```
# Enable powershell executables  Set-ExecutionPolicy  RemoteSigned  -scope  CurrentUser  # Install scoop  iex  (new-object  net.webclient).downloadstring('https://get.scoop.sh')  # scoop uses aria2 for parallel downloads  scoop  install  aria2 
```

Enter fullscreen mode Exit fullscreen mode

## 去吧

```
scoop  install  git 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你在 Windows 10 上，不关注 Windows news，
运行`ssh -V`，准备好震惊吧。【2018 年 4 月起，Windows 搭载 OpenSSH！

> 一些报告指出，您可能需要按如下方式激活该功能(我没有必要这样做):
> 
> ```
> Add-WindowsCapability  -Online  -Name  OpenSSH.Client* 
> ```

因为我们在这里进行最小化安装，所以我们不会安装我们自己的版本。
我们仍然需要为`git`添加一个环境变量来开始使用它，尽管:

Enter fullscreen mode Exit fullscreen mode

不要忘记重启 PowerShell / VS 代码，或者在你计划使用 git 加载环境变量之后重启。

现在，让我们继续配置`git` :

```
git  config  --global  user.name  "YOUR ACTUAL NAME HERE"  git  config  --global  user.email  YOUR.ACTUAL@MAIL.HERE 
```

Enter fullscreen mode Exit fullscreen mode

让我们打印配置来验证一下，它应该是这样的:

```
git  config  -l  --global  # user.email=YOUR.ACTUAL@MAIL.HERE  # user.name=YOUR ACTUAL NAME HERE  # credential.helper=manager 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们将实际的密钥添加到`ssh` :

```
ssh-add  YOUR\KEY\PATH\id_rsa 
```

Enter fullscreen mode Exit fullscreen mode

<small>注意:如果您的密钥位于`~/.ssh/id_rsa`，并且您已经安装了`pshazz`，您可以(重新)打开一个 powershell， [`pshazz`将运行`ssh-add`](https://github.com/lukesampson/pshazz/blob/e0cbd3fe165ea194a0d2e58001306b9cab22aa56/plugins/ssh.ps1#L52) ，自动添加它(如果您有密码，这将提示您输入密码)。我觉得把你的密钥放在 Windows 上没有什么特别的价值，所以你可以`ssh-add`它，然后删除文件。</small>

现在，假设你将使用 GitHub(类似于 gitlab 等)，让我们做一个测试连接(并保存 GitHub 的签名，否则 VS 代码将会变得混乱，并永远静默获取):

```
ssh  -T  git@github.com  # The authenticity of host 'github.com (192.30.253.112)' can't be established.  # RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.  # Are you sure you want to continue connecting (yes/no)? yes  # Warning: Permanently added 'github.com,192.30.253.112' (RSA) to the list of known hosts. 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们有了工作的 git，但是提交还没有被签署。

## GPG

现在，`git`包带有一个`gpg`可执行文件，位于:

```
# It's my first time writing powershell, any advice welcome  join-path  (scoop  prefix  git)  'usr\bin\gpg.exe' 
```

Enter fullscreen mode Exit fullscreen mode

我们暂时别名为`gpg`！

```
sal  gpg  (join-path  (scoop  prefix  git)  'usr\bin\gpg.exe') 
```

Enter fullscreen mode Exit fullscreen mode

旁白:`sal`是 <small>( *哇，多 meta* )</small> 对`Set-Alias`的别名。
<small>但是根据`alias alias`，`alias`不是`Get-Alias`的别名吗？🤔🤔🤔</small>

对我来说，这是一个比独立版本稍老的版本。

```
scoop  info  gpg  gpg  --version 
```

Enter fullscreen mode Exit fullscreen mode

但是，再一次，我们要进行最小化的设置。所以，让我们把钥匙插在那里。

```
# Enable signing all commits by default  git  config  --global  commit.gpgsign  true 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们导入我们的密钥:

```
gpg  --import  C:\YOUR\KEY\LOCATION\_SOMETHNG_-private.{key,gpg} 
```

Enter fullscreen mode Exit fullscreen mode

您可能还希望最终信任您的密钥:

```
$(echo  trust;  echo  5;  echo  y;  echo  quit)  |  gpg  --command-fd  0  --edit-key  YOUR.ACTUAL@MAIL.HERE 
```

Enter fullscreen mode Exit fullscreen mode

在这之后移除你的密钥文件是没问题的，它被导入到 gpg 安装中。

最后，让我们安装更多的软件包:

```
# the default bucket doesn't include GUI applications such as VS Code  scoop  bucket  add  extras  # check for (self)updates  scoop  update  scoop  update  *  # install the good nice things  scoop  install  vscode  pshazz 
```

Enter fullscreen mode Exit fullscreen mode

## T2】🎉我们完了！🎉

结果应该是这样的:

```
git  config  -l  --global  # user.email=YOUR.ACTUAL@MAIL.HERE  # user.name=YOUR ACTUAL NAME HERE  # credential.helper=manager  # commit.gpgsign=true  gpg  -K  # C:/Users/YOUR_NAME/AppData/Roaming/gnupg/pubring.kbx  # ------------------------------------------------  # sec   rsa4096 2017-10-18 [SC]  #       8E84465BB27D2E97F5B379C0D41763D409EF23A0  # uid           [ultimate] YOUR ACTUAL NAME HERE <YOUR.ACTUAL@MAIL.HERE>  # ssb   rsa4096 2017-10-18 [E] 
```

Enter fullscreen mode Exit fullscreen mode

要测试签名，请提交:

```
git  init  git-signature-test  pushd  git-signature-test  echo  ""  >  butts  git  add  butts  git  commit  -m  Init  git  log  --show-signature  popd  rm  -r  -fo  git-signature-test 
```

Enter fullscreen mode Exit fullscreen mode

输出应该包括“良好的签名来自”，后跟`YOUR ACTUAL NAME HERE <YOUR.ACTUAL@MAIL.HERE>`。

**编辑**:看起来我们安装了整个`scoop`大部分只是为了得到`git` :)
最初这篇文章包括安装你自己的 OpenSSH 和 GPG，甚至是来自`chocolatey`的东西，但是在阅读了 GitHub 问题和实验之后，事实证明仅仅这些就足够了。🎉