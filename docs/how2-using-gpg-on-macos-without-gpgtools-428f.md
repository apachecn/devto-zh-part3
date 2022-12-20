# 如何 2:在没有 GPGTools 的 macOS 上使用 GPG

> 原文：<https://dev.to/wes/how2-using-gpg-on-macos-without-gpgtools-428f>

我不喜欢 GPGTools。我想要苹果电脑上的 GPG。每个教程都有一些过时的部分。我是这么做的。

* * *

GPGTools 装了很多我不想用的东西。我只想在 GitHub 上签署我的提交，并在 macOS 钥匙圈中保存我的 GPG 密钥。

有两个主要的依赖来实现，gnupg 包含 GPG 工具来生成密钥和签名，以及一个代理来做代理的事情；pinentry-mac 是 GPGTools 的一部分，它会提示您输入密钥密码并将其存储在操作系统钥匙串中。

### GPG 设置

在任何事情之前，安装[自制软件](https://brew.sh)。

之后，安装依赖项:

```
brew install gnupg pinentry-mac 
```

然后，让我们生成您的第一个密钥。我建议使用 RSA 和 RSA，密钥大小为 4096，并且不要让密钥过期。记得[选择一个强密码](https://xkcd.com/936/)。

```
gpg --full-generate-key 
```

现在你需要通过创建一个文件~/来配置 gpg-agent 使用 pinentry-mac。gnupg/gpg-agent.conf:

```
# Connects gpg-agent to the OSX keychain via the brew-installed
# pinentry program from GPGtools. This is the OSX 'magic sauce',
# allowing the gpg key's passphrase to be stored in the login
# keychain, enabling automatic key signing.
pinentry-program /usr/local/bin/pinentry-mac 
```

然后，签署测试信息，以便 pinentry-mac 可以将您的密码储存在钥匙串中:

```
echo "test" | gpg --clearsign 
```

这将打开一个对话框，提示您输入密码。记得勾选“存储在钥匙串中”。

### 连接 GitHub

首先，复制你的私钥添加到 GitHub:

```
gpg --export --armor your@email.here | pbcopy 
```

并粘贴到 [GitHub 的设置> SSH 和 GPG 键>新 GPG 键](https://github.com/settings/gpg/new)中。

其次，配置您的 git 环境以使用签名提交。我已经在全球范围内做到了。首先获取您的公共 GPG 密钥:

```
$ gpg --list-secret-keys
(...)
sec   rsa2048 2019-01-15 [SC]
      YOUR_GPG_KEY_APPEARS_HERE
uid           [ultimate] Your Name <your@email.here>
ssb   rsa2048 2019-01-15 [E] 
```

然后配置 git:

```
git config --global commit.gpgsign true git config --global user.signingkey YOUR_GPG_KEY 
```

最后，用-S 参数提交一些东西，以确保它是签名的:

```
git commit -S -m "Testing GPG signature" 
```

### 故障排除

如果不奏效，您可以尝试的方法:

```
# Kill gpg-agent
killall gpg-agent

# Run gpg-agent in daemon mode
gpg-agent --daemon 
```

* * *

*封面照片由[迈卡·威廉姆斯](https://unsplash.com/photos/lmFJOx7hPc4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/padlock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄。*