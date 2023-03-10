# 纯 CSS 的汉堡菜单

> 原文：<https://dev.to/jacobmparis/hamburger-menu-in-pure-css-mga>

Twitter 上的一名用户用 HTML 和 javascript 构建了一个滑动`hamburger`风格的菜单，并向其寻求提示。我在这里用纯 CSS 构建了一个，使用一个复选框作为触发器，CSS 变换来动画菜单的进出。

[https://codepen.io/anon/pen/YbKvgR?editors=1100](https://codepen.io/anon/pen/YbKvgR?editors=1100)

```
<input id="sidebar__trigger" class="sidebar__trigger" type="checkbox" />
<label for="sidebar__trigger">
  <span class="sidebar__button sidebar__button-open">OPEN</span>
  <span class="sidebar__button sidebar__button-close">NOPE</span>
</label> 
```

Enter fullscreen mode Exit fullscreen mode

所以这个输入是我们打开和关闭菜单的触发器。因为它是一个复选框，它已经具有浏览器内置的切换功能。我们需要给它一个 ID，这样我们就可以将一个标签链接到它。贴上标签后，我们只需点击标签即可切换复选框。

现在您可能会想，我们可以通过将输入放在标签中来清理这些代码——通常您是正确的。如果一个输入是标签元素中唯一的输入，浏览器会知道它们是成对的，你不需要在输入上指定`id`或者在标签上指定`for`。在这种情况下，我们将使用 CSS 来切换基于复选框状态的菜单，为此，菜单必须是输入的下游同级。如果输入在标签内，菜单也需要在标签内，然后任何时候你点击侧边栏，它就会切换进或切换出。在某些情况下也许有用，但对我们来说不是。

如果双下划线`__`命名不熟悉，这是一个叫做 BEM 的设计模式，代表 Block__Element - Modifier。通过使用这种命名约定，我们可以确保我们的 CSS 不会干扰网站上的任何其他代码。像`.input`这样的泛型类很可能被用在其他地方，这将导致不利的结果。

标签中有两个`<span>`元素。其中每一个都有不同的类`-open`和`-close`，我们稍后将引用 CSS 来基于复选框状态进行切换。

```
<ul class="sidebar">
    <li>Home Page</li>
    <li>Example 1</li>
    <li>Example 2</li>
    <li>Example 3</li>
    <ul>
        <li>Example 1</li>
        <li>Example 2</li>
        <li>Example 3</li>
    </ul>
    <li>Example 1</li>
    <li>Example 2</li>
    <li>Example 3</li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个通用的占位符菜单。唯一重要的部分是它有类`.sidebar`,因为这是我们在 CSS 中引用它的方式。它还必须按源顺序放在输入的下面。

```
.sidebar {
  background: #333;
  color: white;
  max-width: 200px;
  transition: transform 0.5s;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们会给侧边栏一个基本的样式，这样我们就可以区分它和页面的其他部分。这里重要的是`transition`意味着每次`transform`属性改变时，它将在 0.5 秒内从旧值逐渐过渡到新值。增加这个数字可以使侧边栏滑出的更慢，或者减少它可以加快滑出的速度。

```
.sidebar__trigger {
    display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们想隐藏我们的复选框，这样我们就可以看到我们喜欢的标签。

```
.sidebar__trigger:checked + label > .sidebar__button-open {
    display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将一点一点地分解它

```
.sidebar__trigger:checked // Find our input only when checked
+ label // Move to a label element that is an immediate next sibling
> .sidebar__button-open // And select the open-button that is an immediate child 
```

Enter fullscreen mode Exit fullscreen mode

`display: none;`只是隐藏了那个元素。然后，当输入没有被检查时，我们对关闭按钮做同样的事情。

```
.sidebar__trigger:not(:checked) + label > .sidebar__button-close {
    display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一的新东西是`:not()`选择器，它本质上是一个负匹配。

```
.sidebar__trigger:not(:checked) ~ .sidebar {
    transform: translateX(-100%);
} 
```

Enter fullscreen mode Exit fullscreen mode

之前的规则使用了`+ label`，它只选择了一个`label`标签，它是*的下一个兄弟*。该规则使用`~ .sidebar`，它选择*的任何后续兄弟*。现在我们已经选择了侧边栏，我们可以用 CSS 转换来移动它的位置，使它向左移动 100%的宽度。这将使其在屏幕外变得模糊，直到输入被检查，这将取消该规则并使其回到原来的位置。

```
.sidebar__button {
    width: 300px;
    border: 1px solid #ddf;
    padding: 1rem;
    border-radius: 0.25rem;
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是一个小的按钮样式，使它看起来像一个按钮，而不只是一些文本。

此代码栏显示了一个工作示例

[https://codepen.io/anon/pen/YbKvgR?editors=1100](https://codepen.io/anon/pen/YbKvgR?editors=1100)