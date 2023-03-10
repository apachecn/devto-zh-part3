# 制作带有彩色底边的动画按钮

> 原文：<https://dev.to/ztickm/make-animated-buttons-with-colored-bottom-edges-1cip>

这是一篇入门级的文章，逐行解释了如何只用 CSS 和 HTML 制作简单好看的动画按钮。

首先，在代码中检查我们将要做什么:
[https://codepen.io/ztickm/embed/GxzQKx?height=600&default-tab=result&embed-version=2](https://codepen.io/ztickm/embed/GxzQKx?height=600&default-tab=result&embed-version=2)

### HTML

```
<button class="shadow-btn">
  Button
</button>
<button class="border-btn">
  Button
</button> 
```

Enter fullscreen mode Exit fullscreen mode

基本上，代码告诉浏览器显示两个按钮，并将每个按钮分配给一个不同的类，由 CSS 进行样式化。

**Q** :为什么不用`<input type="submit">`标签代替`<button>`？
**A** :对于我来说，我只是喜欢`<button>`的简洁，它默认其`type`为`submit`；当阅读一个充满其他`<input>`标签的长表格时，也更容易得到。
阅读关于此事的 StackOverflow [讨论](https://stackoverflow.com/questions/25436145/input-type-button-vs-button)。

### CSS

我们将逐个选择器地研究工作表。

#### -1:根

```
:root {
  --main-color: #16a085;
  --transition-time: 0.5s;
} 
```

Enter fullscreen mode Exit fullscreen mode

`:root`选择器是一个 CSS 伪类。当使用 HTML 时，它匹配`html`选择器，除了它有更高的特异性。
`--main-color``--transition-time`是 [CSS 自定义属性](https://www.w3.org/TR/css-variables/)我们可以自己命名。在这种情况下，我们将它们用作变量(是的，CSS 自定义属性并不等同于变量，稍后我会写一篇关于这一点的文章)。因为我们多次使用这些值，这有助于我们以后如果想要更改它们，我们将只更改它们一次，不需要 Ctrl+F CSS 来更改所有出现的值。

#### -2 按钮

```
button {
/* We're selecting every <button> in the document */

  margin: 1px;
  /* We're telling CSS to give 1 pixel of "room" in all directions*/

  border-radius: 0;
  /* We want our buttons to have sharp 90 degrees corners*/

  border: 0;
  /* We want our buttons to have no border, more on this later*/

  padding: 10px 30px;
  /* We want our text to have some space inside the button*/ 

  background: #414141;
  /* All buttons are to have this dark background*/

  color: white;
  /* Our text will be white*/

 /* Explanation of the remaining 3 lines is just outside of this code block*/
  -webkit-transition-duration: var(--transition-time);

  -moz-transition-duration: var(--transition-time);

  transition-duration: var(--transition-time);

} 
```

Enter fullscreen mode Exit fullscreen mode

这个选择器代码的最后三行做了同样的事情，只是不是所有的浏览器都理解`transition-duration`，所以我们使用特定于供应商/浏览器的前缀来实现跨浏览器和跨版本的相同功能。

和它的姐妹元素告诉浏览器花一些时间在元素的当前状态和未来状态之间转换。在我们的例子中，未来状态是`:hover`，这是当用户悬停在元素上时 CSS 使用的伪类。

在`var(--transition-time)`中，我们使用`var()`函数来引用前面声明的`--transition-time`自定义属性。前面的规则相当于写`transition-duration: 0.5s;`。

#### -3 按钮:悬停

```
button:hover{
/* We're telling the browser to apply the following rules to any button that gets hovered over by the user*/

  background: var(--main-color);
  /* We're setting the background color to our afore-declared custom property*/

  color: black;
  /* We're setting the text color to black*/
} 
```

Enter fullscreen mode Exit fullscreen mode

*两种风格按钮的区别在于剩下的两个选择器*

#### -4。border-btn

```
.border-btn {
  border-bottom: var(--main-color) 5px solid;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们选择了类中有 border-btn 的元素，并给它们一个底部的边框，宽度为 5 像素，样式是连续的。还记得我们给所有的按钮一个零的`border`吗？多亏了[的 CSS 规范](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity)，我们现在正在覆盖它。我们只覆盖了底部的边界，其余的保持归零。

#### -5。影子 btn

```
.shadow-btn {
  box-shadow: inset 0px -5px var(--main-color);
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们做了和以前一样的东西，但是使用了盒子阴影属性。
我们在水平偏移中添加一个 0 像素的插入(内部)阴影；-5 垂直偏移，它是负的，所以阴影会在底部；并用我们的`--main-color`上色。

#### ——区别。影子 btn 和。border-btn

如果你仔细观察结果，主要的结果差异是高度，`.border-btn`大了 5px。是因为边框边缘的东西位于盒子外面。

**问:**因为阴影边缘的东西位于内部，所以阴影较小，为什么不使用默认的外部阴影？
**答:**我们可以使用没有`inset`的`box-shadow`,`.shadow-btn`看起来和`.border-btn`完全一样，但行为方式却不同。如果你悬停在一个外部阴影上，浏览器不会将其解释为悬停在元素上，这感觉很尴尬。去 CodePen 上试试吧！