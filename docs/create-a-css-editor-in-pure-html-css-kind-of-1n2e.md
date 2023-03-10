# HTML 的隐藏实时 CSS 编辑器

> 原文：<https://dev.to/michi/create-a-css-editor-in-pure-html-css-kind-of-1n2e>

荣誉归于

> ![unknown tweet media content](img/c78abba7710bc14684a8bd14977028c9.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1121054690295320577/pu/vid/740x432/71JRHRvdUI0bD-IE.mp4?tag=8" type="video/mp4"></video>![Caleb Porzio profile image](img/3da76004217b347847293ce5faafc654.png)calebporzio[@ calebporzio](https://dev.to/calebporzio)![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)🤡我刚刚发现在 HTML 中很酷的事情是可能的。没有实际用途，只是好玩。2019 年 4 月 24 日下午 14:14[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1121054728148926467)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1121054728148926467)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1121054728148926467)

如果您喜欢看到它和运行中的代码，请查看 codepen

[https://codepen.io/mzanggl/embed/KYLrwR?height=600&default-tab=html,result&embed-version=2](https://codepen.io/mzanggl/embed/KYLrwR?height=600&default-tab=html,result&embed-version=2)

对于那些现在不能看视频或玩笔的人。

首先创建以下标记

```
<style>
  html {
    background: blue;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

由于`<style>`只是另一个 HTML 标签，它遵循与其他 HTML 标签相同的规则。如果你检查 devtools 中的 CSS 规则，你会看到`display: none`被应用到了样式标签上。所以，让我们忽略它。

```
<style style="display: block;">
  html {
    background: blue;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

太好了，我们现在可以在页面上看到样式标签的内容了！

最后，让我们试着给它添加`contenteditable`属性。

```
<style style="display: block;" contenteditable>
  html {
    background: blue;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这将让我们在样式字段中键入内容。转折来了:当你打字的时候，样式会被重新评估！

在结果中，尝试将`background: blue;`改为`background: green;`，看看会发生什么。

* * *

看到这个之后，我立刻想到“那么脚本标签呢？”。

一切都是一样的，除了当你改变内容时，脚本不能被重新评估。

## 结论

这可能没有用例，但探索这类事情仍然很有趣。