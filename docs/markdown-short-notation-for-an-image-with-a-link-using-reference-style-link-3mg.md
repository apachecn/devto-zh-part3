# [Markdown]带有使用引用样式链接的链接的图像的简短符号

> 原文：<https://dev.to/tomoyukiaota/markdown-short-notation-for-an-image-with-a-link-using-reference-style-link-3mg>

###### (此处有日文翻译[。)](https://qiita.com/TomoyukiAota/items/193cdf4890dbb0e8bf49)

# 简介

在 Markdown 中，当我们想把一个图像和一个链接放在一起时，符号往往很长。

例如，Travis CI 的状态徽章如下所示:

[![Build Status](img/beb9c4803e01017c42497ea03ad6c591.png)](https://travis-ci.org/travis-ci/travis-web)T3】

```
[![Build Status](https://travis-ci.org/travis-ci/travis-web.svg?branch=master)](https://travis-ci.org/travis-ci/travis-web) 
```

(此徽章是在 Travis CI 上[嵌入状态图像](https://docs.travis-ci.com/user/status-images/)的一个例子。)

在表格中，这样冗长的符号会有问题，就像这样:

| 操作系统（Operating System） | 状态 |
| --- | --- |
| 人的本质 | [![Build Status](img/beb9c4803e01017c42497ea03ad6c591.png)](https://travis-ci.org/travis-ci/travis-web) |

```
| OS     | Status |
|:------:|:------:|
| Ubuntu | [![Build Status](https://travis-ci.org/travis-ci/travis-web.svg?branch=master)](https://travis-ci.org/travis-ci/travis-web) | 
```

上表只有一个徽章。当我们想把几个徽章排成一行时，符号变得非常复杂，以至于很难识别列分隔符`|`。比如 srz-zumix/iutest 的 [README.md 有一个表格，上面有很多徽章，它们的](https://github.com/srz-zumix/iutest/blob/master/README.md)[原始内容](https://raw.githubusercontent.com/srz-zumix/iutest/master/README.md)看起来非常复杂。

我们能以某种方式得到更短的符号吗？

# 解

[引用式链接](https://www.markdownguide.org/basic-syntax/#reference-style-links)有用。对于表，会是这样:

```
| OS     | Status |
|:------:|:------:|
| Ubuntu | [![travisci-image]][travisci-link] | 
```

`travisci-image`和`travisci-link`也需要在文档中定义，比如:

```
[travisci-image]: https://travis-ci.org/travis-ci/travis-web.svg?branch=master 
```

```
[travisci-link]: https://travis-ci.org/travis-ci/travis-web 
```

有了这个简短的徽章符号，即使我们将几个徽章排成一行，表格的符号也是可管理的。

`travisci-image`和`travisci-link`是任意的字符串，所以我们可以做任何我们想做的事情，比如给它们起一个容易理解的名字，调整名字的长度，这样列分隔符`|`就可以跨行对齐，等等。

最后，我创建了一个示例 GitHub Gist 。点击“原始”按钮，看看这个符号实际工作！