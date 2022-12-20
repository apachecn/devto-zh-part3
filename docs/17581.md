# 诺里斯-cli

> 原文：<https://dev.to/wangonya/norris-cli-4ifk>

今年我有过的最好的感觉:看到我做的一些事情被列在 PyPi 上😊 🎉

这是一个非常简单的 cli 应用程序，每次运行时都会收到来自 chucknorris.io 的随机笑话。我用[点击](https://click.palletsprojects.com/en/7.x/)成功了

## 安装它

```
pip3 install norris-cli 
```

Enter fullscreen mode Exit fullscreen mode

确保你安装了 pip3 版本。

## 如何使用

```
$ norris-cli --help
Usage: norris-cli [OPTIONS]

  If I had a nickel for every Chuck Norris joke out there...

Options:
  -c, --category [dev|movie|food|celebrity|science|sport|political|religion|animal|history|music|travel|career|money|fashion]
  --help                          Show this message and exit. 
```

Enter fullscreen mode Exit fullscreen mode

### 随便拿个笑话

```
$ norris-cli
Chuck Norris doesn't call the wrong number. You answer the wrong phone. 
```

Enter fullscreen mode Exit fullscreen mode

### 指定一个类别

*运行`norris-cli --help`*
可以看到所有可用的类别

```
$ norris-cli -c dev
Chuck Norris can't test for equality because he has no equal. 
```

Enter fullscreen mode Exit fullscreen mode

```
$ norris-cli -c food
Chuck Norris' favorite cereal is Kellogg's Nails 'N' Gravel. 
```

Enter fullscreen mode Exit fullscreen mode

尽情享受吧！

[![approval](img/521a4f1dfd8f729d8ad8260f2691d3ba.png)](https://i.giphy.com/media/3hvmlYNsOTFWE/giphy.gif)