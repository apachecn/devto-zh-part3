# SVG 继承颜色

> 原文：<https://dev.to/paulryan7/svg-inherit-color-34f3>

## 走吧

对你们来说这是一篇很好的短文，但我认为这将是一篇有益的文章。

想象我们有这样一个按钮:
[![Picture of button](img/ad882a2104a618e3b7ea00e9a7c57467.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hXQx_ik_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xcdeh95bor4rxc7bl16x.png)

悬停此按钮时，我们会将文本的`background`更改为`blue`并将文本的`color`更改为`white`。
[![Hover of Button](img/ebb6fd7c3b03d2baa40a9e4007f1a50c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hnTaPYjl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m4pfpg560t06oy8ju33z.gif)

如你所见，我们的 SVG 保持相同的颜色，谢天谢地，我们可以让我们的 SVG 继承按钮文本的颜色。

我们这样做:

```

     <circle cx="50" cy="50" r="40" stroke="black" stroke-width="3" fill="currentColor" />
 
```

我们给`circle fill`赋值`currentColor`,它将使用我们设置的文本颜色。

所以现在悬停上我们有:
[![Hover Done Right](img/e7ec1e17c22e64321b5cf5dd0a06706a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kPwWxVgF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/72v04okn4j6nco4vd9yk.gif)

这就是所有的人，希望你学到了一些东西。

以下是所有代码的 code pen:
[https://codepen.io/PaulRyan17/embed/JqWgbx?height=600&default-tab=result&embed-version=2](https://codepen.io/PaulRyan17/embed/JqWgbx?height=600&default-tab=result&embed-version=2)