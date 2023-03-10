# 眼睛跟随鼠标指针

> 原文：<https://dev.to/codeguppy/eyes-follow-the-mouse-pointer-5cl4>

# [T1】简介](#intro)

了解如何建立跟随鼠标光标的著名眼睛。

这是一个很棒的效果，在一点点三角学的帮助下，可以在 CodeGuppy 中轻松实现。

# 需要什么？

要运行本文中的代码，您不需要在本地计算机上安装任何东西。

只需从[codeguppy.com/code.html](https://codeguppy.com/code.html)打开在线 JavaScript playground，在集成编辑器中复制并粘贴以下代码。

准备就绪后，按“播放”按钮运行代码。

[![](img/5f7b6bdf397f644960c87a2ba957a4e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3PyKjlKK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8o0lfe2ouu7vlqn2klx.png)

# 源代码

```
// Use cartesian - polar coordinates conversion to implement
// eyes that follow mouse coursor

function loop()
{
    clear();

    drawEye(200, height / 2, 60);
    drawEye(380, height / 2, 60);
}

function drawEye(x, y, r)
{
    fill("white");
    ellipse(x, y, r * 2);

    drawEyePupil(x, y, 0.75 * r, 0.5 * r);
}

// Draw eyes that follow the mouse position
function drawEyePupil(x1, y1, r, pr)
{
    var angle = atan2(mouseY - y1, mouseX - x1);

    var x2 = x1 + r * cos(angle);
    var y2 = y1 + r * sin(angle);

    fill("black");
    ellipse(x2, y2, pr);
} 
```

# 反馈

如果你喜欢这篇文章，请在 Twitter 上关注 [@codeguppy](https://twitter.com/codeguppy) 和/或访问 codeguppy.com[获得更多教程和项目。](https://codeguppy.com)

另外，如果你想用详细的说明来扩展这篇文章，解释如何一步一步地构建程序，请在评论中留下反馈。