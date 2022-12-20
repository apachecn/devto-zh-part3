# 当 npm 告诉你你完蛋了

> 原文：<https://dev.to/azure/when-npm-tells-you-you-re-hosed-3nc5>

您正在使用的软件包可能有问题，而不是 npm 本身有问题。叹气。深呼吸。咕哝。好了——是时候擦拭`node_modules`并重新开始了。

抛开原因不谈，目前，我似乎每天都在运行一些命令来尝试从错误中恢复。今天早上，凯莉·沃恩让我想到了这个问题。

> 如果一开始你没有成功，试试`rm -rf node_modules &amp;&amp; npm install`
> 
> — Kelly Vaughn 🐞 (@kvlly) [April 22, 2019](https://twitter.com/kvlly/status/1120320939303014402?ref_src=twsrc%5Etfw)

我遇到 npm 包的问题有几个促成因素。

1.  我管理很多代码，包括一些我不常接触的代码，因为它们的包已经过时了。
2.  我经常在咖啡店和无线信号差的地方工作(前一分钟无线信号好，后一分钟就差了)
3.  我已经支持了 1000 多名使用 Node.js 和 npm 的开发人员
4.  墨菲定律围绕着我

也许它们并没有影响到你——在这种情况下——那太好了！

那么，当我看到我的 npm 包出现问题时，我该怎么办？尤其是那些声称某个包可能有问题而不是 npm 本身有问题的？我清空了`node_modules`文件夹，然后再试一次。

```
rm -rf node_modules package-lock.json
npm install 
```

为此，我创建了一个 bash 别名来运行这些命令。

```
npm-wipe
npm i 
```

为什么用两个命令运行它？主要是因为我喜欢看到第一个完成，然后手动运行`npm install`。我没有好的理由。您可能更喜欢一次性运行它。

## Bash 别名

下面是我在 macOS 上设置 bash 别名的方法。

```
alias npm-wipe="rm -rf node_modules package-lock.json" 
```

希望这也能偶尔帮助你摆脱困境！你使用这种技术吗？如果不是，当 npm 错误咬你的时候你会怎么做？