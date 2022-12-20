# 如何使用:npm 标签

> 原文：<https://dev.to/andywer/how-to-use-npm-tags-4lla>

npm 中有一个特性影响着每一个`npm install`，每一个`npm publish`，然而大多数 npm 用户似乎并没有意识到。

是时候说说 [npm 标签](https://docs.npmjs.com/cli/dist-tag)了！

## 它们是什么

让我们直接进入主题:

1.  在每个没有明确版本的`npm install <package>`上，使用一个标签来解析正确的版本号:`latest`标签。

2.  在每一次`npm publish`中，标签或者被更新或者被创建。是的，它也默认为`latest`标签。

因此，让我们假设一个繁忙的工作日刚刚开始，您向您的依赖项添加了一个新的包:

```
$ npm install cowsay 
```

现在，由于您没有指定要安装的任何版本，npm 如何决定使用哪个版本？它使用了`latest`标签！

看看这个:

```
$ npm show cowsay

cowsay@1.4.0 | MIT | deps: 4 | versions: 19
cowsay is a configurable talking cow
https://github.com/piuccio/cowsay

keywords: cow, cowsay, cowthink, figlet, talking, ASCII

bin: cowsay, cowthink

(...)

dist-tags:
latest: 1.4.0

published a month ago by piuccio <piuccio@gmail.com> 
```

看一看`dist-tags`。它只显示了一个标签，即`latest`标签。从`npm show`的输出判断，我们已经可以想象标签的功能是什么:

> 标签是从一些人类友好的标识符到特定版本号的映射。

啊哈！因此，当您执行您的临时`npm install cowsay`时，npm 将获取包元数据(尝试`npm show cowsay --json`)，包括包的所有已发布标签的列表。`latest`标签将告诉 npm 要安装什么版本。

那么 npm 为什么不直接安装带有最新发布时间戳的版本呢？我们很快就会谈到这一点，但让我们在这里添加一个剧透:

因为最近的`npm publish`可能没有发布*稳定的*版本，但可能是用户不应该在生产中运行的早期测试版本。

## 使用标签

### 吞噬

要安装一个包，但是*不是*的`latest`标签:

```
$ npm install <package>@<tag> 
```

一个流行的例子是:

```
# Install the latest alpha version of React
$ npm install react@next 
```

### 出版

要发布一个默认情况下不应该安装的珍贵软件包版本:

```
$ npm publish --tag <tag> 
```

这样你可以很容易地与他人分享一些不稳定的代码，这样他们就可以测试它:

```
$ npm publish --tag beta 
```

或者

```
$ npm publish --tag testing-feature-new-dashboard 
```

如前所述，不带`--tag`参数运行`npm publish`还会更新一个标签:`latest`标签，这是发布和安装的默认标签。

### 改变标签

如果需要，您可以随时更改标签以指向另一个版本。使用`npm dist-tag`子命令:

```
$ npm dist-tag --help
npm dist-tag add <pkg>@<version> [<tag>]
npm dist-tag rm <pkg> <tag>
npm dist-tag ls [<pkg>]

alias: dist-tags 
```

如果您意外发布了使用错误标签的版本，请使用它来修复标签。

## 区别于 git 标签

值得注意的是，npm 标签在语义上不同于通常使用的 git 标签，尽管在技术上它们非常相似。

git 标签指向一个 commit，它是特定时间点的代码，通常不会改变。git 标签本质上相当于 npm 版本。

另一方面，npm 标记是一个指向版本的可变指针，而版本又是一个指向某个特定时间点的代码的不可变指针。所以 npm 标签基本上是一个元指针。

从 git 的角度来看，npm 标签和 npm 版本都可以使用 git 标签来实现，只是前一种类型的标签被认为是可变的，而版本 git 标签被认为是不可变的。

## 利润美元美元

那么，我们如何在日常工作中利用这一特性呢？

我们可以:

*   **与 beta 测试人员分享不稳定版本** : `npm publish --tag beta`
*   **发布一次使用，一劳永逸的版本** : `npm publish --tag testing-new-feature`

在另一端安装这个版本也很简单:

```
$ npm install my-fancy-package@testing-new-feature 
```

早点发布，经常发布。但是，不要和你的产品用户过不去。使用 npm 标签。

黑客快乐！

*保罗·墨菲在 [Unsplash](https://unsplash.com/search/photos/graffiti?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的预告照片。显示喷雾标签，而不是 npm 标签。*