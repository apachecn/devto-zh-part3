# 节点、浏览器和 Lambda 上的日期和时区异常

> 原文：<https://dev.to/dvddpl/date-and-timezone-oddities-on-node-browser-and-lambda-c39>

如果我跑

```
node
> new Date() 
```

Enter fullscreen mode Exit fullscreen mode

在终端中，我得到这个时间戳:**2019-03-28t 13:37:22.081 z**T3】而如果我在浏览器控制台中运行`new Date()`我将得到
我得到这个时间戳:**Thu Mar 28 2019 14:37:39 GMT+0100(中欧标准时间)**这更准确，因为我在汉堡，这是 1h+。

如果我将个人电脑的时钟调回到 5 小时后运行相同的代码，我会从节点得到 **2019-03-28T08:43:26.895Z** ，从浏览器控制台得到**2019 年 3 月 28 日星期四 09:43:14 GMT+0100(中欧标准时间)**。

[![back to the future](img/aa8ad9e32a6173c781458fa8ea6a115c.png)](https://i.giphy.com/media/7TZvWKVkm0xXi/giphy.gif)

这很有道理——这里没什么特别的。(尽管我对浏览器和 nodeJs 中日期构造函数的不同输出感到有点惊讶)。

当我在 Lambda 上运行代码时，时区是什么？如果代码在不同区域的容器上运行**会有不同吗？
当然——幸运的是——不是！**

> 不管 lambda 在哪里运行，它总是使用 UTC 时区。

这可以通过运行`console.log(process.env.TZ)`来测试。TZ 是一个[λ保留的环境变量](https://docs.aws.amazon.com/en_us/lambda/latest/dg/current-supported-versions.html)，它将打印 **:UTC** 。

因此(除非我弄乱了我的 pc 时钟),我部署的 Lambda 上的新 Date()将会给我与它在本地运行时相同的时间。

即使在这里也没什么好惊讶的。
[![boring..](img/3951a0b38d47ee092ecc3aad17221d1c.png)T3】](https://i.giphy.com/media/IZKuUgbb8g0Yo/giphy.gif)

今天早上搞糟我的代码的是**date . tolocaledatestring()**

因为我们必须向 RestAPI 传递一个日期格式为 *YYYY-MM-DD* 的查询参数，所以我们使用了`d.toLocaleDateString('de-DE')`，然后按照预期的格式处理输出。这是一个愚蠢的方法，但是我们不想为这个小东西而被麻烦的导入 [Moment.js](https://www.npmjs.com/package/moment) 。

一切都很好，我有许多单元测试证明不同的日期和格式。然后在部署之后(当然是在开发阶段):砰！，什么都不管用...API 拒绝了我的请求。

然后我意识到这种格式被破坏了，因为在 AWS 上 toLocaleDateString 的行为不同。嗯，不仅仅是 AWS，Node 的行为也与浏览器不同，toLocaleDateString 如何在 Node 上工作取决于 Node 版本和用它构建的 **icu** 。

在我的机器上

```
 const d = new Date()
    console.log(d)
    console.log("toLocaleDateString('')",d.toLocaleDateString())
    console.log("d.toLocaleDateString('en-US')",d.toLocaleDateString('en-US'))
    console.log("d.toLocaleDateString('de-DE')",d.toLocaleDateString('de-DE')) 
```

Enter fullscreen mode Exit fullscreen mode

正在打印:

```
2019-03-28T09:04:31.507Z
toLocaleDateString('') 3/28/2019
d.toLocaleDateString('en-GB') 28/03/2019
d.toLocaleDateString('en-US') 3/28/2019
d.toLocaleDateString('de-DE') 28.3.2019 
```

Enter fullscreen mode Exit fullscreen mode

而在λ
上

```
2019-03-28T14:05:14.952Z
toLocaleDateString('') 2019-3-28
d.toLocaleDateString('en-GB') 3/28/2019
d.toLocaleDateString('en-US') 3/28/2019
d.toLocaleDateString('de-DE') 2019-3-28 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，我没有时间进一步研究 node 的 [intl 模块](https://nodejs.org/api/intl.html#intl_internationalization_support)如何基于构建工作，也没有时间进一步研究如何使用【本期】[https://github.com/nodejs/node/issues/8500](https://github.com/nodejs/node/issues/8500)中提到的 [full-icu 包](https://www.npmjs.com/package/full-icu)，所以最终我们放弃了 reverseFormat 方法，我们只是简单地单独获取每个值来构造我们想要的字符串:
`d.getFullYear() + "-" + (d.getMonth() + 1) + "-" + d.getDate())`。

不好，但有时你也不得不接受事实，并在截止日期前完成(反正相反的方法并不聪明..)

概括一下:

*   不管你的 Lambda 运行在哪个地区，时区都是 UTC。
*   由于在节点版本中运行 ICU build，toLocaleDateString 可能会有意外的输出。
*   如果你想得到一个特定时区的当前时间，那么你必须使用 [moment-timezone](https://www.npmjs.com/package/moment-timezone) (覆盖 process.env.TZ 对 Date 使用的时区没有任何影响)

希望有帮助。