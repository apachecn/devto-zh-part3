# 小细节决定大不同

> 原文：<https://dev.to/lostdesign/small-details-make-a-difference-1k4a>

最近，我和我的 web 开发同事朋友聊天，讨论表单，以及如果必填字段丢失，他们不应该让用户提交请求。我试图满足我今年圣诞假期的要求，但是他们没有任何确认。我最后又添加了同样的数据😔..

# 那么这有什么关系呢？

#### 表单验证很重要！

您有多经常遇到这样的问题:填写没有任何前端验证的表单，甚至可能不显示错误所在或根本不起作用？我猜你很了解这种感觉！

这里的问题是，用户通常认为一旦他们填写完表单，一切都会好的，但是然后一切都消失了。你现在有一个失望甚至愤怒的顾客。至少那个网站让这只可怜的猫哭了。

[![really sad cat](img/33aae010b73b7312cce2c0d1cc9959da.png)](https://i.giphy.com/media/71PLYtZUiPRg4/giphy.gif)

# 你如何解决这个问题？

让我们在输入上使用`required`!

耶 -事实上没有..

你*能*做到，但还是没有在合适的时间给用户反馈！我们现在在同一点上，用户假设他填写的数据是正确的。

那你该怎么办呢？你可以安装所有漂亮的 js 库来让它工作*或者*简单地使用普通的`JS`和`CSS`。有时候你可能不需要比你在下面的代码栏中看到的更多的东西！

我做了这个代码笔作为概念的证明，试着不输入任何值就点击按钮。然后点击输入，添加一些东西并点击提交。

[https://codepen.io/lost-design/embed/BGeyEr/?height=600&default-tab=result&embed-version=2](https://codepen.io/lost-design/embed/BGeyEr/?height=600&default-tab=result&embed-version=2)

#### 钢笔的工作原理

我在`mouseenter`的按钮上添加了一个`addEventListener`，它将触发一个叫做`validateNote()`的功能。

```
btn.addEventListener('mouseenter', validateNote) 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，`mouseenter`可能是最好的选择，因为一旦鼠标进入按钮，它就会被触发(点击阅读更多关于事件触发器的信息)

`validateNote()`使用三元运算符检查输入值是否为空字符串。根据是否有任何值，它将禁用或启用按钮。

```
input.value === "" ? disableButton() : enableButton() 
```

Enter fullscreen mode Exit fullscreen mode

`disableButton()`将`.err` css 类添加到按钮、输入和标签中，以显示错误信息。

```
function disableButton() {
    btn.classList.add('err');
    input.classList.add('err');
    errMsg.classList.add('err');

    // this kicks the cursor from the input which still has focus
    document.activeElement.blur();

    // the button here will be disabled
    btn.removeEventListener('click', submitNote, false);
} 
```

Enter fullscreen mode Exit fullscreen mode

`enableButton()`明显会反其道而行之！它从按钮、输入和标签中移除了`.err` css 类来隐藏错误消息。

```
function enableButton() {
    btn.classList.remove('err');
    input.classList.remove('err');
    errMsg.classList.remove('err');

    btn.addEventListener('click', submitNote, true);
} 
```

Enter fullscreen mode Exit fullscreen mode

专业提示:在输入上使用一个内阴影来防止它改变大小，因为添加一个边框会使输入变得更大，你的布局会开始移动。

```
input.err {
    -webkit-box-shadow:inset 0px 0px 0px 2px #f00;
    -moz-box-shadow:inset 0px 0px 0px 2px #f00;
    box-shadow:inset 0px 0px 0px 2px #f00;
    transition: all .3s ease-in-out;
} 
```

Enter fullscreen mode Exit fullscreen mode

另一个提示:因为我们在这里使用动画，使用任何你喜欢的放松方法——至少使用任何一种。找到一些缓和属性[在这里](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Transitions/Using_CSS_transitions#Defining_transitions)

顺便说一句，你应该为你的输入添加随时可见的标签——在这个例子中我没有这样做。但这是一个不同的话题，我将在其他时间讨论。

# Tl；博士；医生

只需在之前验证所有必需的输入**，用户可以在前端提交它们，但也可以在用户完成填充数据后提交，以防止任何中断。**

它有助于用户的体验，以防止任何错误的期望，然后导致挫折。关键是在正确的时间告诉用户哪里出了问题，而不要扰乱他们的流程！

错误消息

*   应该在正确的时间和地点显示。
*   应该主要避免混淆。
*   应该传达正在发生的事情。
*   应该描述用户如何修复它。
*   应该简短，但有意义。
*   信息应该有正确的颜色。

*摘自 [UX 证券交易所](https://ux.stackexchange.com/questions/120939/inline-validation-on-smaller-forms)T3】*

这是我试图找出如何用小细节做出大改进的开始，敬请关注！

我总是乐于提高自己，所以请随意帮忙！感谢已经帮助我改进这篇文章的不和谐的人们