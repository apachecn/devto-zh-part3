# 修复一个非常常见的 Sequelize.js 警告

> 原文：<https://dev.to/mzubairahmed/fixing-a-very-common-warning-of-sequelize-js-273>

我们的开发日志中经常出现这样的错误:

> 基于字符串的运算符现在已被弃用。请使用基于符号的操作符以获得更好的安全性，更多信息请访问 http://docs . sequelize js . com/manual/tutorial/query . html # operatorsnode _ modules/sequelize/lib/sequelize . js:236:13

通过简单的谷歌搜索，我就能在 GitHub[https://github.com/sequelize/sequelize/issues/8417](https://github.com/sequelize/sequelize/issues/8417)上找到这个大问题。

那里的对话是巨大的，似乎有许多潜在的解决方案，合作者最建议的是禁用操作符别名。因此，任何基于字符串的操作符将被禁用，没有任何别名，而是基于符号的内置操作符将被使用。这可以防止跨站点攻击。

```
const sequelize = new Sequelize(config.databaseUrl, {
  operatorsAliases: false,
}) 
```

[下面的](https://github.com/sequelize/sequelize/issues/8417#issuecomment-341617577)更详细的解释。