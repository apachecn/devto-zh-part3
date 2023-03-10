# 如何导入 Webpack 范围之外的模块

> 原文：<https://dev.to/geoff/how-to-import-modules-out-of-webpacks-scope-h5>

**TL；** [博士查看解决方案](#the-fix)

考虑这样一个场景:您从事一系列项目，这些项目都需要一组 JavaScript 模块，并且您希望从一个集中的位置跟踪和导入这些公共模块。因此，您开始将这些公共模块转移到它们自己的存储库中；这个想法是不再复制粘贴每一个变化，只是从这个新的回购导入！

在这个场景中，假设单个项目使用 Webpack 和 Babel 的通用工作流来传输和构建`.js`包。Webpack 有一个很棒的特性，它允许在特定标识符下的给定路径中的模块别名，而不是例如`'../path/to/commons/module.js'`。这也适用于 Webpack 配置文件范围之外的模块；可以在`resolve.alias`对象下配置别名:

```
// webpack.config.js
const path = require("path");

module.exports = {
  // ...
  resolve: {
    alias: {
      commons: path.resolve(__dirname, "../path/to/commons")
    }
  }
  // ...
}; 
```

Enter fullscreen mode Exit fullscreen mode

为了导入这些`commons`模块，其中一个项目中的 import 语句必须更新为使用这个别名:

```
// index.js
import { getAvgLength } from "commons/stringUtils"; 
```

Enter fullscreen mode Exit fullscreen mode

对于这个场景，假设`commons`目录现在只有一个文件，上面导入的`stringUtils.js`文件；那个文件看起来像这样:

```
// commons/stringUtils.js
export function getAvgLength(...strings) {
  const stringCount = strings.length;
  const combinedStringLengths = strings.reduce((total, str) => {
    return total + str.length;
  }, 0);
  return combinedStringLengths / stringCount;
} 
```

Enter fullscreen mode Exit fullscreen mode

很实用，对吧？所以 commons 目录是用一个模块初始化的，Webpack 在一个项目目录中别名化了`commons`目录，并且在那个项目的主 JavaScript 文件中设置了正确的 import 语句。现在，应该重新启动 Webpack，并成功构建一个包；重新启动浏览器以获得这个最新的版本将显示一切都已设置好，运行顺畅，不需要再考虑更多的事情。

只不过，不一定是这样。

### 问题

实际发生的是从`commons`导入的模块不是*实际上是*被 Webpack 加载并被 Babel 传输；它被导入并放在包中，可以正常使用，但仅此而已。位于`webpack.config.js`范围之外的任何模块都将被导入和捆绑，而不进行任何额外的转换；这是`bundle.js`的一部分的样子:

```
/***/ "../path/to/commons/stringUtils.js":
/*!*******************************!*\
  !*** ../path/to/commons/stringUtils.js ***!
  \*******************************/
/*! exports provided: getAvgLength */
/***/ (function(module, __webpack_exports__, __webpack_require__) {

"use strict";
__webpack_require__.r(__webpack_exports__);
/* harmony export (binding) */ __webpack_require__.d(__webpack_exports__, "getAvgLength", function() { return getAvgLength; });
function getAvgLength(...strings) {
  const stringCount = strings.length;
  const combinedStringLengths = strings.reduce((total, str) => {
    return total + str.length;
  }, 0);
  return combinedStringLengths / stringCount;
}

/***/ }), 
```

Enter fullscreen mode Exit fullscreen mode

除非`commons`代码正在使用浏览器还不支持的特性(或者，例如，像 TypeScript 或 Flow 这样的类型注释系统)，否则在构建过程开始之前，或者在不支持某些`commons`特性的浏览器中测试代码之前，问题不会很明显。如果导入的代码符合目标环境支持的 ECMAScript 版本(很可能是 ES5)，那么这可能不是问题，所以这只影响与`webpack.config.js`不在同一目录的 ES2015+代码。

### 修罗

无论对项目的 Webpack 或 Babel 配置文件进行多少调整或更新，都无助于解决这个问题；解决办法是去源头。在`commons`目录下，设置一个通过 Babel 运行所有 JS 文件的脚本，在对常用模块进行修改时要运行该脚本；这可以通过添加一个`package.json`、某种形式的巴别塔配置(见下面 package.json 文件中的属性)并安装`@babel/cli`、`@babel/core`和`@babel/preset-env` :
来实现

```
// commons/package.json
{
  "scripts": {
    "babel": "babel entry.js -d dist/"
  },
  "babel": {
    "presets": [
      [
        "@babel/env",
        {
          "targets": {
            "browsers": ["last 2 versions"]
          }
        }
      ]
    ]
  },
  "devDependencies": {
    "@babel/cli": "^7.2.3",
    "@babel/core": "^7.2.2",
    "@babel/preset-env": "^7.3.1"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

当启动`yarn babel` / `npm run babel`时，Babel 将传输所有匹配 glob 模式的文件(在上面的例子中，它只传输`entry.js`)并将结果放入`dist/`。为了让项目导入正确的、经过编译的代码，更新 Webpack 的`commons`别名以指向那个`dist`目录:

```
// webpack.config.js
// ...
resolve: {
  alias: {
    commons: path.resolve(__dirname, "../path/to/commons/dist");
  }
}
// ... 
```

Enter fullscreen mode Exit fullscreen mode

当重新启动 Webpack 或者启动一个构建时，bundle 现在应该只输出编译下来的代码，而不管 Babel 的设置是什么。问题解决了！

* * *

我绝不是 Webpack/Babel 工作流方面的专家，所以我不知道问题出在管道的哪个地方，但我怀疑断点在`babel-loader`的某个地方，因为 Webpack 正在做导入模块的工作。不管怎样，我写这篇文章不是为了指责谁，而是为了引起注意并提出解决方案。

我在工作中尝试为我的 JavaScript 模块创建一个公共目录时遇到了这个问题，并且在运行构建时，发现没有一个 ES2015+代码被编译。希望这能帮助那些考虑使用类似的开发和组织模式的人！