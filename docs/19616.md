# #01 面向设计和开发的研究

> 原文：<https://dev.to/creatorqsf/01-research-toward-design-x-development-52b6>

# #01 面向设计 x 开发的研究

也就是原子设计

受 ln-north 的影响，特别是这篇:
[**コンポーネント指向開発とデザイナー×エンジニアの協業/设计师 X 工程师发展#01 資料/えるにゃんさん-ニコナレ**
*设计师 x 工程师开发# 01(https://roppongi-designers.connpass.com/event/98120/)での@ ln _ north...*niconare.nicovideo.jp](https://niconare.nicovideo.jp/watch/kn3536)

[![Photo by [Israel Sundseth](https://unsplash.com/@kappuru?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)](img/e973d6b5ac12df47a9d1569bfba9487d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--6RhGJUHW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/9184/0%2A2EL6UePWFTGOGYYw) *照片由[以色列 Sundseth](https://unsplash.com/@kappuru?utm_source=medium&utm_medium=referral) 上 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)* 

## 背景

我是，一个设计师，一个开发者。从事网页设计的设计师，在 Go 中从事服务器端软件的开发者。

这是我的副业。“我的”，也意味着下 de-liKeR。

根据我的经验，我有一些想法来鼓励设计师和开发者之间的团结。为了实现这一点，这个项目已经启动。

## 当前问题

1.  设计师和开发商之间的模糊界限

2.  CSS 模块

3.  原子设计

4.  CSS 没有范围

5.  HTML 导入

### 1。设计师和开发商之间的模糊界限

设计师是那些从事设计、用户界面、UX 等工作的人。有一些想法是设计师必须能理解和编写 CSS 和 HTML，而不是 JS。动画呢？还是 logo？也许，经常使用动画的公司数量很少。然而，在简单设计的潮流中，动画可能更有助于公司的品牌推广。复杂的动画还是需要 JavaScript。类切换，:before，::after，这些东西很有用，但是有时候会导致 CSS 中断。如果你需要更多的伪元素，你会在 html 中添加一些元素吧？诚然，HTML 一定与设计无关，这就是被弃用的原因，如果是这样，为什么我们要像过去一样做噩梦？

遗憾的是，情况并不那么乐观。

### 2。CSS 模块& 3。原子设计& 4。CSS 没有范围

在 React 的一些应用中，CSS 模块是被鼓励的。见[这个](https://css-modules.github.io/webpack-demo/)。但是，还是有用的，这真的是我们想要的东西吗？我们的目标是什么？引入这个真的会解决吗？
我不这么认为。我支持以范围为目的的 css 模块的想法，但是 react 方式不是解决方案。并且，这种方式被显著地调整为反应产物。

我们需要另一种方法。如果没有任何影响，一个解决方案什么也解决不了。

### 5。HTML 导入

这是我的梦想。我喜欢这种方式，因为 HTML 的语义和功能。
在我看来，最后文件设计者和开发者编辑的一定是。html。设计开发的“咸”点可以也应该是 html。

原因是，html 是一种标记语言。标记语言显示文档的构造。所以分析的方式一定是从 HTML 开始，然后往下到 CSS。或者，如果我们想知道页面的功能，我们可以分析结构，意思是 HTML，然后是 each 函数，意思是 js。

此外，如果我们引入 HTML 导入或与 web 组件相关的东西，这种流动会更受鼓励。所以咸点就是 HTML。

## 工作

所以在这里，也许你能理解为什么 HTML 如此受重视。

这一次，作为我的副业，我想开始“另一种”方式。

首先，在 2018 年 11 月，我将其公开，这是我在 private repository 中的一个产品。
[**Qs-F/expandup**
*在 GitHub 上创建一个账户，为 Qs-F/expand up 开发做贡献。*github.com](https://github.com/Qs-F/expandup)

这个 cmd 提供了将一些 html 文件合并成一个文件的功能。也许你会想，嗯，这就像 webpack。但实际上并不一样。

这样做的第一个目标是通过使一些相同的工作变得共同来获得更多的生产力。例如，如果我每次都写，为什么我不这样做？还有就是我记忆力不好，经常忘记写 meta viewport。这些东西一定很常见。所以，我扩大了。

通过使用这个，你可以这样写:

。/.扩展/通用:

```
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0"> 
```

。/index.html:

```
<!doctype html>
<html lang="en">
  <head>
    <!-- EXPANDUP COMMON -->
  </head>
  <body>
  </body>
</html> 
```

然后，运行 expandup -w，您可以得到

。/index.html:

```
<!doctype html>
<html lang="en">
  <head>
    <!-- EXPANDUP COMMON -->
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- (EXPANDUP END) -->
  </head>
  <body>
  </body>
</html> 
```

在此之后，如果公共文件被重写，并运行 expandup，您可以获得更改。如果你在 expandup 标签里面写，它会自动删除。

幸运的是，我给出了一些例外，这是针对格式化的。任何缩进的前缀空格或制表符都会被忽略。

简而言之，expandup 使内容保持最新，当然还有某种版本。现在，我将它用作 riot.js 的 google 字体安装程序或脚本标签。

我开发和使用 expandup 的原因是离线工作。有时候，我们会面临离线的情况。npm 之类的总是需要在线。如果用 expandup(用我的一些私人产品，不好意思)就可以解决这个问题。这是第一个原因。另外，这只适用于我的私人项目，所以它不需要支持复杂的基于目录的上下文切换器。

然而，日复一日，我觉得这是一个不错的产品。如果产品使用 minify 工具，可能会自动删除 html 评论，所以不会影响产品。因此，我考虑如何应用当前项目的扩展配置。

这都是为了扩张。

那么，有什么关系呢？

首先，作为这个项目，我要更新 expandup。变化如下:

*   任何目录名将被接受，因此您可以将其指定为选项。如果你想用另一个名字。膨胀，这是可以的。

*   显示()的前缀和后缀是可配置的。

这是用于 riot.js 的风格。例如:

assets/style/area.css

```
.area-shadow {
  box-shadow: rgba(0, 0, 0, .23) 0 16px 64px;
  display: inline-block;
}

.top-level-padding {
  padding: 32px;
} 
```

样本.标签:

```
<sample-tag>
  <h2 class="area-shadow top-level-padding">Hello World!!</h2>
  <style>
/* EXPANDUP area.css */
  </style>
</sample-tag> 
```

这只是一个提议，但是

```
$ expandup -c ./assets/style/ --prefix-marker "/* EXPANDUP " --suffix-marker " */" --end-marker "/* (EXPANDUP END) */" . 
```

您可以获得:

```
<sample-tag>
  <h2 class="area-shadow top-level-padding">Hello World!!</h2>
  <style>
/* EXPANDUP area.css */
.area-shadow {
  box-shadow: rgba(0, 0, 0, .23) 0 16px 64px;
  display: inline-block;
}

.top-level-padding {
  padding: 32px;
}
/* (EXPANDUP END) */
  </style>
</sample-tag> 
```

这是这个项目的第一个实验。之后，主题将是后 css，CSS 摘要。

#01 到此为止。感谢您的阅读。