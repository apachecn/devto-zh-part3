# 重命名 Vue CLI 3 项目的 src 文件夹

> 原文：<https://dev.to/anthonygore/renaming-src-folder-of-a-vue-cli-3-project-34dk>

设计一个好的 CLI 工具的挑战在于平衡合理的默认值和定制的自由。

我所知道的最好的 CLI 工具之一是 Vue CLI 3。它的一个“合理的默认值”是源文件，例如未编译的组件和 JavaScript 文件，将位于目录`src`中。

但是，如果你有你自己独特的应用程序结构，你想像 MVC 那样使用，或者也许你自己的约定，你在你所有的代码基础上使用，所以你更喜欢给源文件夹一个不同的名字，像`view`、`app`、`client`等。

在这个 [GitHub 问题](https://github.com/vuejs/vue-cli/issues/1134)中，一个用户问`src`怎么能被重命名为别的名字。

答案是，这不是可以直接做到的事情。正如埃文在本期杂志的结束语中所说:

*“对此没有一流的选择，因为我们希望所有 Vue CLI 项目都有一个一致的标准目录结构。使用不同的目录名只是个人喜好的问题，所以如果你坚持要改变它，那就只能靠你自己了。”*

## 不要惹麻烦？

那为什么要搞成这样呢？只是一个文件夹名，有关系吗？

这是我在起草我的[企业 Vue](https://dev.to/courses/enterprise-vue) 课程时不得不问自己的问题，该课程围绕创建包括 Vue CLI 3 在内的全栈 Vue/Express 应用。该应用程序的结构使得它在 scaffold 的子文件夹中包括服务器/API 文件。

> 我在上一篇文章中讨论了我对使用 Vue CLI 3 来容纳全栈应用程序的想法: [Vue CLI 3 全栈应用程序结构](https://vuejsdevelopers.com/2019/03/11/vue-cli-3-full-stack)。

作为一个坚定的个人主义者和无可救药的完美主义者，我发现我不能把客户机源文件放在一般称为`src`的文件夹中，而服务器源文件放在更贴切的名称为`server`的文件夹中。

如果你真的相信你的文件夹结构的完整性是值得的，改变`src`的名字是可行的，我将在下面解释。

## 网页包配置

您将遇到的主要问题是，Vue CLI 3 下的 Webpack 配置将`src`硬编码为源目录的名称。

Webpack config 在 Vue CLI 3 中被设计成不透明的，但是如果你确实需要看到它，你可以使用`inspect`命令。

我的建议是将它写到一个文件中，并搜索`src`的用法。

```
$ vue inspect > webpack.txt 
```

在典型的 Vue CLI 安装中，您会看到两个`src`实例，一个在别名部分，一个在条目文件中。

*webpack.txt*

```
{
...
  resolve: {
    alias: {
      '@': '/home/vagrant/app/src',
      ...
    },
    ...
  },
  ...
  entry: {
    app: [
      './src/main.js'
    ]
  }
} 
```

要更改这些，您需要创建或添加到 Vue 配置文件，并覆盖这个 Webpack 配置。我喜欢使用 [Webpack Chain](https://github.com/neutrinojs/webpack-chain) 方法来声明性地做这件事。

下面展示了如何覆盖硬编码`src`目录的规则，并将它们更改为其他内容:

*view . config . js*

```
const path = require("path");

module.exports = {
  chainWebpack: config => {
    config
      .entry("app")
      .clear()
      .add("./client/main.js")
      .end();
    config.resolve.alias
      .set("@", path.join(__dirname, "./client"))
  }
}; 
```

完成后，再次检查 Webpack 配置，您将看到`src`已被替换:

*webpack.txt*

```
{
...
  resolve: {
    alias: {
      '@': '/home/vagrant/app/client',
      ...
    },
    ...
  },
  ...
  entry: {
    app: [
      './client/main.js'
    ]
  }
} 
```

问题已解决...或者是？请记住，Vue CLI 3 插件都将假定`src`是主文件夹，所以准备好每次添加插件时以类似的方式修改配置。

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *