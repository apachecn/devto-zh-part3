# 为了中间的一个微笑

> 原文：<https://dev.to/s1hofmann/for-a-little-smile-in-between-4002>

去年，我为同事们举办了一个关于艾滋病毒的研讨会。由于工作坊自然需要一些工作，我需要想出一个小项目，应该在一个下午就可以完成。

足够幸运的是，我发现 [xkcd](https://xkcd.com/) 也提供了一个 [API](https://xkcd.com/json.html) 。因此，我的目标是建立一个小的 XKCD 漫画浏览器来演示 web 清单、服务人员生命周期、数据获取、数据缓存和自动部署到 GitHub 页面。(由于我是 XKCD 的粉丝，我会为自己做一些有趣的事情！)

在准备研讨会时，我遇到了一些意想不到的问题，这些问题后来证明非常有用。这就是这篇文章的内容！:)

## 走，不跑

以下是我如何想象我的应用程序的内部工作方式:

1.  获取所有可用帖子的元数据
2.  使用数据总量，获取随机帖子的元数据
3.  获取文章数据(图像和文本)
4.  展示它

获取数据应该非常简单:

```
fetch("https://xkcd.com/info.0.json").then(...) 
```

Enter fullscreen mode Exit fullscreen mode

Oooops…

```
Access to fetch at 'https://xkcd.com/info.0.json' from origin 'http://localhost:3000' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled. 
```

Enter fullscreen mode Exit fullscreen mode

显然 XKCD API 没有发送 [CORS 头](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)，这个问题已经在[这篇文章](http://fora.xkcd.com/viewtopic.php?t=113315)中解决了，但是还没有改变。

这给我的工作室带来了一些问题:

1.  我将无法获取任何帖子元数据 <sup>1</sup>
2.  也不可能获取图像数据 <sup>1</sup>
3.  我仍然可以渲染东西，但这与我想要实现的目标无关:)

## 多走一英里

在关于 CORS 的论坛帖子之后，我开始寻找我的问题的现有解决方案，并发现了 XKCD API 的 CORS 版本。使用这个 API 而不是官方的 XKCD API 将允许我去掉我的开放问题列表中的两点:

*   ~~获取所有可用帖子的元数据~~
*   ~~使用总数据量，获取随机帖子的元数据~~
*   获取文章数据(图像和文本)

启用 CORS 的 API 允许我使用我的服务工作者获取元数据并缓存响应。

但是仍然存在一个问题:我如何能够获取图像数据并正确地缓存它？

在我的研讨会上，我想强调的一点是，有了服务人员，就有可能拦截和修改任何网络请求。我手头的问题就是一个很好的例子！我只需拦截传出的请求，检查它们是否试图从 XKCD 获取图像数据，并代理它们启用 CORS！

快速搜索让我找到了 [cors-anywhere](https://cors-anywhere.herokuapp.com) ，这是一项代理你的请求并返回带有 cors 标题的响应的服务。在将代理代码添加到我的服务工作者之后，我可以勾选列表的最后一点:

*   ~~获取所有可用帖子的元数据~~
*   ~~使用总数据量，获取随机帖子的元数据~~
*   ~~获取帖子数据(图片和文本)~~

## 买一送一

代理图像数据请求非常有效。我注意到的唯一一件事是，有时候，图像加载会相当慢。没什么大问题，但我对此不太高兴。

我开始寻找替代方案，但过了一会儿，我意识到一些重要的事情。我可以利用它的优势，而不是试图修复相当慢的数据加载。

> 我会构建一个小的 XKCD 漫画查看器来演示 web 清单、服务人员生命周期、数据获取、**数据缓存**和自动部署到 GitHub 页面。

当谈到数据缓存时，这又是一个完美的例子！最初的数据加载相当慢，但是一旦我们将响应添加到我们的动态缓存中，图像会在瞬间加载。

## 摇一摇！

实际上，我想在我的 workshop PWA 中添加一个“摇动刷新”特性，但是我没有时间实现它，而且它已经超出了 workshop 的范围。玩 web APIs 很有趣，所以我构建了一个小的[设备运动包](https://www.npmjs.com/package/device-motion)，并在我最近修改这个研讨会时添加了这个功能。如果你想在你的应用程序中使用设备运动，你可以看看它。

## 说够了，给我看看结果！

这里是[源](https://github.com/s1hofmann/xkcdpwa)，这里是部署的[应用](https://s1hofmann.github.io/xkcdpwa/)。

## 结论

我的工作室变得很棒！我真的很喜欢，根据我同事的反馈，他们也很喜欢！

尽管我不得不额外转弯，但看到这个结果作为我最初计划的工作室的一个更好的例子真的很酷。被踢出你的舒适区是值得的！:)

这就是工作坊的酷之处。通过准备，你可以学到很多东西，一旦你做好了，别人也会学到新的东西。因此，如果你有机会参加研讨会，我只能建议你参加。

真的是多方面的回报！

再见

西蒙

<sup>1</sup> 仍然可以使用`opaque response`来获取数据，但是不透明的响应不能使用服务工作者来缓存，所以我必须找到另一种方法。