# 自动移除 TypeScript 项目中所有未使用的导入

> 原文：<https://dev.to/wesgrimes/auto-remove-all-unused-imports-in-a-typescript-project-19c7>

[![Ultimate Courses](img/fe3747739b9b235e31105565d22ff033.png)](https://ultimatecourses.com/topic/typescript/ref/wes.grimes/ "Ultimate Courses")

# 问题

最近，我有一个需求，需要以编程方式递归遍历给定项目中的所有`*.ts`文件，并删除所有未使用的 TypeScript 导入。在撰写本文时，如果不打开每个单独的`*.ts`文件并在 Windows/Linux 上点击`CTRL + Shift + O`，就无法在 Visual Studio 代码中做到这一点。经过一些研究，以及 Twitter 同事的大力帮助，我找到了一个可行的解决方案。这应该适用于 Angular、React、Vue.js 或任何普通的 TypeScript 项目。

# 解

我们将使用`tslint`命令行工具，结合`tslint-etc`规则，递归地自动检测并删除目录中所有未使用的导入。如果您有一个大型项目，这个过程可能需要一些时间来运行。修复过程完成后，仔细检查所有文件的正确性非常重要。

## 安装所需工具

安装此过程运行所需的以下节点程序包。

```
$ npm install -g typescript tslint tslint-etc 
```

Enter fullscreen mode Exit fullscreen mode

## 创建一个临时的`tslint`配置文件

在项目的根目录下创建一个名为`tslint-imports.json`的新文件。这创建了一个高度集中的 tslint 进程，它只检查未使用的声明。重要的是*注意到*这将在未使用的导入、参数和变量上抛出`tslint`错误。我们只在`--fix`过程中使用它。因此，`tslint-etc`规则只自动修复未使用的导入。

该文件需要以下内容:

```
{  "extends":  [  "tslint-etc"  ],  "rules":  {  "no-unused-declaration":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 运行自动修复过程

以下命令将递归遍历项目中的所有`*.ts`文件，并删除未使用的导入。它会自动将文件保存在适当的位置。

**小心！**只有当您使用像`git`或`svn`这样的源代码控制解决方案时，这个过程才是可逆的，在这里您可以恢复更改。

```
$ tslint --config tslint-imports.json --fix --project . 
```

Enter fullscreen mode Exit fullscreen mode

## 仔细检查你的文件

在这一点上，我强烈建议你仔细检查你的文件的正确性。这个工具在我的 195 个`*.ts`文件中除了两个之外都有效。其中两个组件更新不正确。我能够通过运行一个`ng build --prod`来发现这一点，因为它是一个`Angular`应用程序。如果您的项目不使用构建工具，您可以运行手动`tslint --project .`。

## 资源

*   [原推特对话](https://twitter.com/wesgrimes/status/1096134870726774787)
*   [丝带](https://palantir.github.io/tslint/usage/cli/)
*   [功能区等](https://cartant.github.io/tslint-etc/)