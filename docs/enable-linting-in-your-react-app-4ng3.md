# 在 React 应用中启用林挺

> 原文：<https://dev.to/booyaa/enable-linting-in-your-react-app-4ng3>

*Unsplash 上 [rawpixel](https://unsplash.com/photos/svvName5USA) 的照片*

Linters 帮助你写更好的代码(以及测试)。

当您移除对 IDE/代码编辑器自带的内置林挺的依赖时，您可以将检查绑定到您的连续交付管道。现在你的构建质量提高了！

简而言之:林挺很好。

我选择的 linter 是 [eslint](https://eslint.org/) ，这是因为它是可扩展的(插件)，并且应该在别人不进化的时候继续进化(看着你 [jshint](https://jshint.com/) )。

几乎所有的 linters 都使用某种形式的规则集。这些插件是作为[插件](https://www.npmjs.com/search?q=eslint-config)提供的，我选择了运行`create-react-app`时捆绑使用的[插件](https://www.npmjs.com/package/eslint-config-react-app)。

## 安装

```
yarn add --dev eslint@latest \
    babel-eslint@latest \
    eslint-config-react-app \
    eslint-plugin-flowtype@latest \
    eslint-plugin-import@latest \
    eslint-plugin-jsx-a11y@latest \
    eslint-plugin-react@latest 
```

在 react 项目
中创建以下文件`.eslintrc`

```
{  "parser":  "babel-eslint",  "env":  {  "browser":  true,  "node":  true  },  "extends":  "react-app"  } 
```

## 加到你的`package.json`

```
 "scripts":  {  "lint":  "eslint src"  } 
```

## 测试

```
yarn run lint 
```

## 加成:在 Visual Studio 代码中启用

安装这个扩展: [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) 。

这主要是让你们知道我还活着；)