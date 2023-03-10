# 处理浏览器事件的指南

> 原文：<https://dev.to/sarah_chima/a-guide-to-handling-browser-events-ean>

<figcaption>Cover Photo by [Ambitious Creative Co. - Rick Barrett](https://unsplash.com/photos/HnUHOBuJ7s4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](/search/photos/event?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

大多数网页是交互式的，用户可以在网页上执行操作。动作的例子是点击一个链接，提交一个表格，甚至滚动网页。

有时，当用户执行某个操作时，您可能希望页面以某种方式运行。例如，当用户单击按钮时，您可能希望显示一个模式。或者当用户填写页面上的表单时，您可能希望向用户显示输入是否有效。这些动作是浏览器事件。

在本文中，我们将讨论如何处理浏览器中发生的事件。我们还将讨论如何防止默认操作以及什么是事件传播。让我们马上开始。

### 事件处理程序简介

当事件发生时，我们可以使用事件处理程序告诉浏览器如何响应。事件处理程序是一个在事件发生时运行的函数。我们有三种方式添加事件处理程序:

1.  使用 HTML 属性的内联事件处理程序
2.  DOM 属性
3.  使用 addEventListener 方法

### 内联事件处理程序

可以使用 HTML 属性设置处理程序。例如，如果我们想让一个按钮在用户点击时提醒“嗨”，我们可以这样做:

```
<button onclick="alert('hi')">Say Hi</button> 
```

我们也可以使用相同的语法对许多其他事件这样做。

在 HTML 元素中编写大量代码可能不太方便。我们也可以在脚本中编写一个函数，在`on<event>`属性中调用这个函数。

```
<button onclick="sayHi()">Say Hi</button>

<script>
    function sayHi() {
        console.log('Hi')
    }
</script> 
```

### DOM 属性

另一种处理事件的方法是使用 DOM 属性。让我们用上面的例子来做这件事。

```
<button>Say Hi</button>

<script>
    var button = document.querySelector('button');
    button.onclick = () => {
        console.log('hi')
    }
</script> 
```

这与前面的方法产生相同的结果。

当我们将内联事件处理程序和 JavaScript 处理程序应用于同一个元素时会发生什么？

```
<button onclick="console.log('hi')">Say Hi</button>

<script>
    var button = document.querySelector('button');
    button.onclick = () => {
        console.log('no')
    }
</script> 
```

JavaScript 处理程序优先于内联处理程序，因此记录“否”。

### addevent listener 方法

我们可以使用 addEventListener 方法来监听特定 DOM 元素上的事件。使用这种方法，我们提供了一种处理这种事件的方法。这种方法的一个优点是它允许你在一个元素上注册尽可能多的事件。

这是 addEventListener 方法的语法。

```
target.addEventListener(type, function, useCapture) 
```

Target 是您想要监听事件的元素。

Type 是您要监听的事件的类型。例如，如果它是一个按钮，您可以侦听单击事件。

该函数是事件处理程序。它指定事件发生时执行的功能。

useCapture 是可选的。它是一个布尔值，指定事件应该在捕获阶段还是冒泡阶段使用。如果为真，则在捕获阶段使用。如果为 false(默认值)，则用于冒泡阶段。我们将在后面详细讨论这一点。

这里有一个使用这种方法的例子。

```
<button>Say Hi</button>

<script>
    let button = document.querySelector('button');
    button.addEventListener('click', () => {
        console.log('Hi') //Hi
    })
</script> 
```

在本例中，`button`是我的目标，事件类型是`click`，箭头函数是这段代码运行时执行的函数。如果您运行这段代码，任何时候单击按钮，您都会在控制台中看到“Hi”。

命名函数也可以作为回调函数传递。

### 事件对象

当事件发生时，浏览器创建一个事件对象，并将其作为参数传递给事件处理程序。此事件对象包含事件的详细信息。例如，您可能想知道事件发生时单击了哪个按钮或按下了哪个键或鼠标坐标。您可以从事件对象中获取所有这些信息。

使用我们上面的例子，我们可以这样做:

```
<button>Say Hi</button>

<script>
    let button = document.querySelector('button');
    button.addEventListener('click', (event) => {
        console.log(event.clientX) //41
        console.log(event.clientY) //19
    })
</script> 
```

它打印事件发生位置的 x 和 y 坐标，在本例中分别为 41 和 19。

事件中存储的信息取决于发生的事件类型。要获得事件对象的全部细节，您可以将`event`对象记录到控制台`console.log(event)`。在任何情况下，它总是有一个`type`属性来告诉发生的事件的类型。

### 默认动作

当事件在某些元素上发生时，默认情况下会发生一些动作。例如，当一个链接被点击时，它会把你带到链接的目标。当单击表单中的按钮时，表单被提交。这些是浏览器的默认操作。

在默认事件发生之前，大多数事件都会调用事件处理程序。使用`event.preventDefault()`方法可以防止默认动作。此方法阻止默认操作发生。下面是一个例子:

```
<a href="https://sarahchima.com">Sarah Chima</a>

<script>
    let link = document.querySelector('a');
    link.addEventListener('click', (event) => {
        console.log("Nope, you ain't visiting her today")
        event.preventDefault()
    })
</script> 
```

当单击此链接时，它不会将您带到预期的 URL，而是将语句记录到控制台中。

这也可以用于在页面或上下文菜单中实现键盘快捷键。

但是，为了获得良好的用户体验，最好避免截取元素的预期行为。这种行为可能会让用户认为你的网站坏了。

### 事件和传播

当元素上发生事件时，目标元素上的事件处理程序首先运行。然后它的直接父元素的处理程序(如果有的话)运行，并且它的父元素的父元素的处理程序一直运行到有处理程序的最外层元素。这就是所谓的传播。

这里举个例子会有所帮助。

```
<style>
    * {
        border: 1px solid green;
        margin: 15px
    }
</style>
<div onclick="alert('div')"> DIV
    <p onclick="alert('p')"> P 
        <span onclick="alert('span')"> SPAN</span>
    </p>
</div> 
```

我添加了一点风格来区分元素。如果运行这段 HTML 代码并单击 span 元素，您会注意到三个处理程序被触发。首先是`span`上的那个，接下来是`p`标签上的那个，然后是`div`标签上的那个。这是因为当事件发生时，它会传播到外部元素，并且它们的处理程序也会被触发。

在一个元素中有两种类型的传播:冒泡和捕获。我们以前在讨论 addEventListener 方法的语法时见过这种情况。

冒泡和捕获的区别在于，在冒泡阶段，事件向外传播。也就是说，最内层元素的处理程序首先被触发，然后是父元素。

在捕获过程中，情况正好相反。最外层元素上的处理程序首先被触发，然后是子处理程序，一直到事件发生的元素。这与我们示例中发生的情况相反。

对于事件处理程序，冒泡是默认设置，但如果 capture 参数设置为 true，则会发生捕获。要了解更多这方面的内容，你可以在上面阅读[这篇文章](https://javascript.info/bubbling-and-capturing)。

您可能不希望在单击元素时出现这种传播行为。幸运的是，JavaScript 提供了这样做的方法。要停止传播，可以调用`event.stopPropagation()`方法。此方法防止事件传播。

在我们之前的例子中，我们可以这样做:

```
<div onclick="alert('div')"> DIV
    <p onclick="alert('p')"> P 
        <span> SPAN</span>
    </p>
</div>

<script>
    var span = document.querySelector('span');
    span.addEventListener('click', (event) => {
        alert('span')
        event.stopPropagation();
    }
</script> 
```

如果运行这段代码，您会注意到只有 span 上的处理程序被触发。

与所有默认行为一样，只有在必要时才应该阻止传播。

我们走之前还有最后一件事。

### 删除事件监听器

可以使用`removeEventListener`方法删除事件监听器。我们使用它的方式与使用`addEventListener`方法的方式相同。我们以同样的方式传入类型和函数。但是，函数必须存储在变量中才能工作。

这里有一个例子

```
<button>Say Hi</button>

<script>
    let button = document.querySelector('button');

    function sayHi() {
        console.log('Hi')
    }
    button.addEventListener('click', sayHi)
    button.removeEventListener('click', sayHi)
</script> 
```

运行这段代码，您会注意到事件监听器函数从未运行过。这是因为 eventListener 已被删除。

如果我们不像下面的例子那样将函数存储在变量中，`removeEventListener`就不会工作。

```
<button>Say Hi</button>

<script>
    let button = document.querySelector('button');
    button.addEventListener('click', () => {
        console.log('hi')
    })
    button.removeEventListener('click',  () => {
        console.log('hi')
    })
</script> 
```

### 结论

我们讨论了如何使用 HTML 属性、DOM 属性和 addEventListener 方法处理事件。我们看到，我们可以通过使用`event.preventDefault`方法来防止默认操作。我们还学习了事件传播。

处理事件可以帮助你控制浏览器响应用户动作的方式，我们已经讨论了如何做到这一点。在我的下一篇文章中，我将讨论可以在浏览器中监听的常见事件。

有什么问题或补充吗？留下评论。

感谢您的阅读😊。