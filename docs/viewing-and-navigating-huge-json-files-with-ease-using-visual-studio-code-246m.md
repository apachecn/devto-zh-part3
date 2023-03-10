# 使用 Visual Studio 代码查看和导航大型 JSON 文件(数百兆字节)

> 原文：<https://dev.to/dtinth/viewing-and-navigating-huge-json-files-with-ease-using-visual-studio-code-246m>

问题:我试图查看一个巨大的 JSON 文件。

[![](img/7a08890bd26d7da1c1a67469c5fae917.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bJRlJ-Et--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8pvk8amp6l2qzsn1s15n.png)

直接在 VS 代码中查看，没有语法高亮或折叠。浏览这个 JSON 树非常困难。

[![](img/77e3e2e8696994fd92cd6704b9d7d2c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rj2_jAnK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9iwpo2lpecmxe6766nr8.png)

解决方案:所以，我创建了一个新的 JavaScript 文件:

```
const data = require('./webpack.stats_master.json')
debugger 
```

…并按下了`F5`。

这将启动 Node.js 调试会话。

[![](img/e5392ffa0d93295f8c00b31e0e166a93.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X4Ctbxlx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9fdmvocgyb2cg17y48cp.png)

现在，JSON 数据在一个树形视图中可视化。问题解决了。