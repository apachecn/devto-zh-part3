# 辅助项目-博客上的 CSS

> 原文：<https://dev.to/krzysztofzuraw/side-project-css-on-blog-468p>

**注:这是来自** [我的简讯](https://krzysztofzuraw.com/newsletter)的交叉帖子。**每封邮件发出后，我都会公布。** [订阅](https://buttondown.email/krzysztof_zuraw) **以便在您的收件箱中更早获得更多类似内容！📧。**

几周后欢迎回来👋🏻。这是波兰的一个小假期，所以我抓住这个机会，暂停写作。

今天我想写一下我最近做的一个副业，以及我是如何对待副业的。我也将写关于估计和测量我在项目上花费的时间。我想提到的最后一件事是为你的工作收集反馈。

旁注:这篇博文需要一些 CSS 和 HTML 知识，所以请记住这一点。

## 问题

最近，我决定重新设计我的博客,让它看起来更简约。我部分成功了，因为它看起来像这样:
[![Imgur](img/6bf2c8ba00e26e30500c93b496fe06b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vUOs65hG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TkHFAHa.png)

这里的主要问题是有些东西看起来不太对劲——这是我的一个同事在工作中指出来的。阅读文本太麻烦了。

我正在寻找一个解决方案，我在一些黑客新闻上发现了这个[链接](https://jgthms.com/web-design-in-4-minutes/)。这个网站叫做“4 分钟网页设计”。所以我花了 4 分钟浏览了网站。

事实证明，我可以通过删除几行代码来改进我的博客设计。我知道我必须做什么——现在我必须开始编码。

## 侧项目

在开始写代码之前，我将修复我的博客作为一个辅助项目。这是什么意思？

这意味着在开始时，我使用思维导图工具来勾画一个附带项目的想法。我问自己需要做什么，为什么需要做，怎么做。我还加了一个时间估计。由于这一点，我将知道我对工作量的估计是否正确。你可以在下面找到那个思维导图的截图:
[![Imgur](img/54d5067e3be6fcba6aa45272954ba48b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vs0LZUHA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/r5PapA0.png)

我正在使用[思维节点](https://mindnode.com/)进行思维导图。作为最后一步，我将 todos 导出到[的东西](https://culturedcode.com/things/)。

## 解

我用来修复网站的代码是什么？你可以在下面找到:

```
body {
  margin: '0 auto';
  max-width: 50em;
  line-height: 1.5;
  padding: '3em 1em';
  color: '#555';
} 
```

### 有什么帮助？

它通过一个`margin`将我的上下文置于屏幕中央。另一个改进是`padding`标题没有粘在屏幕顶部。我添加的最后一个东西是`color`,它没有默认为白色——这对于阅读来说不是最好的。

经过这些改变后，我的博客看起来是这样的:
[![Imgur](img/6bf2c8ba00e26e30500c93b496fe06b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vUOs65hG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/TkHFAHa.png)

## 总结&TL；速度三角形定位法(dead reckoning)

通过在 4 分钟内阅读[网页设计](https://jgthms.com/web-design-in-4-minutes/)，我修复了我的博客的布局问题。我了解到应用少量的 CSS 会有所帮助。此外，我比我的估计要好——这花了我一个小时，而不是三个小时。
你呢？你是怎么做你的副业的？你从他们身上学到了什么？不要犹豫，通过电子邮件给我写信。