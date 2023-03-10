# 从终端打开 Dev.to🎉

> 原文：<https://dev.to/wangonya/open-dev-to-from-your-terminal-33g2>

有时候，当我在我的终端上进行黑客攻击时，我喜欢休息一下，看看这个了不起的社区上的文章。 [dev.to cli](https://github.com/sarthology/devtocli) 非常简洁，我强烈推荐它。很好用！如果您还没有尝试过，请尝试一下。

## 文章不再可用

不过，我觉得对我来说更好的是，我只需输入一个命令，一个新的 Dev.to 选项卡就会在我的浏览器上打开。所以我写了一个简单的 python 脚本来做这件事。使用 python 的`webbrowser`包做起来非常简单。

[![python](img/7b6b743b900d6149f202b31c1bd7dee9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xao-jPPB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://imgs.xkcd.com/comics/python.png)

然后我就想:*“为什么不把这个做成一个包，分享给大家呢？”*，而[正是我做的](https://github.com/wangonya/open-dev.to)。

## 安装

从[PyPi](https://pypi.org/project/open-dev.to/)安装开放式开发

```
pip install open-dev.to 
```

Enter fullscreen mode Exit fullscreen mode

## 如何使用

```
$ dev.to --help
Usage: dev.to [OPTIONS]

  Open a new dev.to browser tab on the browser

Options:
  -t, --tag TEXT  add a tag
  --help          Show this message and exit. 
```

Enter fullscreen mode Exit fullscreen mode

### 打开主页

```
$ dev.to 
```

Enter fullscreen mode Exit fullscreen mode

### 打开标签页面

例如 javascript、职业、生产力

```
$ dev.to -t javascript 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！

[![kitten](img/7af1c85478b96387a398f0c95ff1b2ba.png)](https://i.giphy.com/media/3oKIPnAiaMCws8nOsE/giphy.gif)