# 在你的 Gatsby 站点中可视化滚动位置

> 原文：<https://dev.to/barrymcgee/visualising-scroll-position-in-your-gatsby-site-2ocm>

今年年初，我用[盖茨比](https://www.gatsbyjs.org/)重建了我的博客。

作为一个静态站点生成器，Gatsby 与众不同的突出特点之一是[插件生态系统](https://www.gatsbyjs.org/plugins/)。如果别人已经设计了一个非常好的轮子，为什么还要重新发明轮子呢？

web 开发的美妙之处在于，我们可以轻松地越过他人的肩膀，很可能是在世界的另一端，并快速向他们学习。

在一个设计良好的插件生态系统中工作，我们不仅可以向其他开发者学习，还可以通过快速采用他们的工作并将其添加到我们自己的项目中来加速我们自己的开发，从而做出有望超过其部分总和的东西。

我天生是一个没有耐心的人。你是否被困在火车或飞机上，不知道什么时候才能再次出行？这难道不令人愤怒吗？其他人[似乎也同意](https://www.theguardian.com/lifeandstyle/2017/aug/28/michelle-hanson-still-here-waiting-childcae)。

每当发生重大交通中断事件时，你会注意到晚间新闻中疲惫不堪的受访者抱怨的第一件事通常不是中断的原因，而是缺乏相关信息。

一点点信息能走很长很长的路。

你会注意到，当你向下滚动[我的博客](https://www.barrymcgee.co.uk)上的一篇文章时，你的进度会沿着屏幕顶部从左到右被跟踪。

这给了你一个直观的指示，告诉你离这一页结束还有多长时间。

在我们这个时间匮乏的数字世界里，我的理论是，这个小小的指示器更有可能让你一直读到最后(尽管我的写作质量很差)。)

我在我的博客上开发了这个小功能，看到它很好。然后我把它打包成一个 [Gatsby 插件](https://www.gatsbyjs.org/packages/gatsby-plugin-scroll-indicator/),供其他人在他们自己的 Gatsby 网站上获取和使用。

要将它添加到您自己的 Gatsby 站点，请使用 npm 安装；

`npm install --save gatsby-plugin-scroll-indicator`

...或者纱线...

`yarn add gatsby-plugin-scroll-indicator`

...然后将插件引用和配置选项添加到您的`gatsby-config.js`文件:

```
plugins: [
  {
    resolve: `gatsby-plugin-scroll-indicator`,
    options: {
      // Configure your indicator bar color here
      color: '#BADA55',
    },
  },
]; 
```

重新启动所有的东西，清除所有的缓存，你现在应该在你的网站上有一个可视化的滚动指示器。💥

源代码: [Github](https://github.com/barrymcgee/gatsby-plugin-scroll-indicator)