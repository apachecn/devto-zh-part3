# 使用 lastpass 保护环境变量

> 原文：<https://dev.to/sugendran/securing-environment-variables-with-lastpass-1nkd>

我过去一直在努力解决的一个问题是，在哪里放置访问 API 所需的秘密令牌。如果你把它放在一个. env 文件中，那么任何可以访问你的机器的人都可以读取它。如果你把它粘贴到你的 shell 中，它就会出现在你的 shell 的`history`中。是的，您可以使用 pbpaste，但是在粘贴之前，您需要将令牌复制到其他地方。我想我已经找到了解决这个问题的最好方法——使用 [lastpass cli](https://github.com/lastpass/lastpass-cli) 和[脚本](https://github.com/luketurner/lpass-env)来加载安全便笺的内容。

让我们继续吧。

首先要做的是安装 lastpass cli。假设你用的是 Mac，你可以用 brew 安装它。如果您没有使用 Mac，lastpass cli 文档中有其他安装方法。

```
brew install lastpass-cli 
```

通过执行`lpass login <username>`并按照提示进行操作，您将能够在您的 shell 中登录到 lastpass。

接下来，我们将脚本安装到您的`/usr/local/bin`中，并使其可执行。在执行这一步之前，您应该先阅读一下脚本，并确保您信任它。它的布局非常好，希望你能充分理解并信任它。

```
curl https://raw.githubusercontent.com/luketurner/lpass-env/master/bin/lpass-env -o /usr/local/bin/lpass-env && chmod +x /usr/local/bin/lpass-env 
```

现在转到 lastpass，创建一个名为`.env`的文件夹。我把它叫做这个，但实际上你可以随便叫它什么。我喜欢把你所有的脚本放在一个文件夹里，这样你就知道它们在哪里了。接下来要做的是创建第一个脚本。我创建了一个名为`github`的文件，其内容如下:

```
GITHUB_TOKEN=MYMAGICTOKEN 
```

执行以下命令将加载一个名为`GITHUB_TOKEN`的环境变量，其值为`MYMAGICTOKEN`

```
$(lpass-env export .env/github) 
```

厉害吧！？

因为我很懒，我也在我的`.zshrc`中添加了下面的函数——它应该也能和你的`.bashrc`一起工作。

```
function loadcreds() {
    $(lpass-env export .env/$1)
} 
```

现在我可以执行`loadcreds github`了，它会为我加载凭证。