# 设置用于构建自定义 HTML 元素的 Vue CLI 项目

> 原文：<https://dev.to/firstclown/setting-up-a-vue-cli-project-for-building-custom-html-elements-3ncj>

我在上一篇文章中谈到了什么是自定义 HTML 元素。今天，我将介绍一个新的 Vue CLI 项目的启动过程，以便您可以构建自己的项目。我将再次使用`<my-blink>`标记的例子，但是将集中于一步一步的 <sup id="fnref1">[1](#fn1)</sup> 指令，用于创建构建和部署您将要创建的标记的环境。

## 获取 Vue CLI<sup id="fnref2">T3】2T5】</sup>

您首先要设置的是 [Vue CLI](https://cli.vuejs.org/) 。这是一个了不起的工具，它将安装和配置 Vue 开发所需的几乎所有东西，并将帮助您将我们的组件构建为自定义 HTML 元素。

您将在我们的命令行中执行相当多的操作，因此打开您最喜欢的命令行，在 Windows 上键入以下内容:

```
npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

或者这个在 Mac 或者 Linux 上:

```
sudo npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

这将在你的电脑上安装一个新程序，有趣的是，这个程序叫做`vue`。这个`vue`应用程序对于任何新的 Vue 项目来说都是一个很好的起点，无论是 SPA <sup id="fnref3">[3](#fn3)</sup> ，一个移动应用程序，还是定制的 HTML 元素。

## 使用 Vue CLI 创建新项目

要创建新的 Vue 项目，您可以使用新的 Vue CLI。如果您运行下面的命令，它将创建并下载一个全新的 Vue 项目到一个名为`my-custom-elements` :
的文件夹中

```
vue create --bare --default my-custom-elements 
```

Enter fullscreen mode Exit fullscreen mode

使用`--bare`将防止 CLI 创建不必要的`HelloWorld`组件，而`--default`将告诉 CLI 建立一个简单的项目。有许多额外的东西可以使用 CLI 添加到项目中，但是目前您并不需要这些东西。

完成后，切换到新目录，并在您喜欢的编辑器中打开它。 <sup id="fnref4">[4](#fn4)</sup>

## Vue 项目结构

查看项目结构，这里有很多为我们创建的内容！这里有许多 Node.js 文件，它们为我们的项目设置了一些工具，以及构建和编译我们的标签所需的所有库。你可以在这里了解一下什么是 [`package.json`，在这里](https://nodesource.com/blog/the-basics-of-package-json-in-node-js-and-npm/)了解一下 [`node_modules`。](https://stackoverflow.com/questions/34526844/what-is-node-modules-directory-in-angularjs)

你应该会看到井里面的另外两个文件夹，`src`和`public`。我会把对`public`的解释留到下次，但是你要关注的是`src`文件夹。

`src`文件夹是项目的所有组件代码存放的地方。这是源代码在项目结构中的位置。

事实上，您将在这里创建一个新文件夹。右键点击`src`文件夹，选择新建文件夹，调用文件夹`components`。在这里，您将创建将成为定制元素的组件。

## 创建自定义元素

既然您已经设置了项目，我们将创建一个新的组件。右击新的`components`文件夹，选择新建文件。将这个文件命名为`Blink.vue`，并将其内容设置为:

```
<template>
<span ref="blinkyText"><slot></slot></span>
</template>

<script>
export default {
  name: "blink",
  mounted() {
    setInterval(() => {
      this.$refs.blinkyText.classList.toggle("onoff");
    }, 500);
  }
};
</script>

<style>
.onoff {
  visibility: hidden;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这为我们在 Vue 中创建了一个[单个文件组件](https://vuejs.org/v2/guide/single-file-components.html)，这使得使用现有的 web 开发者语法创建定制元素变得非常简单。这个组件应该很容易阅读。`<slot></slot>`将被替换为自定义标记中的内容，因此视图只是在它周围加上了一个`span`。底部的 CSS 创建了一个新的样式类，它将隐藏一个元素(将其可见性设置为`hidden`)。然后，当组件被“安装”或加载到页面上时，JavaScript 逻辑将从您创建的 span 中切换(或添加/删除)类，从而显示和隐藏`slot`中的内容。

现在，您可以将它创建为一个定制的 HTML 元素。

## 将组件构建成自定义元素

默认情况下，该项目并不设置为实际创建自定义元素。它拥有实现这一目标所需的所有工具，但是我们需要设置一个新的脚本来实现它。

打开`package.json`文件。这个文件中已经设置了很多，但是您想转到标记为`"scripts"`的部分并添加一个新的。目前，它看起来像这样:

```
 "scripts":  {  "serve":  "vue-cli-service serve",  "build":  "vue-cli-service build",  "lint":  "vue-cli-service lint"  }, 
```

Enter fullscreen mode Exit fullscreen mode

你要把`build`脚本改成新的。把它改成这样:

```
 "scripts":  {  "serve":  "vue-cli-service serve",  "build":  "vue-cli-service build --target wc --name my-blink 'src/components/Blink.vue'",  "lint":  "vue-cli-service lint"  }, 
```

Enter fullscreen mode Exit fullscreen mode

该构建脚本现在被更改为创建 Web 组件(`--target wc`)，为定制元素命名(`--name my-blink` ) <sup id="fnref5">[5](#fn5)</sup> ，并且只构建一个组件(`'src/components/Blink.vue'`)。

一旦您将它添加到`package.json`并保存文件，您现在就可以运行构建过程来创建您的定制元素。在项目文件夹的命令行中运行以下命令:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

这将构建您的定制元素，并将一个 JavaScript 文件放入您的项目的`dist`文件夹中(这应该是刚刚创建的)。它还会为您创建一个`demo.html`文件。从那个文件中，你会看到这个:

```
<meta charset="utf-8">
my-blink demo
<script src="https://unpkg.com/vue"></script>
<script src="./my-blink.js"></script>

<my-blink></my-blink> 
```

Enter fullscreen mode Exit fullscreen mode

这不是一个完整的 HTML 文件，所以不太有用，但是您可以看到使用这个定制元素需要做些什么。首先，您需要在页面上包含 Vue 框架:

```
<script src="https://unpkg.com/vue"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后将您的定制元素 JavaScript 文件包含在`dist`文件夹中:

```
<script src="./my-blink.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后使用您的新标签！

```
<my-blink></my-blink> 
```

Enter fullscreen mode Exit fullscreen mode

或者，更准确地说，如果你在现实生活中使用它:

```
<my-blink><h1>Look!!! Blinking text!!!</h1></my-blink> 
```

Enter fullscreen mode Exit fullscreen mode

这就是全部了。这里有很多步骤，我们甚至还没有谈到多个定制元素或让它们一起通信(我计划这样做)，但是您可以看到如何将一个简单的 Vue 组件创建成一个定制元素。

* * *

1.  哦哦哦，宝贝！ [↩](#fnref1)

2.  这里我假设您已经安装了[节点和 NPM](https://nodejs.org/en/) 。如果你不知道，那就先这么做。 [↩](#fnref2)

3.  单页应用 [↩](#fnref3)

4.  我可以建议使用 Visual Studio 代码吗？ [↩](#fnref4)

5.  按照标准，开发人员构建的所有定制元素都必须包含一个`-`。我将在另一篇文章中讨论好的命名惯例。 [↩](#fnref5)