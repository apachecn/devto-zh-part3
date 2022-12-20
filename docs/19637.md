# JavaScript (ES5) -入门

> 原文：<https://dev.to/martyhimmel/javascript-es5---getting-started-5980>

*此文最初于 2017 年 1 月 11 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

## 入门

你决定学习 JavaScript。现在你在想，“我该怎么办？我在哪里写代码？”信不信由你，你正在寻找最快开始的地方。不，不是这个教程，虽然我很感谢你使用它，并希望这将是你编码之旅的一个很好的起点。我说的是你的浏览器。右键单击此页面上的任意位置，然后单击“Inspect”(根据您的浏览器，措辞可能会有所不同)。在新打开的部分，点击“控制台”在控制台中，键入或复制粘贴这段代码:

```
alert('Hello world!'); 
```

Enter fullscreen mode Exit fullscreen mode

您应该看到一个弹出框，上面写着“Hello world！”在里面。恭喜你！你刚刚写了你的第一行 JavaScript。说真的，入门就是那么容易。控制台是测试小代码片段、学习和玩耍的好地方。

在整个系列中，您将看到`console.log('something in here');`语句——括号内的任何内容都将被打印到您刚刚打开的控制台上。事实上，您也可以在控制台中复制并粘贴它。您将看到在该行下面打印出“这里有东西”——记录到控制台的语句。

## 内联 JavaScript

控制台只是编写 JavaScript 的一种方式。实际上，除了测试之外，你不会想使用它，因为没有什么是永恒的。只要重新加载页面，控制台就会被清除(除非您的设置保留了控制台日志)。

对于更永久的脚本，有几种方法可以做到这一点。第一种是在 HTML 文件中——一个内联脚本。您可以在`head`或`body`部分的任何地方放置一组`script`标签。下面的示例展示了这两个部分中的一个脚本。

```
<html>
<head>
  <script>
    alert('Popup triggered in the head tags.');
  </script>
</head>

<body>
  <script>
    console.log('This is logged to the console from the body tags.');
  </script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

要记住的一点是顺序很重要。首先运行`head`部分中的脚本，然后运行`body`部分中的脚本。如果在`body`部分的顶部有一个脚本，它的目标是脚本下面的 HTML 元素，它不知道该做什么，你会得到一个错误。在脚本运行时，HTML 元素还没有加载，所以它不存在。有几种方法可以解决这个问题。

首先是将所有的`script`标签放在结束的`body`标签之前。这确保了在运行脚本之前首先加载所有的 HTML。十多年来，这是一种常见的解决方法，一些系统仍然使用这种方法来向后兼容(例如 WordPress)。第二个是`defer`或`async`属性。我们先来看看 JavaScript 文件。

## JavaScript 文件

在页面中包含 JavaScript 的另一种方法是创建一个单独的 JavaScript 文件并链接到它。这类似于内联脚本，只是代码存储在一个。js”文件，并使用`script`标签上的`src`属性进行引用。标签内容也是空的。

```
<!-- index.html -->
<html>
<head>
  <script src="myscript.js"></script>
</head>

<body>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

```
// myscript.js
alert('Popup triggered in the head tags.'); 
```

Enter fullscreen mode Exit fullscreen mode

还是那句话，顺序很重要。“myscript.js”文件的内容将在加载`body`标签之前加载。这就是`async`和`defer`发挥作用的地方。`async`将在后台加载一个文件，同时继续加载页面的其余部分。如果您有依赖于 HTML 元素的代码，这可能有效，也可能无效，这取决于哪个元素先完成加载。js 文件或 HTML)。`defer`会等到页面加载完毕，然后会加载脚本。

如果您正在编写任何依赖于页面内容的代码(大多数 JavaScript 都会这样)，我建议您使用`defer`。这是它的样子。

```
<html>
<head>
  <script src="myscript.js" defer></script>
</head>

<body>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 语法注释

在深入研究 JavaScript 之前，有几个语法规则需要复习。这不会涵盖所有内容，但意味着强调一些重要的规则，以便您知道您在前面的教程中看到了什么。

语句应该以分号结束。如果你忘记了分号，浏览器会在解释代码时自动插入一个，但并不总是准确的。缺少分号会导致难以调试的问题。

注释以两种方式之一编写——使用`//`或`/* */`。第一个是单行注释，第二个是多行注释。浏览器会忽略注释。

```
// This is a single line comment
/*
This comment
spans multiple
lines
*/ 
```

Enter fullscreen mode Exit fullscreen mode

当使用引号(用于文本字符串)时，可以使用单引号或双引号。在 JavaScript 中，使用哪一个都没关系。唯一重要的是你是否在字符串中使用了相同的类型，比如一个缩写词。`"some text"`与`'some text'`相同。对于字符串中的相同类型，您可以同时使用这两种类型，也可以对引号进行转义。两者并用看起来像`"it's blue"`。转义字符就是在字符前面加一个反斜杠，比如`'it\'s blue'`。