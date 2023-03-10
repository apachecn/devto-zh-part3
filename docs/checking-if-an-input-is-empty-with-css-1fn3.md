# 用 CSS 检查输入是否为空

> 原文：<https://dev.to/zellwk/checking-if-an-input-is-empty-with-css-1fn3>

只有 CSS 有可能知道一个输入是否为空吗？

当我试图为学习 JavaScript 创建一个自动完成组件时，我就有这个问题。基本上，我想:

1.  如果输入为空，隐藏下拉列表
2.  如果输入已填写，则显示下拉列表

[![autocomplete demo from https://learnjavascript.today](img/0e10f0f79fde7f0ce3045565d09d97c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cayu69fV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/autocomplete.gif)

我找到了做这件事的方法。它并不完美。这里面有一些细微的差别，但是我想和你分享一下。

## 形式

首先，让我们构建一个表单，这样我们就在同一页上了。我们将使用一个只有一个输入的简单表单。

```
<form>
  <label for="input"> Input </label>
  <input type="text" id="input" />
</form> 
```

输入完成后，我们希望将其`border-color`更改为绿色。下面是我们正在创建的示例:

[![when input is filled, borders should turn green](img/dcdfa394fbf7eb09f50f97e542f2759d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eVr-keZe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check.gif)

## 检查输入是否为空

我依靠 HTML 表单验证来检查输入是否为空。这意味着我需要一个`required`属性。

```
<form>
  <label> Input </label>
  <input type="text" name="input" id="input" required />
</form> 
```

此时，当输入被填充时，它工作得很好。边框变成了绿色。

[![borders turned green when input is filled](img/dcdfa394fbf7eb09f50f97e542f2759d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eVr-keZe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check.gif)

但是有一个问题:如果用户在字段中输入空白，边框也会变成绿色。

[![Borders turn green even if user enters a whitespace](img/d903f81726400b74a5f6338761d680f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h5bqOjDI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check-whitespace.gif)

从技术上讲，这是正确的。输入被填充，因为用户在其中输入了一些内容。

但是我不想让空白触发一个空白的下拉菜单(对于自动完成组件)。

这还不够。我需要更严格的检查。

## 进一步检查

HTML 让您能够使用带有`pattern`属性的正则表达式来验证输入。我决定测试一下。

因为我不想让空白被识别，所以我从`\S+`模式开始。这种模式意味着:一个或多个不是空格的字符。

```
<form>
  <label> Input </label>
  <input type="text" name="input" id="input" required pattern="\S+"/>
</form> 
```

果然有效。如果用户在字段中输入空格，输入不会得到验证。

[![Input doesn't get validated when whitespaces are entered](img/389c8a6b0b6209ba72ea9ee4f5f59f8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MX1ols5I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check-pattern-pre1.gif)

但是当在输入中(任何地方)输入空格时，输入就会失效。

[![Borders turned from greet to black when a whitespace is added.](img/087e5472512a08069bad03a9548be479.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8PxVIgq---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check-pattern1.gif)

不幸的是，这种模式在我的用例中不起作用。

在学习 JavaScript 自动完成组件时，我教学生如何完成国家列表。一些国家的名字有空格...

[![Dropdown contains countries with names that have spaces in them. For example, United States.](img/0e10f0f79fde7f0ce3045565d09d97c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cayu69fV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/autocomplete.gif)

我必须在组合中包含空白。

我能想到的下一个最佳选择是`\S+.*`。这意味着一个或多个非空白字符，后跟零个或多个(任意)字符。

```
<form>
  <label> Input </label>
  <input type="text" name="input" id="input" required pattern="\S+.*"/>
</form> 
```

这成功了！我现在可以在组合中输入空格了！

[![Borders remained green when whitespace is added in the middle of the input](img/05fbc3681402bfa6cb3fa53fe21fe83b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H8tFv9EJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check-pattern2.gif)

但是还有一个问题...如果以空格开头，输入不会验证...

[![Borders turned black when whitespace is added to the start of input](img/3a807c15ed67919fbda2b8e0113e97d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i-iqA2T---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://zellwk.cimg/2018/empty-input-validation-css/check-pattern3.gif)

这就是我无法解决的问题。稍后将详细介绍。

在撰写本文时，我遇到了另一个有趣的问题:当输入填充不正确时，是否有可能设计无效状态的样式？

## 使输入无效

我们不想使用`:invalid`,因为我们会用一个无效的状态启动输入。(当输入为空时，它已经无效)。

这就是 Chris Coyier 用“[HTML 和 CSS 中的表单验证 UX](https://css-tricks.com/form-validation-ux-html-css/)”来拯救我们的地方。

在文章中，Chris 谈到了一个`:placeholder-shown`伪类。它可用于检查是否显示了占位符。

这个想法是:

1.  您可以在输入中添加一个占位符
2.  如果输入是隐藏的，这意味着用户在字段中输入了一些内容
3.  继续验证(或无效)

下面是 CSS(简化版。完整版本，请查看克里斯的文章

```
/* Show red borders when filled, but invalid */
input:not(:placeholder-shown) {
  border-color: hsl(0, 76%, 50%);
} 
```

因为我有验证和无效样式，所以我必须确保有效样式在无效样式之后。

```
/* Show red borders when filled, but invalid */
input:not(:placeholder-shown) {
  border-color: hsl(0, 76%, 50%);;
}

/* Show green borders when valid */
input:valid {
  border-color: hsl(120, 76%, 50%);
} 
```

这里有一个演示供您使用:

见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的笔[纯 CSS 空验证](https://codepen.io/zellwk/pen/dgEKxX/)。

注意:Edge 不支持`:placeholder-shown`，所以现在还不太可能用在生产中。没有好的方法来检测这个特性。

现在回到我无法解决的问题。

## 格局问题

属性很棒，因为它允许您接受一个正则表达式。这个正则表达式允许您用您能想到的任何东西来验证输入。

但是...**正则表达式必须与文本完全匹配**。如果文本没有完全匹配，输入就会失效。

这就产生了我上面提到的问题。(问题提醒:如果用户先输入空格，输入将无效)。

我找不到一个适用于我想到的所有用例的正则表达式。如果您想尝试创建我需要的正则表达式，我非常欢迎您的帮助！

以下是使用案例:

```
// Should not match
''
'  '
'  '
'  '

// Should match
'one-word'
'one-word '
' one-word'
' one-word '
'one phrase with whitespace'
'one phrase with whitespace '
' one phrase with whitespace'
' one phrase with whitespace ' 
```

(话说回来，我可能想多了...🙄).

## 更新:问题解决！

许多读者非常慷慨地把他们的解决方案发邮件给我。我想感谢所有帮助过我的人。非常感谢！

我收到的最干净的解决方案是:[丹尼尔·奥康纳](https://www.nvinteractive.com)。这意味着:

*   `.*`:任意字符
*   `\S`:跟随*一个*非空白字符
*   `.*`:后跟任意字符

我收到的其他正则表达式包括:

*   `.*\S+.*`由[马特貂](https://twitter.com/matthewjmink)制作。
*   `\s*\S.*`作者[赵松斌](https://github.com/pcr910303)
*   `^\s?(?=\S).`由[康斯坦丁](https://twitter.com/KonstantinRouda)进行前瞻

还有很多其他的！

这里有一个由丹尼尔更新解决方案的代码笔:

见 [CodePen](https://codepen.io) 上 Zell Liew ( [@zellwk](https://codepen.io/zellwk) )的笔[纯 CSS 空验证](https://codepen.io/zellwk/pen/NeRaPw/)。

## 包装完毕

是的，用纯 CSS 验证表单是可能的，但是当涉及到空白字符时，验证就有潜在的问题。

如果你不介意留白，那就完美了。体验这种模式的乐趣吧！(不好意思，没办法)。

* * *

感谢阅读。这篇文章最初发表在我的博客上。如果你想要更多的文章来帮助你成为一个更好的前端开发者，请注册[我的时事通讯](https://zellwk.com)。