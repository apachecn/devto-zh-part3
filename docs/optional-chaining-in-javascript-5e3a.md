# JavaScript 中的可选链接

> 原文：<https://dev.to/viralpatelblog/optional-chaining-in-javascript-5e3a>

可选链接使 Javascript 开发人员能够在尝试访问任何对象属性之前对对象执行 nil 检查。

举个例子:

```
this.props?.user?.email 
```

可选链接的行为类似于 Ruby 的 pretzel(&)操作符。

查看官方的巴别塔文档。[巴别塔-可选链接](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)