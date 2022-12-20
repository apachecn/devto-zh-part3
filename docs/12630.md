# 隐式传递对等依赖

> 原文：<https://dev.to/arcanis/implicit-transitive-peer-dependencies-ed0>

**TL；DR:** 如果你写一个依赖于`Foo`的包，并且`Foo`有一个对等依赖，那么你必须在`dependencies`或者`peerDependencies`字段中提供它。您不会“隐式继承”在`Foo`中声明的对等依赖。

* * *

同伴依赖是一种易变的野兽。有时很强大，因为它们允许我们选择自己想要使用的包的版本，有时很烦人，因为它们会触发一系列“未满足的对等依赖”错误(顺便说一句，Yarn 现在支持[可选的对等依赖](https://github.com/yarnpkg/rfcs/blob/master/accepted/0000-optional-peer-dependencies.md)！；).他们也有一些边角案例，这是我们今天要讲的其中一个。

想象你正在为巴别塔写一个预置。您的预设依赖于超级有用的`babel-plugin-proposal-class-properties`。不错！这就是你的`package.json`的样子:

```
{  "name":  "babel-preset-arcanis",  "dependencies":  {  "@babel/plugin-proposal-class-properties":  "^7.3.3"  }  } 
```

你把它发布到 npm 注册中心，一切都很好。对吗？不对！看，你忘了一个小细节。让我们看`package.json`对 [`babel-plugin-proposal-class-properties@7.3.3`对](https://cdn.jsdelivr.net/npm/@babel/plugin-proposal-class-properties@7.3.3/package.json)来搞清楚问题。

```
{  "name":  "@babel/plugin-proposal-class-properties",  "version":  "7.3.3",  "...",  "peerDependencies":  {  "@babel/core":  "^7.0.0-0"  }  } 
```

啊！在我们不知情的情况下，`babel-plugin-proposal-class-properties`对`@babel/core`有一个对等依赖，而我们没有提供它！现在，我已经听到你了:*“但是我亲爱的梅尔，`@babel/core`是由我们预置的用户提供的，因此我们不需要列出它-包管理器会弄清楚的”*。这听起来确实合乎逻辑，但你的计划有一个缺陷。

* * *

让我们先把巴别塔的例子放在一边，让我们考虑一个稍微不同的例子。想象以下情况:

*   您的应用程序依赖于`Foo`和`Bar@1`
*   `Foo`包依赖于`Baz`和`Qux`
*   `Baz`包依赖于`Bar`
*   为了简单起见，假设`Baz`和`Qux`不能被提升(在真实的场景中，这通常是因为它们的直接祖先碰巧依赖于不兼容的版本)。

现在让我们来揭开发生了什么。同样为了简单起见，让我们假设我们在一个老式的、非 PnP 的环境中(即一个大的`node_modules`)。在这种情况下，我们将以类似于下面的东西结束:

```
./node_modules/bar@1
./node_modules/foo
./node_modules/foo/node_modules/baz
./node_modules/foo/node_modules/qux 
```

那么:`Baz`能够访问你的应用程序提供的`Bar`版本吗？*“是的，当然”*，我听到你说，*“因此，将军，我赢了，你欠我五块钱。”没有这么快。让我们来谈谈这个`Qux`家伙。事实上，让我们添加以下要求:*

*   `Qux`包依赖于`Bar@2`

听起来不多，但是它将如何改变我们的包在磁盘上的布局呢？嗯，相当多。看，因为`Bar@1`(我们的应用程序需要的)和`Bar@2`(`Qux`需要的)不能合并，包管理器会发现自己处于`Bar`只能被提升一级(在`Foo`内)
的情况

```
./node_modules/bar@1
./node_modules/foo
./node_modules/foo/node_modules/baz
./node_modules/foo/node_modules/bar@2
./node_modules/foo/node_modules/qux 
```

看到了吗？我们的`Bar@2`包裹出现在`foo/node_modules/bar`——不能再吊了！这需要做的很简单:现在，`Baz`不像你所期望的那样需要`Bar@1`，而是使用从`Qux`附属中提升出来的`Bar@2`。

我再一次听到你的话:*“好的，但是包管理器应该明白，既然在`Foo`中有一个可传递的对等依赖，那么`Bar@2`就不应该被提升到它里面”*。你开始对包经理要求很多了吧？答案没那么简单。看，一些包可能依赖于中断行为(比如，他们期望`Qux`得到`Bar@2`)。改变这一点实际上是一个突破性的改变——从算法上来说是一个有趣的问题(有趣的故事以后再说)。

* * *

让我们回到巴别塔的例子。答案是什么？我们应该做些什么来避免上述问题？为了安抚上古之神，必须做出什么牺牲？好在简单多了:

```
{  "name":  "babel-preset-arcanis",  "dependencies":  {  "@babel/plugin-proposal-class-properties":  "^7.3.3"  },  "peerDependencies":  {  "@babel/core":  "^7.0.0"  }  } 
```

看到我做了什么吗？我刚刚将`@babel/core`列为我们的依赖项之一。不多不少。由于这一点，包管理器现在完全知道该采取什么行为:因为在`@babel/core`上有一个对等依赖，现在禁止将它从可传递的依赖提升回`babel-preset-arcanis`的级别👌