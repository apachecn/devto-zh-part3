# 寻找快捷路线的测试者

> 原文：<https://dev.to/joelnet/looking-for-beta-testers-for-express-easy-routes-2mhh>

Express Easy Routes 通过配置方法建立了一个快乐的媒介[约定来表达路线。](https://en.wikipedia.org/wiki/Convention_over_configuration)

你使用快递吗？你愿意试一下[快捷路线](https://www.npmjs.com/package/@paciolan/express-easy-routes)吗？

如果你习惯这样写快捷路线👇，您可能会发现快速简易路线很有用！

```
app.use(express.static()); // ❌ NO!

// ❌ NO!
app.get("/", (req, res) => {
  res.send("Hello World!");
}); 
```

告诉 express 在哪里可以找到您的路线，而不是配置每条路线。

```
// ✅ YES!
routes({ app, path: __dirname + "/middlewares/**/*.middleware.js" });
routes({ app, path: __dirname + "/controllers/**/*.controller.js" }); 
```

我已经让 Paciolan 相信了为开源社区做贡献的好处。我希望把这变成一次积极的经历，这样他们会继续给我们开发者时间和资源来做出更多的贡献。

欢迎所有反馈！想投稿？前往 [Github](https://github.com/Paciolan/express-easy-routes) 。

继续:[NPM](https://www.npmjs.com/package/@paciolan/express-easy-routes)github

干杯！