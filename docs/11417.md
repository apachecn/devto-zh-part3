# 我写了我的第一个巴别塔插件！(而且也没那么难！)

> 原文：<https://dev.to/lgraziani2712/i-wrote-my-first-babel-plugin-and-it-wasnt-that-hard-4f9h>

> 封面图片来自[https://www . grimms . eu/en/products/building-amp-rainbow-worlds/organic-shapes/1240/colored-Waldorf-blocks](https://www.grimms.eu/en/products/building-amp-rainbow-worlds/organic-shapes/1240/colored-waldorf-blocks)

* * *

今天我写了一个小小的 babel 插件，通过在 vue proyect 的最小表达式中转换`props`属性(同时，移除作为依赖项的`vue-types`)来减小 vue proyect 的大小。

### 我学习如何写一个巴别塔插件的步骤

为了理解 babel 的插件系统是如何工作的，我做了以下工作:

1.  设置 vscode 以便能够调试插件。
2.  阅读巴别塔手册的插件部分:[https://github.com/jamiebuilds/babel-handbook](https://github.com/jamiebuilds/babel-handbook)。不过，我没有全部读完。第一次什么都不懂。
3.  阅读其他插件的代码。
4.  安装`@types/babel__core`。vscode 中的 autocomplete(甚至在 JS 中)是亮的。真的很有帮助！
5.  调试很多。结合手册，让我明白了一点，怎么理解代码是怎么解读的，怎么修改的。
6.  尽可能地添加 jsdoc。你帮 vscode 帮你；)

下面的 snipet 是 vscode launch 调试 babel 插件的配置:

```
{  "type":  "node",  "request":  "launch",  "name":  "Debug babel",  "console":  "integratedTerminal",  "autoAttachChildProcesses":  true,  "program":  "${workspaceFolder}/node_modules/@babel/cli/bin/babel.js",  "args":  [  "--config-file=${workspaceFolder}/babel.config.js",  "${workspaceFolder}/path/to/file.js"  ]  } 
```

字符串`"${workspaceFolder}/path/to/file.js"`是要编译的文件。

### 巴别塔插件基本结构

```
const { declare } = require('@babel/helper-plugin-utils');
const { types: t } = require('@babel/core');

module.exports = declare(api => {
  // If the plugin requires babel 7++
  api.assertVersion(7);

  return {
    // For what I learned, although the name is not required,
    // if you add one, remember to NOT add the "babel-plugin"
    // prefix. E.g., if the package's name is
    // "babel-plugin-transform-vue-props", the name would be
    // the following:
    name: 'transform-vue-props',
    visitor: {
      /**
       * @param {babel.types.ImportDeclaration} path Import's node
       * @return {void}
       */
      ImportDeclaration(path) {
        if (path.node.source.value === 'vue-types') {
          path.remove();
        }
      },
    },
  };
}); 
```

支撑着一切发生的地方。

> 当我们谈到“去”一个节点时，我们实际上是指我们正在访问它们。
> 
> [手册:访客](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md#toc-visitors)

每个节点都有一个类型，每个节点都可以被访问。在上面的例子中，我们正在访问每个导入声明，如果它们正在导入`vue-types`库，就删除它们。

### 如何转换代码

顺便说一下，如果你想把一个对象转换成一个字符串数组(键)，你必须做以下事情:

考虑这个代码:

```
const obj = {
  name: 'Luciano',
  age: 28,
}; 
```

如果要转换成这样:

```
const obj = ['name', 'age']; 
```

您必须执行以下操作:

```
const { declare } = require('@babel/helper-plugin-utils');
const { types: t } = require('@babel/core');

module.exports = declare(() => {
  return {
    name: 'transform-obj-to-array',
    visitor: {
      /**
       * @param {babel.types.VariableDeclarator} path Declaration
       * @return {void}
       */
      VariableDeclarator(path) {
        const node = path.node;

        if (!t.isObjectExpression(node.init)) {
          return;
        }
        node.init = t.arrayExpression(
          node.init.properties.map(prop => t.stringLiteral(prop.key.name)),
        );
      },
    },
  };
}); 
```

> 顺便说一下，我必须调试它，直到我可以找到正确的访问者(不是`VariableDeclaration`也不是`AssignmentExpression`)。JSDoc + `@types/babel__core` + VSCode FTW。

如你所见，并不像替换一个字符串那样简单。来自`@babel/core`的`types`(又名`t`)道具对于验证什么结构是什么以及建造新的结构非常有帮助。

### 巴别-插件-变换-vue-道具

*   这个库可以在这里找到[https://github . com/lgraziani 2712/babel-plugin-transform-vue-props](https://github.com/lgraziani2712/babel-plugin-transform-vue-props)
*   如果 webpack(或任何捆绑器)被配置为使用生产文件(【https://github.com/dwightjack/vue-types#production-build】)，那么*确实*有助于移除`vue-types`作为依赖项(因为它在生产中不做任何事情)，它可以被压缩到 25kb ~ 3.5 kb ~ g 之间。这个尺寸不包括在每个组件中使用的`vue-types`。

## 动机

我*真的*喜欢解决优化问题，我想把`vue-types`从产品包中去掉。我到处都找遍了，但没有找到任何适合使用的东西。我还重新发现了 https://vuejs.org/v2/guide/components-props.html#Prop-Types[文档](https://vuejs.org/v2/guide/components-props.html#Prop-Types)，还记得组件的`props`属性的最简单定义是什么。

编辑:我刚找到[https://astexplorer.net/](https://astexplorer.net/)。真是够毒的！

* * *

我希望这篇文章能激励那些想探索巴别塔插件世界，但不知道从哪里或如何开始的人！干杯！