# CSS 样式的 Console.log

> 原文：<https://dev.to/annlin/consolelog-with-css-style-1mmp>

我记得当我年轻和天真的时候，我不小心右键点击了脸书浏览器，看到了这个巨大的屁股警告。我觉得很可爱。猜猜谁也有同样的警告。(以加号结尾的东西)

[![Facebook's warning log](img/c70a88b2226852f05ce8bb3257262b01.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a53UVsZU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xc10k7gmmtoamltpqhvt.png)

剧透警报，还在。

大多数现代浏览器都支持`console.log()`的样式，除了 Safari。(Whut)在第一个参数中的文本之前添加`%c`，在下一个参数中添加 CSS 样式。

```
 console.log(
        "%cStop!",
        "color:red;font-family:system-ui;font-size:4rem;-webkit-text-stroke: 1px black;font-weight:bold"
      ); 
```

**提示**:右键点击该页面，点击 inspect。现在将上面的代码粘贴到控制台中。
**另一个提示**:这个教程是为了给 noob 留下深刻印象。如果你已经知道这一点，继续你的生活。

通过使用下面的格式，你可以在一个`console.log()`中拥有多个 CSS 样式。

```
console.log('%cb %cb ', 'color:blue;border:1px solid black', 'color:black;border:1px solid black'); 
```

你也许可以在`Console`中使用 CSS 样式和其他方法。虽然我仍然喜欢在我的代码中误用旧的`console.log`，但是正确的方法应该是使用像`https://www.npmjs.com/package/debug`这样的调试模块，它可以让你很容易地显示和隐藏日志。

## 演示、

[https://codepen.io/linxea/embed/OdgGqP?height=600&default-tab=result&embed-version=2](https://codepen.io/linxea/embed/OdgGqP?height=600&default-tab=result&embed-version=2)
在此页面点击右键可以看到 console.log()。

## 问题，

`%c`中的`c`代表什么？
a. CSS
b .习俗
c .人物
d .以上都不是

参考:
[https://developer.mozilla.org/en-US/docs/Web/API/console](https://developer.mozilla.org/en-US/docs/Web/API/console)

## 新的一天新的视频，

[https://www.youtube.com/embed/FU7aRSfrR_M](https://www.youtube.com/embed/FU7aRSfrR_M)