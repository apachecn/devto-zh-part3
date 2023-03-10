# 隐藏字符

> 原文：<https://dev.to/brianmcoates/hidden-characters-485f>

前几天，有人向我的团队报告了一个错误。在 windows 机器和 android 设备上，我们得到了这个字符。

[![](img/25cc7cfeec2e2ab7afa57bb8d32086f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m0nivDPI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_CBD5FD7C06F427E5ED25F8374D3BA4322F37A0714B5C35ADCD3068E21CD15805_1551985270824_image.png)

在 android 上，我们只能看到空白的方块

[![](img/5ea9f621a00666658ce1c220ea09d77f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TcN7h1DR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_CBD5FD7C06F427E5ED25F8374D3BA4322F37A0714B5C35ADCD3068E21CD15805_1551985338254_image.png)

当我们去查看代码时，我们什么也找不到。

[![](img/dcc0f8483c48e63bd651af7c2226ec89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aC_uT0W3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_CBD5FD7C06F427E5ED25F8374D3BA4322F37A0714B5C35ADCD3068E21CD15805_1551985435353_image.png)

我们能够 cmd + shift +右箭头键，它不会选择任何东西，但你可以删除字符。然后，我的老板告诉我关于基本编辑器，以及它有一个设置，你可以打开它来查看隐藏的字符。

这是对隐藏角色运行的设置

[![](img/6f64e0268a50ca76fb69bc7e8154e269.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--u641WuuX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_CBD5FD7C06F427E5ED25F8374D3BA4322F37A0714B5C35ADCD3068E21CD15805_1551985640216_Screen%2BShot%2B2019-03-07%2Bat%2B12.42.05%2BPM.png)

之后我们才能够看到这个隐藏的|人物。

[![](img/c329b46bd41de7a0b2198dc9f15a3b96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gldGRS11--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_CBD5FD7C06F427E5ED25F8374D3BA4322F37A0714B5C35ADCD3068E21CD15805_1551985864777_image.png)

如果你选择了它们并做了搜索，你就能看到编辑器是怎么解释它们的。unicode 2028。[https://www.compart.com/en/unicode/U+2028](https://www.compart.com/en/unicode/U+2028)

[![](img/c040730ca45241642c9e922f2af59ae6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5f377Brd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d2mxuefqeaa7sj.cloudfront.net/s_CBD5FD7C06F427E5ED25F8374D3BA4322F37A0714B5C35ADCD3068E21CD15805_1551985984011_image.png)

然后，我能够搜索我们的整个网站，以确保不再有这些隐藏的角色在周围游荡。我们不确定这些字符是如何添加的，但它可能是从 microsoft word 或 outlook 或我们过去使用的旧设计工具复制并粘贴的。

## 加贺

我做了几个设计，其中一个是贴纸，点击这里获取一个。
[![](img/4be4839546964987f0ab5dc67f6dcf45.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--C2pvlnR7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q38orydxwnmvv7axstng.jpg)

如果你想买的话，点击这里再买一件 t 恤。
[![](img/f9fd49ac05501419a2035a1e399e9b0a.png)T5】](https://res.cloudinary.com/practicaldev/image/fetch/s--nbSUC--S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrmofj9uisdej6jebaxs.jpg)

另外，如果你已经在使用 vscode，我发现了几个插件可以帮助你找到隐藏的字符。
[https://marketplace.visualstudio.com/items?itemName = nachocab . highlight-dodge-characters](https://marketplace.visualstudio.com/items?itemName=nachocab.highlight-dodgy-characters)

[https://marketplace.visualstudio.com/items?itemName = wengerk . highlight-bad-chars](https://marketplace.visualstudio.com/items?itemName=wengerk.highlight-bad-chars)