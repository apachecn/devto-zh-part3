# 一个更轻松的主题

> 原文：<https://dev.to/wuz/a-lighter-devto-theme-3hh5>

我最近在《开发与回购评论》上看到了这个问题:

# [![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)  UI 对比和绘制注意事项 #1470](https://github.com/thepracticaldev/dev.to/issues/1470) 

[![vladinator1000 avatar](img/5625e626efaa9f3eadd000b598493b9e.png)](https://github.com/vladinator1000) **[vladinator1000](https://github.com/vladinator1000)** posted on [<time datetime="2019-01-06T16:11:29Z">Jan 06, 2019</time>](https://github.com/thepracticaldev/dev.to/issues/1470)

**您的功能请求是否与某个问题相关？请描述一下。**文章视图很难阅读，因为用户界面让你向右看，而不是看文章: [![example of box-shadow being too distracting](img/983aba6ccdcd4673412dfc5ff4dd2421.png)](https://user-images.githubusercontent.com/1226564/50738262-cef87980-11c9-11e9-812c-c111ec1c0ed2.jpg)

因为 dev.to 是关于阅读文章的，所以文章应该吸引最多的注意力。方框阴影非常华丽，但是当有大量文本需要阅读时，很快就会让人感到疲劳。

**描述您想要的解决方案**我们可以通过调整颜色值来加强视觉层次，让眼睛看起来更轻松，这样用户就不必费力地将目光从右边移开(像我一样)。基本上，最重要的区域(文章)对比度最高，第二重要的区域对比度较低(黑色会变成灰色)，依此类推...

下面是我在浏览器检查器中想到的，我不小心把文章的文本弄成了灰色，它应该是黑色的: [![reduced contrast of less important elements](img/1c00fd5fd8bd2c1200109c5eaa8bc796.png)](https://user-images.githubusercontent.com/1226564/50738528-eab14f00-11cc-11e9-85b7-7126e8f5683a.png)

**描述你考虑过的替代方案**也可以降低每一个不太重要的元素的颜色值，比如文本，而不仅仅是方框阴影，这样文章的字体会更黑，从而比不太重要的内容有更多的对比度。

**附加背景**这种推理根植于视觉设计原则，参见本[风格指南](https://nexus.leagueoflegends.com/wp-content/uploads/2017/10/VFX_Styleguide_final_public_hidpjqwx7lqyx0pjj3ss.pdf)的第 3 节“价值”。

为特定元素设置值范围的层次结构可以提高可读性和可访问性。当我第一次看到 dev.to 时，我非常喜欢大胆的方框阴影，我认为它增加了个性。但是随着时间的推移，我很自然地求助于 Firefox 阅读器视图，因为文章视图对我的眼睛来说太累了。

有一个类似#1273 的问题与此相关，但我认为它在大背景下没有意义。整体体验应该是非 straneous，以便用户可以阅读更多的文章，只有在完成这一点后，人们才应该考虑如何获得更好的灯塔评分。

[View on GitHub](https://github.com/thepracticaldev/dev.to/issues/1470)

我想我应该使用[手写笔](https://github.com/openstyles/stylus)扩展来稍微简化一下文章主题。这里是我结束的地方:

[![screenshot](img/8a346deca6474dddf899c55ac63cbfe2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9pIB75R5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v2rebirvofsr62yz9hjo.png)

如果你喜欢这个，想装，可以[在这里找到](https://userstyles.org/styles/167958/dev-to-simple-style)！

你怎么想呢?你会改变什么？