# Changelog:新的 SlideShare 液体标签

> 原文：<https://dev.to/link2twenty/changelog-new-slideshare-liquid-tag-d0i>

几天前,@ice-lenor 提出了一个问题，要求能够嵌入 SlideShare 演示。

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) 特征请求:嵌入 SlideShare  #1483](https://github.com/thepracticaldev/dev.to/issues/1483) 

[![ice-lenor avatar](img/a6cbe295aa7eea5c2a23ea1d2599a33f.png)](https://github.com/ice-lenor) **[ice-lenor](https://github.com/ice-lenor)** posted on [<time datetime="2019-01-08T00:30:19Z">Jan 08, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1483)

我似乎无法从 Slideshare 嵌入幻灯片。我尝试了直接链接到幻灯片(这在 WordPress 中有效)和来自 slideshare 的 html 的“嵌入”部分。

因此，我认为这将是一个很好的可能性，将幻灯片插入到开发人员发布。

非常感谢！

* * *

我想分享的一个演示示例:[https://www . slide share . net/elenanikolaeva 9/geo-crash-course-127477385](https://www.slideshare.net/ElenaNikolaeva9/geo-crash-course-127477385)

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1483)

像这样的问题是很棒的，你只需说出你想要什么，任何人都可以拿起它，用它运行。

为了嵌入一个 SlideShare，你需要 slides 密钥，这是嵌入的 src URL 的最后一部分，你可以在 Share 面板中看到。

```
<iframe src="//www.slideshare.net/slideshow/embed_code/key/wZudqqTdctjWXA" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/carologic/ai-and-machine-learning-demystified-by-carol-smith-at-midwest-ux-2017" title="AI and Machine Learning Demystified by Carol Smith at Midwest UX 2017" target="_blank">AI and Machine Learning Demystified by Carol Smith at Midwest UX 2017</a> </strong> de <strong><a href="https://www.slideshare.net/carologic" target="_blank">Carol Smith</a></strong> </div> 
```

Enter fullscreen mode Exit fullscreen mode

那你就把它的标签加上去，就像这样`{% slideshare wZudqqTdctjWXA %}`
[https://www.slideshare.net/slideshow/embed_code/key/wZudqqTdctjWXA](https://www.slideshare.net/slideshow/embed_code/key/wZudqqTdctjWXA)