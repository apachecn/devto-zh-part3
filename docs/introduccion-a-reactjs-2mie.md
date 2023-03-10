# reactjs 简介

> 原文：<https://dev.to/hectorbliss/introduccion-a-reactjs-2mie>

### 谁是这个职位？

既然你已经登上了这篇帖子，我肯定不需要深入地向你解释什么是 reactjs，它有什么好处，为什么它如此酷，或者为什么它是一个角度上的好选择，甚至 vista . js。

如果您在此安全地了解上述内容，则此帖子的目的不是重复此操作，否则，请提供足够友好的介绍，以便在您开始进行 web 编程时，如果您尚未具备前端、后端或 fullstack pero 开发所需的所有基础和基础，但您很好奇并想使用 reactjs，请购买这些产品，然后无需进行测试-我...。)并且您可以在几分钟内在线创建您的第一个组件。

### 第 1 部分-反应堆

#### 工具

reactdom 是负责在 DOM 中放置和解释组件(使用 reactdom es 创建的组件)的库，从发行版 15 开始，这些库是两个单独的库，但同时协同工作，因此我们可以先打开 jsbin.com，然后使用“添加库”按钮安装 reactdom es，并找到 reactdom es。

[![selecting library](img/2423aff92dcb5883f80dae88e5c3cf5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RuaJFNbE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://blog.fixter.org/conteimg/2018/09/Captura-de-pantalla-2018-09-14-a-la-s--12.00.45.png)

一旦我们安装了 react js，我们就可以创建我们的第一个组件并将其放置在 DOM 中，为此，我们将放置一个标签

con id root en la parte izquierda de nuestro editor, recordemos que tenemos varias secciones entre ellas: html, css, javascript, console y output, solo dejaremos activadas solo html, javascript y html.

```
<!DOCTYPE html>  
<html>  
<head>  
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width">
  JS Bin
</head>  
<body>  
   <div id="root" ></div>

</body>  
</html> 
```

### 你的第一成分

在 javascript 部分，我们必须选择正确的蒸发器，即选择“ES6/Babel”而不是 javascript，然后写下我们的第一个组件:

```
const App = () => (  
   <div>
      BlisS
   </div> )

ReactDOM.render(<App/>, root) 
```

*请注意，Reaction 的 render 方法收到 2 个参数，第一个是要以 jsx 格式放置的 react 组件(如 html 标签)，第二个是要放置的位置，我们可以使用 document.getElementById('root ')选择器传递第二个参数，但在最新版本的 reaction 中，Id 名称已经足够，即使不使用逗号也是如此*

我们应该在输出部分看到结果，这让我们知道我们的代码工作正常。

这非常简单，但它让我们了解什么是组件，以及 reactjs 如何作为一个简单的库工作。

恭喜你用 reactjs 创造了你的第一个组件并呈现在 DOM 中，微笑，下一部分见；）

### 视频资源:

*   [firebasemx](https://www.youtube.com/embed/xW2BB5ZjX9Y)