# JavaScript (ES5) -使用选择器

> 原文：<https://dev.to/martyhimmel/javascript-es5---working-with-selectors-598g>

*此文最初于 2016 年 12 月 22 日发布在我的网站 [https://martyhimmel.me](https://martyhimmel.me) 。像 dev.to 上的许多其他人一样，我决定将我的技术博客帖子转移到这个网站。*

## DOM -文档对象模型

JavaScript 通过文档对象模型(DOM)处理 HTML 元素。您可以将 DOM 视为页面上 HTML 元素的层次结构。下面是这种结构的一个例子:

```
<html>
<head>
  Page Title </head> <body>
  <p id="foo">Some text in a paragraph</p>
  <ul class="my-list">
    <li>List Item 1</li>
    <li>List Item 2</li>
    <li>List Item 3</li>
  </ul> </body> </html> 
```

Enter fullscreen mode Exit fullscreen mode

JavaScript 使用 DOM 的结构来遍历、查找和操作元素。在上面的例子中，DOM 结构声明`head`和`body`元素是`html`元素的子元素，相反的是`html`元素是`head`和`body`元素的父元素。这种关系适用于页面上的每个元素(例如，`ul`是`li`元素的父元素，`p`是`body`元素的子元素，等等)。)并且在使用选择器时了解这一点很重要。

第一步通常通过`document`选择器方法来完成。`document`选择器允许你获取页面上的元素并开始使用它们。比如:

```
var paragraphFoo = document.getElementById('foo'); 
```

Enter fullscreen mode Exit fullscreen mode

会将`paragraphFoo`设置为:

```
<p id="foo">Some text in a paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以访问该段落元素的所有属性。这里有几个例子。

*注:在任何使用`paragraphFoo`的地方，都可以用`document.getElementById('foo')`代替。设置`paragraphFoo`变量并使用它可以略微提升性能——我们不需要每次都查询/遍历 DOM 来定位段落元素。*

```
// element.innerHTML
// As a getter, it returns the current HTML inside of the opening and closing tags
paragraphFoo.innerHTML; // Some text in a paragraph

// As a setter, it changes the HTML inside the opening and closing tags
paragraphFoo.innerHTML = 'Change the paragraph text';
console.log(paragraphFoo); // <p id="foo">Change the paragraph text</p> 
```

Enter fullscreen mode Exit fullscreen mode

```
// element.classList
// By itself, it returns an array of classes. You can also use element.classList.add() and
// element.classList.remove() to add and remove classes on the element.
paragraphFoo.classList; // []

paragraphFoo.classList.add('foo');
paragraphFoo.classList.add('bar');
paragraphFoo.classList; // ['foo', 'bar']

paragraphFoo.classList.remove('foo');
paragraphFoo.classList; // ['bar'] 
```

Enter fullscreen mode Exit fullscreen mode

在元素上还可以使用多种方法。你可以查看 Mozilla 开发者文档中可用方法的列表。

## 选择器

**免责声明**

在本节中，当我将选择器描述为返回一个数组时，这在技术上是不正确的。它们要么是元素的`HTMLCollection`要么是元素的`NodeList`。它们在技术上不是数组，因为你不能使用数组方法(`map`、`reduce`等)。)，但是您可以像访问数组一样遍历和访问单个元素(`for`循环、括号符号等)。这也意味着当描述其中一个的`console.log()`结果时，它也不完全准确。代替完整的元素(例如`[<li class="foo">]Some item</li>]`)，它返回返回元素的点符号版本(例如`[li.foo]`)。为了简单起见，我称它们为数组，并使用完整的元素。请记住，从技术角度来看，这是不对的，但从实践角度来看是可行的。

### getElementById

接受一个 HTML id 作为参数。返回单个元素。

```
<p id="foo">Some text in a paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

```
var paragraphFoo = document.getElementById('foo');
console.log(paragraphFoo); // <p id="foo">Some text in a paragraph</p> 
```

Enter fullscreen mode Exit fullscreen mode

### getElementsByClassName

接受 HTML/CSS 类名作为参数。返回元素数组。

```
<p class="bar">paragraph 1</p>
<p>paragraph 2</p>
<p class="bar">paragraph 3</p> 
```

Enter fullscreen mode Exit fullscreen mode

```
var paragraphArray = document.getElementsByClassName('bar');
console.log(paragraphArray);
// [<p class="bar">paragraph 1</p>, <p class="bar">paragraph 3</p>] 
```

Enter fullscreen mode Exit fullscreen mode

### getElementsByTagName

接受一个 HTML 标记(' p '，' div '等)。)作为论据。返回元素数组。

```
// Using the structure example near the top
var listItems = document.getElementsByTagName('li');
console.log(listItems);
// [<li>List Item 1</li>, <li>List Item 2</li>, <li>List Item 3</li>] 
```

Enter fullscreen mode Exit fullscreen mode

### getElementsByName

接受“name”属性值作为参数。返回元素数组。

```
<input name="first_name" type="text">
<input name="last_name" type="text"> 
```

Enter fullscreen mode Exit fullscreen mode

```
var firstName = document.getElementsByName('first_name');
console.log(firstName);
// [<input name="first_name" type="text>] 
```

Enter fullscreen mode Exit fullscreen mode

### 查询选择器

将 CSS 样式选择器作为参数。返回第一个匹配的元素。

```
<p>I have <span class="num">2</span> cats.</p>
<div>I have <span class="num">3</span> dogs.</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
var spanNumOfCats = document.querySelector('p .num');
console.log(spanNumOfCats);
// <span class="num">2</span> 
```

Enter fullscreen mode Exit fullscreen mode

### 查询选择全部

与 querySelector 相同，只是它返回一个包含所有匹配值的数组。

```
<p>I have <span class="num cats">2</span> cats.</p>
<p>I have <span class="num dogs">3</span> dogs.</p>
<p>I have <span class="num rabbits">4</span> rabbits.</p> 
```

Enter fullscreen mode Exit fullscreen mode

```
var spanNonRabbitAnimals = document.querySelector('p .num:not(.rabbits)');
console.log(spanNonRabbitAnimals);
// [<span class="num cats">2</span>, <span class="num rabbits">4</span>] 
```

Enter fullscreen mode Exit fullscreen mode

在现代浏览器和代码中，你通常只会看到/使用`querySelector`和`querySelectorAll`，因为它们极大地简化了过程，使代码更容易阅读和理解。例如，如果你需要一个类为`foo`的单个元素，`document.querySelector('.foo');`比`document.getElementsByClassName('foo')[0];`更简单。