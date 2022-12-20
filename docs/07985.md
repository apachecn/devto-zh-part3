# JavaScript:删除循环相关性

> 原文：<https://dev.to/oieduardorabelo/javascript-eliminando-dependencias-circulares-3hcj>

当两个或多个模块相互引用时，会发生循环相关性(也称为循环相关性)。

这可能是直接参考(a->【b】>【a】:

```
// arquivo a.ts
import { b } from 'b';
...
export a;

// arquivo b.ts
import { a } from 'a';
...
export b; 
```

ou inddirect**_ a->b->c->a)**:

```
// arquivo a.ts
import { b } from 'b';
...
export a;

// arquivo b.ts
import { c } from 'c';
...
export b;

// arquivo c.ts
import { a } from 'a';
...
export c; 
```

虽然循环从属关系不会直接导致错误(它们当然可以)，但它们几乎总是会产生意外的后果。在我们的项目中，我们在[【type script 类型检查】](https://www.typescriptlang.org/)上遇到了速度缓慢的问题，而且我们的 JavaScript 服务器经常出现“内存不足”故障。

[Node.js](https://nodejs.org/en/) 支持各模块之间的循环指令`require` / `import`，但可能很快就会混淆。在“T4”节点文档中，js 表示:“要使循环模块依赖关系在应用程序中正常工作，需要仔细规划。”。

以我的经验来说，处理循环依赖的最好方法是完全避免它们。循环依赖关系通常是错误的代码设计指示，应尽可能进行重构和删除。

# 检查循环相依性

虽然有一些节点包运行静态分析以查找循环依赖关系，但我注意到它们运行不太好。一些包遇到了一些循环依赖，而另一些包则完全错误。我找到的最好的循环依赖检查器在包装层工作。webpack 的[循环依赖插件](https://github.com/aackerman/circular-dependency-plugin)功能相当强大，使用非常简单。

以[为例，循环文档-依赖插件](https://github.com/aackerman/circular-dependency-plugin#basic-usage) :

```
// webpack.config.js
const CircularDependencyPlugin = require('circular-dependency-plugin')

module.exports = {
  entry: "./src/index",
  plugins: [
    new CircularDependencyPlugin({
      // exclude detection of files based on a RegExp
      exclude: /a\.js|node_modules/,
      // add errors to webpack instead of warnings
      failOnError: true,
      // allow import cycles that include an asyncronous import,
      // e.g. via import(/* webpackMode: "weak" */ './file.js')
      allowAsyncCycles: false,
      // set the current working directory for displaying module paths
      cwd: process.cwd(),
    })
  ]
} 
```

插件立即找到了在项目过程中引入的所有类型的循环依赖关系:

[![Saída do `circular-dependency-plugin`](img/4498da6e21744dca55ddbf15bf6cc3fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PcXTfxT3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mq8d5mw0oo2corq0low7.png)

# 修正循环依赖关系

有一些选项可用于消除循环从属关系。对于较长的字符串**a->【b】>【c】>【d】>【a】**，如果删除其中一个引用(例如引用**【d】>a**)，则的模式为

对于诸如 **A - > B - > A** 等较为简单的标准，可能需要进行重构。也许居住在 **B** 的模块可以移至 **A** 。或者，必要的代码可以提取到一个**，并且**【a】**和**【b】**都可以参考。如果两个模块执行类似的行为，也可以将它们合并到单个模块中。**

 **修复大量循环依赖项可能是一项重大的承诺，但它提高了代码库的可维护性，并可以减少将来的错误。将循环依赖插件留在 webpack 管道中时，它可以频繁运行，插入后会立即找到循环依赖关系。

下次启动项目并设置 web 包选项时，我会在第一天添加此插件！

# [t1【学分】](#cr%C3%A9ditos-%EF%B8%8F)

*   从你的 JavaScript 项目中消除循环依赖**