# 关于事件处理程序和事件传播的一切

> 原文：<https://dev.to/ruphaa/everything-about-event-handlers-and-event-propagation-47a8>

这已经写了一百多次了，在 StackOverflow 上可能有一百多个答案。但随着时间的推移，我浏览了不同的文章和答案，筛选出了重要的部分，并写下了这篇文章。

# 事件处理

在 JavaScript 中，有三种方法可以将处理程序附加到事件上。

*   HTML 属性
*   DOM 属性
*   addEventListener()

## HTML 属性

这是通过向 DOM 元素添加属性来将处理程序附加到事件的老方法。

请记住，一个节点只能有一个 onclick 属性。因此，使用这种方法，一个事件只能注册一个处理程序。

```
<div id="el" onclick="kaboom()"></div> 
```

Enter fullscreen mode Exit fullscreen mode

## DOM 属性

这是动态创建 DOM 节点的*JavaScript*方式，向它添加一个属性并为该属性附加一个处理程序。由于这与前面的方法几乎相同，*只有一个处理程序可以注册到一个事件*。

```
<script>
  el = document.querySelector("#el")
  el.onclick = clickMe();
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## addEventListener()🔥

这是将处理程序绑定到事件的现代方式。我们可以将任意数量的处理程序与事件绑定，当事件发生时，所有这些处理程序都将被调用。

```
<script>
  el.addEventListener("click", (e) => {
    // Some code here that goes KABOOM!
  });
</script> 
```

Enter fullscreen mode Exit fullscreen mode

# 事件行为

*   预防默认()
*   事件冒泡
*   事件捕获

## preventDefault()

有一些预定义的事件具有与之相关联的默认操作。举个例子，

*   默认情况下，链接会将您带到链接的目标。
*   如果您按下向下箭头，页面向下滚动。

在这些情况下，与这些元素相关联的定制处理程序**将在默认动作**之前被调用，然后是默认动作。如果你想完全解除元素的默认行为，你可以在你的处理程序中调用`event.preventDefault()`。

注意:这并不能阻止这个事件在 DOM 中冒泡。

## 事件冒泡&事件捕获

DOM 中的事件通常以嵌套在 web 浏览器中的事件堆栈的形式传播。

### 事件冒泡

事件冒泡可以用一个例子更好的解释。

让我们以这样的嵌套 DOM 结构为例

```
<div id="parent">
  <div id="child"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果有处理程序连接到`#parent`和`#child`div，

*   当孩子被单击时，由于事件的冒泡性质，首先调用`#child` div 的处理程序，然后事件传播到`#parent` div，并且调用它的处理程序。

简而言之，随着事件从**底部到顶部**冒泡，首先是**子代，然后是**父代。

### 事件捕捉

事件捕获更多的是捕获 DOM 中的事件，并将其交给事件处理程序来执行。事件是自顶向下捕获的，这意味着最外面的元素首先被捕获，然后传播到内部元素。一旦事件被捕获，事件的冒泡就从最内部的元素开始。

### 停止传播()

您可以通过调用子事件处理程序中的`event.stopPropagation()`来停止事件从子到父的冒泡。

```
function childHandler(event) {
  event.stopPropagation();
} 
```

Enter fullscreen mode Exit fullscreen mode

# 删除事件监听器

您可以通过调用事件上的`removeEventListener()`从元素中移除事件监听器，它接受事件名称和处理程序名称作为参数。

```
function handleClick(e) {
  // go KABOOM!
}

element.addEventListener("click", handleClick);
element.removeEventListener("click", handleClick); 
```

Enter fullscreen mode Exit fullscreen mode

*注意:不要使用匿名处理方法。您应该在回调之外定义该函数，然后在 removeEventListener 回调中引用它。*

```
// this is wrong as you cannot remove this event listener
element.addEventListener("click", () => { /* go KABOOM! */ }); 
```

Enter fullscreen mode Exit fullscreen mode

就这样了，伙计们。如果你认为这篇文章有任何可以改进的地方，请留言。