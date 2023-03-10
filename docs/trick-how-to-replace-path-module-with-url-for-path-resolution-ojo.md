# 技巧如何用 URL 替换路径模块进行路径解析

> 原文：<https://dev.to/rumkin/trick-how-to-replace-path-module-with-url-for-path-resolution-ojo>

这是一个小技巧，当您需要解析路径并以跨平台的方式进行解析时，这个技巧会很有用。这段代码没有依赖性，这使得它的使用非常简单和便宜。此外，这样的代码可以从 Node.js 迁移到浏览器，而不需要依赖捆绑:不需要 browserify、rollup 或任何东西。

诀窍在于在 URL 构造函数中使用`file:`协议。

好吧，我们来举个例子:

```
const path = require('path')

const absPath = path.resolve('/some/root', '../index.js') 
```

并替换为:

```
const absPath = new URL('../index.js', 'file:///some/root/').pathname 
```

在这两种情况下，我们接收到相同的`absPath`值:

```
/some/index.js 
```

**注意**内置`fs`模块接受[URL 作为路径](https://nodejs.org/dist/latest-v12.x/docs/api/fs.html#fs_url_object_support)。因此，当你需要在模块中使用解析路径时，可以只使用一个 URL 作为参数:

```
const absPath = new URL('../../hello.txt', 'file:///project/root/')

fs.writeFileSync(absPath, 'Hello, World!') 
```