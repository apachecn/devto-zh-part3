# 25 常见前端开发人员访谈问答

> 原文：<https://dev.to/jaamaalxyz/25-common-frontend-developer-interview-qa-593d>

[![feature-image](img/4f7fb5fbff65fb193712d5aa02178c79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HY9gd3EC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/886465/pexels-photo-886465.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D650%26w%3D940)

照片由 [Pexels](https://www.pexels.com) 拍摄

## 提问:

1.  HTML5 中的语义元素是什么？
2.  CSS3 中的视口高度和宽度？
3.  什么是 CSS 断点？
4.  关于 ID 和类的 CSS 最佳实践。
5.  什么是萨斯/SCSS？
6.  萨斯/SCSS 的 mixin 是什么？
7.  ES6 有什么新功能？
8.  什么是事件冒泡？
9.  JavaScript 中的原型继承是什么？
10.  JavaScript 中的抽象函数？
11.  箭头函数中的`this`关键字是什么？
12.  正常函数中的`this`关键字是什么？
13.  localStorage 和 sessionStorage 有什么区别？
14.  什么是 CORS？
15.  什么是 HTTP 请求？
16.  我们有多少 HTTP 方法？
17.  什么是 API 和 REST API？
18.  发出 AJAX 请求？
19.  浏览器是如何工作的？
20.  JavaScript 中的纯函数是什么？
21.  JavaScript 中的事件处理？
22.  JavaScript 执行上下文？
23.  如何在 React 中管理自己的状态？
24.  Redux 是如何工作的？
25.  什么是表象成分和功能成分？

让我们多描述一点:

### 1。HTML5 中的语义元素是什么？

语义元素清楚地向浏览器和开发人员描述了它的含义。

*   非语义元素的例子:`<div>`和`<span>`——对其内容一无所知。
*   语义元素的例子:`<form>`、`<table>`、`<article>`——明确定义其内容。

更多详情: [W3Schools](https://www.w3schools.com/html/html5_semantic_elements.asp)

### 2。CSS3 中查看端口高度&宽度？

视口单元是一组新的单元，是为我们今天面临的挑战而设计的。一页纸、全幅网格、排版和许多其他东西都依赖于视窗的大小。以前，我们使用百分比或 JavaScript 来应对这些挑战。

这套新单元由四个不同的单元组成。每个轴两个，以及这两个值的最小值和最大值。

*   `vw`:1/100 视口宽度
*   `vh`:1/100 视口高度
*   `vmin`:最小边的 1/100
*   `vmax`:最大边的 1/100

注:`IE9`用`vm`代替`vmin`。它不支持`vmax`。
澄清一下:在纵向模式下，`1vmax`等于`1vh`，而在横向模式下，`1vmax`等于`1vw`。

更多详情:[来源](https://web-design-weekly.com/2014/11/18/viewport-units-vw-vh-vmin-vmax/)

### 3。什么是 CSS 断点？

CSS 断点是网站内容根据设备宽度做出响应的点，允许您向用户显示最佳布局。CSS 断点也称为媒体查询断点，因为它们与媒体查询一起使用。

```
/* For mobile phones: */
.col {
  width: 100%;
}

/* For tablets: */
@media only screen and (min-width: 600px) {
  .col-s {width: 50%;}
}

/* For desktop: */
@media only screen and (min-width: 768px) {
  .col-lg {width: 33.33%;}
} 
```

Enter fullscreen mode Exit fullscreen mode

更多详情: [RWD](https://responsivedesign.is/strategy/page-layout/defining-breakpoints/)

### 4。关于 ID 和类的 CSS 最佳实践。

我们需要在 HTML/XHTML 文档中描述内容的方法。像`<h1>`、`<p>`和`<ul>`这样的基本元素通常可以完成这项工作，但是我们的基本标签集并没有涵盖所有可能的页面元素类型或布局选择。为此，我们需要 ID 和类。例如`<ul id="nav">`，这将使我们有机会专门针对这个无序列表，以便我们可以对页面上的其他无序列表进行独特的操作。或者，我们可能在页面上有一个部分没有相关的标签来表示它，例如，一个页脚，我们可能会这样做:`<div id="footer">`。或者，我们在侧边栏中有一些盒子，用于以某种方式将内容分开:`<div class="sidebar-box">`。

更多详情: [CSS 招数](https://css-tricks.com/the-difference-between-id-and-class/)

### 5。什么是萨斯/SCSS？

Sass(语法上令人敬畏的样式表)是一种样式表语言，最初由 Hampton Catlin 设计，由 Natalie Weizenbaum 开发。...Sass 是一种预处理器脚本语言，它被解释或编译成级联样式表(CSS)。SassScript 本身就是脚本语言。

关于 [Sass/SCSS 官方 sit](https://sass-lang.com/) 和[我自己的孟加拉语博客](https://bit.ly/2IyeyW1)的更多详情

### 6。萨斯/SCSS 的 mixin 是什么？

`CSS`预处理器`Sass`最强大的特性之一是`mixin`，它将一个公共模式抽象成一个语义和可重用的块。想象一下获取一个按钮的样式，不需要记住所有的属性是什么，而是让一个选择器包含按钮的样式。

```
@mixin transform($property) {
  -webkit-transform: $property;
      -ms-transform: $property;
          transform: $property;
}

.box { @include transform(rotate(30deg)); } 
```

Enter fullscreen mode Exit fullscreen mode

当我们编译成 CSS 的代码看起来像下面的类型:

```
.box {
  -webkit-transform: rotate(30deg);
      -ms-transform: rotate(30deg);
          transform: rotate(30deg);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 7。ES6 有什么新功能？

JavaScript ES6 带来了新的语法和令人惊叹的新特性，使您的代码更加现代，可读性更好。它允许你写更少的代码，做更多的事情。ES6 向我们介绍了许多很棒的特性，如箭头函数、模板字符串、类销毁、模块…等等。

*   箭头
*   班
*   增强的对象文字
*   模板字符串
*   解构
*   默认+休息+传播
*   let + const
*   迭代器+ for..关于
*   发电机
*   采用双字节对字符进行编码
*   模块
*   模块加载器
*   地图+套装+武器地图+武器套装
*   委托书
*   标志
*   子类内置
*   承诺
*   数学+数字+字符串+数组+对象 API
*   二进制和八进制文字
*   反映 api
*   尾部呼叫

更多详情: [ES6 特性](https://github.com/lukehoban/es6features)

### 8。什么是事件冒泡？

冒泡原理很简单。

当一个事件发生在一个元素上时，它首先在这个元素上运行处理程序，然后在它的父元素上运行处理程序，最后在其他祖先元素上运行处理程序。

假设我们有 3 个嵌套元素 FORM > DIV > P，每个元素上都有一个处理程序:

```
 <style>
  body * {
    margin: 10px;
    border: 1px solid blue;
  }
</style>

<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

点击内层`<p>`首先运行`onclick`:

*   在那个`<p>`上。
*   然后在外层`<div>`。
*   然后在外层`<form>`。
*   依此类推，直到文档对象。

更多详情: [JavaScript.info](https://javascript.info/bubbling-and-capturing)

### 9。JavaScript 中的原型继承是什么？

**继承**指的是一个对象从另一个对象访问方法和其他属性的能力。对象可以继承其他对象的东西。JavaScript 中的继承通过称为原型的东西来工作，这种形式的继承通常被称为原型继承。

数字海洋的更多细节

### 10。JavaScript 中的抽象函数？

在面向对象编程中，**抽象**是四个核心原则之一(另外三个是**封装**、**继承**和**多态**)。抽象的价值在于两个关键原因:

*   **抽象**隐藏某些细节，只显示对象的本质特征。它试图减少和分解细节，以便开发人员可以一次专注于几个概念。这种方法提高了代码的可理解性和可维护性。
*   抽象帮助我们减少代码重复。抽象提供了处理横切关注点的方法，使我们能够避免紧密耦合的代码。

缺乏抽象不可避免地会导致可维护性的问题。

关于 [CSS 技巧的更多细节](https://css-tricks.com/importance-javascript-abstractions-working-remote-data/)

### 11。箭头函数中的`this`关键字是什么？

在经典的函数表达式中，`this`关键字根据调用它的上下文绑定到不同的值。然而，对于 arrow 函数来说，`this`是有词汇限制的。这意味着它从包含 arrow 函数的代码中使用它。

比如看下面的 setTimeout 函数:

```
// ES5
var obj = {
  id: 42,
  counter: function counter() {
    setTimeout(function() {
      console.log(this.id);
    }.bind(this), 1000);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在 ES5 的例子中，需要`.bind(this)`来帮助将这个上下文传递给函数。否则，默认情况下`this`将是未定义的。

```
// ES6
var obj = {
  id: 42,
  counter: function counter() {
    setTimeout(() => {
      console.log(this.id);
    }, 1000);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

ES6 arrow 函数不能绑定到一个`this`关键字，所以它将在词汇上向上一个作用域，并在定义它的作用域中使用`this`的值。

关于 [freeCodeCamp 博客](https://medium.freecodecamp.org/when-and-why-you-should-use-es6-arrow-functions-and-when-you-shouldnt-3d851d7f0b26)的更多详情

### 12。正常函数中的`this`关键字是什么？

与其他语言相比，函数的`this`关键字在 JavaScript 中的行为略有不同。它在严格模式和非严格模式之间也有一些区别。

大多数情况下，`this`的值是由函数的调用方式决定的。在执行过程中不能通过赋值来设置，可能每次调用函数都不一样。ES5 引入了`bind()`方法来设置函数的`this`值，而不管它是如何被调用的，ES2015 引入了不提供自己的`this`绑定的箭头函数(它保留了封闭词法上下文的`this`值)。

关于 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this) 的更多详情

### 13。localStorage、sessionStorage 和 cookies 有什么区别？

好的， **localStorage** 因为它被称为浏览器的本地存储，它可以存储高达 **10MB** ， **sessionStorage** 也是如此，但正如其名称所示，它是基于会话的，在关闭浏览器后会被删除，也可以存储少于 **localStorage** ，比如高达 **5MB** ，但 **cookies** 是存储在浏览器中的非常小的数据，最多可以存储**4k...**

关于 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API) 的更多详情

### 14。什么是 CORS？

跨源资源共享(CORS)是一种机制，它使用额外的`HTTP`头来告诉浏览器让在一个源(域)上运行的 web 应用程序有权访问来自不同源的服务器的选定资源。当 web 应用程序请求来源(域、协议和端口)不同于其自身来源的资源时，它会发出跨来源 HTTP 请求。

更多详情: [MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)

### 15。什么是 HTTP 请求？

**HTTP Request** 是一台计算机发送给另一台计算机的信息包，以进行某种交流。就其核心而言， **HTTP 请求**是由客户端发送到服务器的二进制数据包。一个 **HTTP 请求**包含以下部分。请求行。头，请求中有 0 个或多个头。

关于[代码学院](https://www.codecademy.com/articles/http-requests)的更多详情

### 16。我们有多少 HTTP 方法？

HTTP 定义了一组`request methods`来指示对给定资源要执行的期望动作。虽然它们也可以是名词，但是这些请求方法有时被称为 HTTP 动词。它们中的每一个都实现了不同的语义，但是它们之间有一些共同的特性:例如，请求方法可以是安全的、幂等的或者可缓存的。

#### 得到

方法请求指定资源的表示。使用`GET`的请求应该只检索数据。

#### 头

`HEAD`方法要求与`GET`请求相同的响应，但是没有响应体。

#### 岗位

`POST`方法用于将实体提交给指定的资源，这通常会导致状态的改变或对服务器产生副作用。

#### 放

`PUT`方法用请求负载替换目标资源的所有当前表示。

#### 删除

方法删除指定的资源。

#### 连接

`CONNECT`方法建立到目标资源所标识的服务器的隧道。

#### 选项

`OPTIONS`方法用于描述目标资源的通信选项。

#### 痕迹

`TRACE`方法沿着到目标资源的路径执行消息环回测试。

#### 补丁

`PATCH`方法用于对资源进行部分修改。

### 17。什么是 API 和 REST API？

##### API:

允许创建访问操作系统、应用程序或其它服务的功能或数据的应用程序的一组功能和过程。

关于 [freeCodeCamp 博客](https://medium.freecodecamp.org/what-is-an-api-in-english-please-b880a3214a82)的更多详情

##### 休息 API:

REST 或 T2 RESTful API 设计(代表性状态转移)旨在利用现有的协议。虽然 **REST** 几乎可以在任何协议上使用，但是当用于 Web APIs 时，它通常利用 HTTP。这意味着开发人员不需要安装库或额外的软件来利用 REST API 设计。Roy Fielding 博士在他 2000 年的博士论文中定义了 REST API 设计。它以令人难以置信的灵活性著称。由于数据与方法和资源无关，REST 有能力处理多种类型的调用，返回不同的数据格式，甚至随着超媒体的正确实现而改变结构。

关于[站点点](https://www.sitepoint.com/developers-rest-api/)的更多详情

### 18。发出 AJAX 请求？

下面是一个通用的 jQuery AJAX 请求:

```
$(function() {
    function LoadPersons(data) {
        // do something with data
    }

    $.ajax({
        type: 'POST',
        url: url,
        data: JSON.stringify(parameters),
        contentType: 'application/json;',
        dataType: 'json',
        success: function(result) {
            // do something with persons (data)
            // e.g. LoadPersons(data);
        }
    });
}); 
```

Enter fullscreen mode Exit fullscreen mode

关于 [jQuery 官方文档的更多详情](http://api.jquery.com/jquery.ajax/)

### 19。浏览器是如何工作的？

web 服务器定位信息，然后将信息发送到 web 浏览器，浏览器显示结果。当 web 浏览器联系服务器时，它们要求发送用超文本标记语言(HTML)构建的页面。浏览器解释这些页面并在你的电脑上显示出来。

关于 [html5rocks](https://www.html5rocks.com/en/tutorials/internals/howbrowserswork/) 的更多细节

### 20。JavaScript 中的纯函数是什么？

纯函数是接受输入并返回值而不修改其范围之外的任何数据的函数(副作用)。它的输出或返回值必须依赖于输入/参数，纯函数必须返回值。...这是一个纯函数。

关于 [freeCodeCamp 博客](https://medium.freecodecamp.org/what-is-a-pure-function-in-javascript-acb887375dfe)的更多详情

### 21。JavaScript 中的事件处理？

JavaScript 与 HTML 的交互是通过用户或浏览器操作页面时发生的事件来处理的。

当页面加载时，它被称为事件。当用户点击一个按钮时，这个点击就是一个事件。其他例子包括像按下任意键、关闭窗口、调整窗口大小等事件。

关于[雄辩的 Javascript](https://eloquentjavascript.net/15_event.html) 的更多细节

### 22。JavaScript 执行上下文？

执行上下文(EC)被定义为 JavaScript 代码执行的环境。我所说的环境是指 JavaScript 代码可以访问的变量、对象和函数的值，构成了它的环境。

关于[介质](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)的更多细节

### 23。如何在 React 中管理自己的状态？

在 React 中，管理状态的方式总是数不胜数。Redux 一直是一个受欢迎的选择，但是随着 React 16 和最近发布的库的出现，现在有了更多的选择。你可以使用 **React 上下文 API** 、 **Redux** 或 **Mobx** 等来管理你的状态。

关于[介质](https://blog.bitsrc.io/state-of-react-state-management-in-2019-779647206bbc)的更多细节

### 24。Redux 是如何工作的？

动作、减少器、动作创建器、中间件、纯功能、不变性……
这些术语中的大部分看起来完全陌生。

Redux 简单得惊人。它使用一个名为 reducer 的函数(这个名字来源于 JavaScript reduce 方法),这个函数有两个参数:一个动作和下一个状态。reducer 可以访问当前(即将成为先前)状态，将给定动作应用于该状态，并返回所需的下一个状态。

戴夫·塞迪亚博客的更多细节

### 25。什么是表象成分和功能成分？

##### 表象成分:

很少有自己的状态(即使有，也是 UI 状态而不是数据)。

丹·阿布拉莫夫博客的更多详情

##### 功能组件:

如果 React 组件为相同的状态和属性呈现相同的输出，则可以认为它是纯的。

关于[日志火箭](https://logrocket.com/blog/pure-functional-components/)的更多细节

想了解下面的“全栈开发人员面试问题”toptal.com 的文章是一个很好的阅读。
[10 个必不可少的全栈面试问题](https://www.toptal.com/full-stack/interview-questions)

请随意评论并分享这篇文章到你最喜欢的地方，是的，鼓励我通过点击心形按钮来传播你的爱。谢谢

## 我是谁？

这是在孟加拉国达卡工作的软件开发人员贾马尔·乌丁博士。我喜欢学习新东西并与他人分享。玩切割技术是我的爱好，处理遗产是我的日常工作:)。在[推特](https://twitter.com/jamal_uddin95)和 [LinkedIn](https://www.linkedin.com/in/jamal-pb95/) 上联系我

注意:本帖所有数据都是从各种来源收集的。我参考了其中的一些。