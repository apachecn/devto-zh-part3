# 随机颜色用一行 js 代码生成随机颜色

> 原文：<https://dev.to/akhil_001/generating-random-color-with-single-line-of-js-code-fhj>

你是否曾经感到无聊或厌倦，为不同的 div 或跨度编写长的随机颜色，只是为了测试一些简单的东西？
[![bored](img/5556ac997b757f2327aa5acd23fcbfeb.png)T3】](https://i.giphy.com/media/3o7TKEP6YngkCKFofC/giphy.gif)

这里有一个简单的解决方案。以下代码片段以十六进制格式生成随机颜色。

```
var randomColor = '#'+Math.floor(Math.random()*16777215).toString(16);
//generates a random color -> #56eec7 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！🥳你可以把它放在一个函数中，每次你需要一个随机颜色的时候就调用这个函数

```
function generateRandomColor()
{
    var randomColor = '#'+Math.floor(Math.random()*16777215).toString(16);
    return randomColor;
    //random color will be freshly served
}
document.body.style.backgroundColor = generateRandomColor() // -> #e1ac94
someDiv.style.color = generateRandomColor() // -> #34c7aa 
```

Enter fullscreen mode Exit fullscreen mode

这是魔术的结尾。如果你赶时间，你可以在这里留言。

* * *

# 但是，

if you are curious to know why only the number 16777215 and toString(16) are used, then the following part covers those explanations

所以，让我们把代码分成 3 个部分

### 1。为什么是数字`16777215`？

*   这需要一点数学知识。我们都知道颜色范围从`#000000`(漆黑)到`#ffffff`(纯白)。
*   根据 rgb 值从黑色到白色存在的颜色数量为`16777216`。
*   这可以简单地通过使用排列组合公式`[result = m to the power of n => 16 to power of 6 => 16777216]`来计算
*   然而，我们的最终目标是将数字转换成十六进制格式，`16777216`转换成`1000000`，`16777215`转换成`ffffff`。因此我们用 **167777215** 作为十六进制转换的最高数字

### 2。随机性

*   由于我们的输出需要一些随机性，我们将幻数乘以`Math.random()`,返回范围从 0 到 1 的浮点数

```
Math.random()*16777215
//->9653486.355498075 
```

Enter fullscreen mode Exit fullscreen mode

*   正如所看到的，输出是浮点型的，我们需要将它削减为一个整数进行十六进制转换，因此我们使用`Math.floor()`来实现这一点

```
Math.floor(Math.random()*16777215)
//->96953486 
```

Enter fullscreen mode Exit fullscreen mode

### 3。十六进制转换

*   现在我们到了最后阶段，代码的最后一部分。要将一个数字转换成十六进制格式的字符串，我们有一个漂亮的方法`toString()`,它接受告诉它必须转换成哪种格式的数字。
*   因为我们要转换成六进制格式的字符串，所以我们传递 16 作为参数，如下所示

```
(96953486).toString(16)
//->934cee
Math.floor(Math.random()*16777215).toString(16);
//->12ef556 
```

Enter fullscreen mode Exit fullscreen mode

```
- All we need to now is just attach # before the string 
```

Enter fullscreen mode Exit fullscreen mode

```
var randomColor = '#'+Math.floor(Math.random()*16777215).toString(16);
//->#19feac 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！向你致敬！
您已成功完成
帖子*祝您快乐！*