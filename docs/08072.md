# 使用对象静止扩散属性解决聚合物模量误差

> 原文：<https://dev.to/justinribeiro/working-around-polymer-modulizer-errors-with-object-rest-spread-properties-hf3>

随着 Firefox 67 在 5 月中旬登陆，并支持动态导入()，现在是开始考虑将 PRPL 的 Polymer 2 组件迁移到 Polymer 3(并最终迁移到 lit-element)的最佳时机。

从表面上看，对于大多数聚合物 web 组件或 web 应用程序来说，这不是问题。聚合物团队已经有一段时间有了[模块化器](https://github.com/Polymer/tools/tree/master/packages/modulizer)，它为你做了大部分工作。它是一个有用的工具，我在很多情况下都成功地使用了它，但是它有一个特殊的缺点:它不支持 object rest spread。

我知道你在想什么。“Justin 这是 ES2018 中的组件，无论如何你都必须将它混淆，所以你不应该使用它。”事实上，对于 Edge(这是这组组件关心的唯一不支持它的东西)，您确实必须混淆它。这是我在为这组组件构建的定制构建工具中完成的(它是基于 polymer-build 和 babel 构建的)。对第四阶段提案的支持是相当稳固的，这是一个很棒的语言特性。

这就引出了更大的问题，为什么 modulizer 不支持 object rest 传播道具？查看 CLI 上的错误，我们可以看到当前版本的 [Esprima](https://github.com/jquery/esprima) (在本文发布时为 4.0.1)不支持 object rest spread 属性。但是，通过研究他们的源代码，发现 master 分支确实支持它。有了这些知识，我们可以通过为我们的用例构建一个定制版本的 modulizer 来解决这个问题。

第一步，让我们克隆[聚合物/工具](https://github.com/Polymer/tools)单回购和[埃斯普里马](https://github.com/jquery/esprima)回购:

```
$ git clone git@github.com:Polymer/tools.git $ git clone git@github.com:jquery/esprima.git 
```

克隆了我们的 repos 后，让我们开始构建 esprima:

```
$ cd esprima $ yarn install $ yarn compile 
```

此时，您将在`dist`文件夹中有一个新的构建。

构建完成后，让我们告诉 modulizer 使用它。我们将更新 modulizer 的`package.json`以指向本地文件夹:

```
$ cd tools/packages/modulizer $ vim package.json # replace dep line # "esprima": "file:../../../esprima", 
```

编辑完成后，我们可以将 deps、build 和 link modulizer 安装到我们路径中:

```
$ yarn install $ yarn link # modulizer now in path 
```

让我们转换我们的 web 组件。

```
$ modulizer --import-style name --out . 
```

而且…我们遇到了另一个对象传播错误。怎么回事？原来我们还有另一个依赖项 recast，它使用了不同版本的 esprima。为了快速解决这个问题，我们将把重铸的版本替换为新构建的版本:

```
$ cp esprima/dist/esprima.js tools/packages/modulizer/node\_modules/recast/node\_modules/esprima/dist 
```

再次运行我们的模块器，我们看到我们的转换顺利完成！现在你可以开始移除你的旧`importHrefs`并继续你的探索以更新到 Polymer 3。