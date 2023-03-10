# JavaScript 中的引用

> 原文：<https://dev.to/karataev/references-in-javascript-ojf>

我从事 JavaScript 工作已经 4 年了，仍然会遇到有趣的场景。下面的代码是我最近在日常工作中遇到的问题的简化示例。

```
let color = {value: 'brown'};

let cat = {color: color};

color = {value: 'white'};

console.log(cat); // ? 
```

Enter fullscreen mode Exit fullscreen mode

花点时间，阅读代码，想想`console.log`的输出会是什么🤔。

答案是

```
{
  color: {value: 'brown'}
} 
```

Enter fullscreen mode Exit fullscreen mode

等等。我们知道在 JS 中，非原语变量是通过引用传递的。但是很明显`cat`对象在`color`变量修改后没有变化。

这是怎么回事？让我们一步一步地分析代码。

首先，我们声明变量`color`，创建一个新对象`{value: 'brown}`，并将该对象赋给`color`。

之后，新对象`{color: color}`被创建，并被分配给新变量`cat`。

最后，我们又创建了一个对象`{value: 'white'}`，并将其赋给现有的变量`color`。

问题是:为什么对象`cat`没变？

❗在 JavaScript 中，变量不可能引用其他变量。其他语言可以，JS 不行。

执行完第二行后，`cat.color`引用的是`{value: 'brown'}`对象，而不是`color`变量。这就是为什么给`color`变量分配一个新对象不会改变`cat`对象。

希望这有意义。保持好奇，继续编码！😎

附:自测。如果我们稍微修改一下代码
，输出会是什么

```
let color = {value: 'brown'};

let cat = {color: color};

color.value = 'white';

console.log(cat); // ? 
```

Enter fullscreen mode Exit fullscreen mode