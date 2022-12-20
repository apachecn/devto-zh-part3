# 别忘了让巴贝尔编译。mjs 文件

> 原文：<https://dev.to/crenshaw_dev/don-t-forget-to-ask-babel-to-compile-mjs-files-h85>

我花了几个小时试图理解为什么巴贝尔没有翻译杰瑞米·瓦格纳的 [yall.js 库](https://github.com/malchata/yall.js/)中的`const`语句。

因为实际的文件名是 yall。我没有配置 Babel 来处理这个扩展。

正确的`test`配置结果如下:

```
test: /\.(m?j|t)s$/, 
```

Enter fullscreen mode Exit fullscreen mode

或者，如果不需要 TypeScript:

```
test: /\.m?js$/, 
```

Enter fullscreen mode Exit fullscreen mode

请不要让我去思考这个扩展的弊端和好处。那是给聪明人的。

但是既然有些人*确实*使用那个扩展，那么更新你的 Babel 配置和相关文档/教程可能是值得的。