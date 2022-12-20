# 在 Create React App 2.0 中使用 TypeScript 的绝对路径而不弹出

> 原文：<https://dev.to/resir014/using-typescripts-absolute-paths-in-create-react-app-20-without-ejecting-4kd9>

*标题图片:[F-4B 座舱中国湖弹射座椅试验(1967)](https://commons.wikimedia.org/wiki/File:Ejection_seat_test_at_China_Lake_with_F-4B_cockpit_1967.jpg) ，公共领域(美国)。*

***更新 2019-05-15:** 从[3.0 版本](https://github.com/facebook/create-react-app/releases/tag/v3.0.0)开始，Create React App 会尊重你在`tsconfig.json`中已经有的`baseurl`设置。如果您已经使用 CRA 3.0 和更高版本，本指南不再是必要的。*

[Create React App (CRA) 2.0](https://facebook.github.io/create-react-app/) 允许对 TypeScript 的嵌入式支持。这意味着我们终于可以从核心 CRA 包中使用 TypeScript，而不必依赖社区维护的分叉，如[create-react-app-TypeScript](https://github.com/wmonk/create-react-app-typescript)。多亏了 Babel 7 的 TypeScript 支持，与 CRA 2.0 的 TypeScript 集成是无缝的。尽管这意味着与`create-react-app-typescript`略有不同。

from `create-react-app`和 TypeScript 缺少的一个 TypeScript 特性是使用绝对导入的能力。这允许从基于项目根目录的绝对路径导入。这意味着您可以导入模块，而不必求助于相对路径。它们没什么问题，但是当你的文件夹嵌套很深时，它们有时会变得很难看。(`../../../../../../../`，有人吗？)

不幸的是，由于 CRA 2.0 使用巴别塔进行传输，这个特性相当有限。他们通过对这种行为施加一些违约来表明这一点。例如，每当我们运行 CRA 2.0 的脚本时，它会自动从`tsconfig.json`中删除`baseUrl`和`paths`选项。这意味着对于一些想利用这个特性的人来说，弹出是唯一的选择。

不再是了。多亏了一些聪明的小技巧和使用 CRA 重新布线工具，我们现在可以设置 CRA 2.0 来尊重 TypeScript 的绝对进口而不弹出。

## 出发前

请注意，重写 CRA 脚本是一项稍微高级的技术。小心行事！

通过重新编写脚本，您打破了 React 提供的[“保证”](https://github.com/facebookincubator/create-react-app/issues/99#issuecomment-234657710)。您现在接受您“拥有”您的配置，这意味着 CRA 团队不能为您的重新布线配置提供支持。

> “东西会碎”——[丹·阿布拉莫夫](https://twitter.com/dan_abramov/status/1045809734069170176)

但幸运的是，有了像`craco`这样的工具，如果出现问题，我们可以很容易地恢复到默认的 CRA 配置。这仍然是一个比完全驱逐 CRA 更好的选择。

## 设置`craco`

craco 是一个很棒的工具，它允许我们在不弹出的情况下扩展 CRA 2.0 配置。它是 [react-app-rewired](https://github.com/timarney/react-app-rewired) 的精神继承者，已经停止开发。

```
# yarn
$ yarn add @craco/craco

# npm
$ npm install @craco/craco 
```

Enter fullscreen mode Exit fullscreen mode

安装完`craco`之后，创建一个空的`craco.config.js`文件。我们以后会谈到这个。

```
// craco.config.js

module.exports = {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，将`package.json`中的 npm 脚本更改为以下内容:

```
 "scripts": {
- "start": "react-scripts start", + "start": "craco start", - "build": "react-scripts build", + "build": "craco build" - "test": "react-scripts test", + "test": "craco test"
   } 
```

Enter fullscreen mode Exit fullscreen mode

这将通过`craco`运行 CRA 脚本，将`craco.config.js`中设置的重新连线注入脚本。

## 从一个单独的文件中扩展`paths`选项

CRA 2.0 将在我们每次运行`npm start`时自动覆盖基础`tsconfig.json`上的`paths`配置。所以我们必须通过在一个单独的文件中定义它来解决这个问题。

您可以随意命名这个文件。这个文件的存在仅仅是因为 CRA 2.1 在我们每次运行`npm start`时都会覆盖我们的基本 tsconfig。总是在这个文件中定义路径！

`tsconfig.paths.json`

```
{  "compilerOptions":  {  "baseUrl":  "./",  "paths":  {  "//":  "Define absolute imports path mappings.",  "//":  "Don't forget to add these to `resolve.alias` on `craco.config.js`.",  "*":  ["src/*"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

这允许我们设置相对于`src`文件夹的绝对导入，例如`components/Container`。如果您喜欢使用通配符，如`~`，您也可以使用:

```
{  "//":  "Allows you to import modules with the `~/components/Container` format.",  "compilerOptions":  {  "baseUrl":  "./",  "paths":  {  "~/*":  ["src/*"]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以在主`tsconfig.json`文件中扩展上面的配置。

`tsconfig.json`

```
{  "//":  "Extend the config we just created",  "extends":  "./tsconfig.paths.json",  "compilerOptions":  {  "//":  "the rest of the tsconfig"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## Rewire webpack 别名使用`craco`

现在是时候告诉 transpiler 将一个根模块目录命名为正确的路径了。有两种方法可以做到这一点，一种是使用`babel-plugin-module-resolver`通过 Babel，另一种是通过设置 webpack 别名。我们将做后者。

打开您的`craco.config.js`文件并添加 webpack 配置 rewire。

```
// craco.config.js
const { paths } = require('@craco/craco')

module.exports = {
  webpack: {
    alias: {
      // Add the aliases for all the top-level folders in the `src/` folder.
      assets: `${paths.appSrc}/assets/`,
      components: `${paths.appSrc}/components/`,
      interfaces: `${paths.appSrc}/interfaces/`,
      modules: `${paths.appSrc}/modules/`,
      utils: `${paths.appSrc}/utils/`,

      // Another example for using a wildcard character
      '~': `${paths.appSrc}/`
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

再次启动开发服务器，现在您的绝对导入应该可以工作了！

## 配置笑话的`moduleNameMapper`

如果使用 Jest，还应该让它知道如何处理绝对导入。当然，我们可以通过`craco`做到这一点。

```
module.exports = {
  webpack: {
    alias: {
      // Add the aliases for all the top-level folders in the `src/` folder.
      assets: `${paths.appSrc}/assets/`,
      components: `${paths.appSrc}/components/`,
      interfaces: `${paths.appSrc}/interfaces/`,
      modules: `${paths.appSrc}/modules/`,
      utils: `${paths.appSrc}/utils/`,

      // Another example for using a wildcard character
      '~': `${paths.appSrc}/`
    }
  },
  jest: {
    configure: {
      moduleNameMapper: {
        // Jest module mapper which will detect our absolute imports.
        '^assets(.*)$': '<rootDir>/src/assets$1',
        '^components(.*)$': '<rootDir>/src/components$1',
        '^interfaces(.*)$': '<rootDir>/src/interfaces$1',
        '^modules(.*)$': '<rootDir>/src/modules$1',
        '^utils(.*)$': '<rootDir>/src/utils$1',

        // Another example for using a wildcard character
        '^~(.*)$': '<rootDir>/src$1'
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 就是这样！

您的 CRA 2.0 安装程序现在支持 TypeScript 的绝对导入。很简单，对吧？

在 React 应用程序中享受 TypeScript 的强大功能！如果你想了解更多关于 TypeScript 如何在 React 中帮助你，请查看 [React TypeScript 备忘单](https://github.com/sw-yx/react-typescript-cheatsheet)。它还包括 TypeScript 中常见的 React 模式的备忘单。