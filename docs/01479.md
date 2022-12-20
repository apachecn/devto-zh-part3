# 一个简单的 react a11y 表情组件

> 原文：<https://dev.to/flrnd/a-simple-react-a11y-emoji-component-dim>

表情符号很酷，对吧？每个人都有❤️表情符号，但屏幕读者对此有不同的看法。

对于那些还不知道的人来说，有两个属性对帮助屏幕阅读器非常有用:

`aria-label`给元素添加一个字符串标签，这在文本标签在屏幕上*不*可见的情况下很有用。

`aria-hidden` ( [来自 MDN](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_aria-hidden_attribute) ):

> 向元素添加 aria-hidden="true "会从可访问性树中删除该元素及其所有子元素。这可以通过隐藏以下内容来改善辅助技术用户的体验:
> 
> 纯装饰性的内容，如图标或图像。
> 重复的内容，如重复的文字。
> 屏幕外或折叠的内容，如菜单。

因此，这些属性对我们的表情符号组件来说是一个非常好的主意。让我们来看看它的作用:

```
import React from 'react';

const Emoji = ({label, symbol}) => (
  <span
    className="emoji"
    role="img"
    aria-label={label ? label : ''}
    aria-hidden={label ? 'false' : 'true'}
  >
    {symbol}
  </span> );

export default Emoji; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们没有传递标签道具，我们希望将 aria-hidden 设置为 true，这样屏幕阅读器就会忽略我们的表情符号。

还有一些使用例子:

```
<Emoji label="open book" symbol="📖" /> {timeToRead} min read 
```

Enter fullscreen mode Exit fullscreen mode

它会呈现这样的内容:

```
<span class="emoji" role="img" aria-label="open book" aria-hidden="false">📖</span>
2 min read
<!-- ... -> 
```

Enter fullscreen mode Exit fullscreen mode

这就是了！很有趣不是吗？
快乐的❤️☕️🐑🐧！

封面图片由[*Lidya Nada @ Unsplash*T3】](https://unsplash.com/@lidyanada)