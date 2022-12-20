# 与 yarn 工作区共享公共代码

> 原文：<https://dev.to/michalbryxi/share-common-code-with-yarn-workspaces-5g29>

*yarn 工作区*的基础知识很好[在官方 yarn 文档](https://yarnpkg.com/lang/en/docs/workspaces/)或[其他博客文章](https://medium.com/square-corner-blog/ember-and-yarn-workspaces-fca69dc5d44a)中有描述。我在我的项目中使用它的方式是:

*   我的项目就像一个 monorepo。
*   我的项目使用了 [EmberJS](https://www.emberjs.com/) ，但是我认为这篇文章中的内容适用于任何基于 npm 的项目。
*   有一个 *bp-ember-components* 子目录，其中包含所有消费应用程序共享的代码。
*   有多个*应用*消费提到的 *bp-ember-components* 。

## 例子

省略不感兴趣的部分。顶层`package.json`的`admin`包只是另一个消费 app，和`frontend`一样。

顶级`./package.json` :

```
{
  "name": "monorepo",
  "workspaces": {
    "packages": [
      "frontend",
      "admin",
      "bp-ember-components"
    ],
    "nohoist": [
      "**/semantic-ui-ember",
      "**/semantic-ui-ember/**"
    ]
  }
} 
```

App 等级`./frontend/package.json` :

```
{
  "name": "frontend",
  "devDependencies": {
    "bp-ember-components": "0.0.0",
  }
} 
```

## 无葫芦

对我来说最重要的部分是[无提升纱线选项](https://yarnpkg.com/blog/2018/02/15/nohoist/)。最初我使用 [npm 链接](https://dev.to/michalbryxi/share-emberjs-common-code-between-apps-1a7k)来解决同样的问题。主要是因为*的工作空间*因为随机的奇怪错误而失败。

*TL；*博士是: *Yarn workspaces* 特性可能会在不同的`node_modules`目录下安装一个包，而不是“标准目录”。一些软件包访问其他软件包(或其自身)的源代码，假设它们安装在“标准”目录中。所以当你运行你的项目时，它会悲惨地失败。

解决方案？

1.  查看失败的堆栈跟踪，并尝试识别“根本原因”包。很可能是你的某篇`package.json`中提到的内容。
2.  在顶层`package.json`中，通过以下方式将这个*有问题的包*添加到`nohoist`数组中:`"nohoist": [ "**/problematic-package", "**/problematic-package/**"]`
3.  删除顶层的`node_modules`目录和所有的“子目录”。也许不完全必要，但只是为了确定。
4.  现在发生了三件事之一:
    1.  如果错误信息改变了，您可能已经找到了，可以返回到第 1 步。
    2.  如果错误信息没有改变，那么尝试猜测-选择另一个包并继续第 2 步。
    3.  如果错误信息消失了，那么你就赢了这场捉迷藏游戏！
5.  做个好公民[向包维护者报告问题](https://github.com/Semantic-Org/Semantic-UI-Ember/issues/241)。也许链接这篇文章可能有助于解释什么是错的。