# 神奇的 handleEvent 函数

> 原文：<https://dev.to/rikschennink/the-fantastically-magical-handleevent-function-1bp4>

我们来看看吧！

[https://codepen.io/rikschennink/embed/wNzQYg?height=600&default-tab=js,result&embed-version=2](https://codepen.io/rikschennink/embed/wNzQYg?height=600&default-tab=js,result&embed-version=2)

我记得几年前当我发现这件事的时候我的反应，而且我仍然留着大胡子。

[![A man with a mustache saying: What the fuck?!?](img/e2e5c50361917dfe5d63e91031349c8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vJ4Rsqxn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pojqauoyf4pk57oyz429.gif)

没错，这是可行的，这很奇怪，但确实如此。

如果你设置一个对象作为`addEventListener`的处理程序，并且这个对象有一个名为`handleEvent`的属性，并且这个属性的类型是`function`，那么`event`会被自动发送到那里。

这样做的原因(在所有浏览器上都有效):

> 与遗留内容兼容的需求要求`EventListener`接受带有`handleEvent()`属性函数的`function`和`object`。

[MDN 上的 event listener](https://developer.mozilla.org/en-US/docs/Web/API/EventListener)

关于*这个*的另一件好事是**这个**保持`this`工作。

[https://codepen.io/rikschennink/embed/PVGgYW?height=600&default-tab=js,result&embed-version=2](https://codepen.io/rikschennink/embed/PVGgYW?height=600&default-tab=js,result&embed-version=2)

所以，不需要`bind(this)`来确保你在正确的范围内结束。此外，使用`removeEventListener('click', this)`可以轻松删除事件

您可以使用单个`handleEvent`函数来路由各种事件，而不是使用不同的函数来处理事件。

```
class MyClass {

  constructor() {

    // Get a reference to the button node
    const btn = document.querySelector('button');
    const input = document.querySelector('input');

    // Handle clicks by this
    btn.addEventListener('click', this);

    // Handle input by this
    input.addEventListener('input', this);

  }

  handleEvent(event) {
    if (event.type === 'click') {
      // handle button clicks 
    }
    else if (event.type === 'input') {
      // handle text input
    }
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

我希望这能给你带来一些有趣的新想法！