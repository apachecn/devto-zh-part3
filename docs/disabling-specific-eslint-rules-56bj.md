# 禁用特定的 eslint 规则

> 原文：<https://dev.to/ajsharp/disabling-specific-eslint-rules-56bj>

[create react app](http://github.com/facebook/create-react-app/) eslint 预置附带了几个稍微有点烦人的规则。其中之一是 jsx-a11y/href-no-hash，它确保您不会在没有 href 属性的有效 http 地址的情况下添加 

```
module.exports = {
  "parser": "babel-eslint",
  "env": {
    "browser": true
  },
  "rules": {
    "jsx-a11y/anchor-is-valid": "off"
  }
} 
```

然后确保重新加载 vscode 窗口。

完整的 eslint 配置文件文档[在这里](https://eslint.org/docs/user-guide/configuring)。

* * *

☝️ ***一定要看看 [Sharesecret](https://www.sharesecret.co) ，它让安全共享敏感数据变得很容易。***