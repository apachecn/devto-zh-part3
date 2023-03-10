# 为什么垂直文本方向是跨浏览器兼容性的噩梦？

> 原文：<https://dev.to/lambdatest/why-vertical-text-orientation-is-a-nightmare-for-cross-browser-compatibility-3gem>

起初，垂直文本方向的必要性似乎并不明显，它的使用仅仅局限于网页的设计方面。然而，许多亚洲语言，如汉语或日语，可以垂直书写，从右向左书写，或者在蒙古语中从左向右书写。在这种语言中，即使块流方向是从左到右或从右到左，一行中的字母或字符也是从上到下垂直流动的。垂直文本方向的另一个常见用途是在表格标题中。这就是文本定向属性变得不可或缺的地方。

## CSS 文本方向

我们将在这篇博客中重点介绍的方法是使用 CSS 文本定向。**‘text-orientation’**是一个 CSS 属性，它定义了一行中字符的方向。请记住，文本方向需要与书写模式属性结合使用。它只能在写入模式设置为垂直(垂直-rl 或垂直-lr)时使用。如果写入模式设置为水平(水平-tb)，它将不起作用。

**语法:**

**文字-方向:**混合|直立|侧身；

默认情况下，文本方向设置为混合。

*   混合:水平脚本将顺时针旋转 90 度，而垂直脚本保持不变。
*   直立:即使方块流是垂直的，所有的角色都将保持直立
*   侧向:所有文本(水平和垂直脚本)将顺时针侧向旋转 90 度。

### 浏览器支持

前往 caniuse 并搜索“CSS 文本定向”以深入了解哪些浏览器和特定浏览器版本支持此功能。

*CSS 文本方向属性的跨浏览器兼容性——can use*

[![Cross browser compatibility of CSS text-orientation property](img/f5d8b7bcff669710c7c1378835020504.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hBPpkNo_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/unnamed-5-1.png)

请记住，CSS3 中的文本定向是一个相对较新的特性，将来可能会进行语法更改。尽管它受到所有主流浏览器的支持，如 Chrome、Firefox、Opera 和 Safari，但主要的例外是 Microsoft Edge 和 Internet Explorer。谨慎的做法是对这种不受支持的浏览器使用某种回退，这将进一步减轻我们对[跨浏览器测试](https://www.lambdatest.com/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)的努力。

当你前往[执行跨浏览器测试](https://www.lambdatest.com/blog/performing-cross-browser-testing-with-lambdatest/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)以在 [LambdaTest 实验](https://www.lambdatest.com/experiment/css-text-orientation.html?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)上确认浏览器对垂直文本定向的支持时，你也可以看到 CSS 文本定向在起作用。它不仅用代码显示了这个特性的一个实例，而且还显示了所有支持和不支持特定特性的主要浏览器的列表，它还可以检测用户当前的浏览器和操作系统。

[![Lambdatest Experiments - CSS vertical text-orientation](img/982cb24732fb6dd6aaa7a39e83a063f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UySm1KP4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image9-2.png)

*Lambdatest 实验–CSS 垂直文本方向*

在使用文本方向之前，我们需要首先了解书写模式属性，并理解块流和内嵌流方向的概念。

### 书写模式

**‘writing-mode’**属性指定行是否设置为水平或垂直文本方向。此外，文本块的方向是从左到右或从右到左。写入模式可以有 3 个值–水平-tb、垂直-lr 或垂直-rl。

**书写模式:**水平-tb |垂直-rl |垂直-lr；

**注:**还有 2 个不应在生产代码中使用的实验值。–“横向-左后”或“横向-左后”。此外，lr、lr-tb、tb、tb-rl、rl 等旧值已被弃用。

书写模式引入了两个关键概念-

*   **块流向:**指定块级盒子在容器内堆叠的方向。它可以是从上到下、从左到右或从右到左。
*   **行内流动方向:**指定内容在文本行内流动的方向，以及新行开始的位置。它可以是水平的，也可以是垂直的。

[![WRITING MODE](img/6f6374f4ac2e5db69ee3c1a5fdf37f69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ezeUFrPJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-0-17.png)

[![WRITING MODE](img/1d0577d7196e6ea3bd8efb7729ad2fbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bS2EUPf6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-0-18.png)

若要实现垂直文本方向，请将书写模式属性设置为 vertical-lr(或 vertical-rl ),并将文本方向设置为 upright。

[![vertical text using text orientation property](img/8df6f566625ff7537d6ae1d74fee5eaa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KbG2Y6W1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/unnamed-6.png)

### 对 LambdaTest 感到疑惑？

LambdaTest 是一个跨浏览器测试云，它可以帮助您在 2000 多种浏览器和浏览器版本上轻松执行[浏览器兼容性测试](https://www.lambdatest.com/feature?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)。您可以通过托管在云服务器上的虚拟机直接与真正的浏览器交互，来执行[手动跨浏览器测试](https://www.lambdatest.com/blog/34-ways-to-save-time-on-manual-cross-browser-testing/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)。您还可以通过您选择的测试自动化框架，使用他们的在线 Selenium 网格来执行自动化的[跨浏览器测试](https://www.lambdatest.com/selenium-automation?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)。
[![LambdaTest](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)

### 使用“文本方向”属性创建竖排文本

要创建垂直方向的文本，需要将 CSS 中的文本方向设置为垂直，同时将**书写模式**设置为**垂直方向**。正如我们前面讨论的，只有当书写模式属性设置为垂直**(垂直-rl 或垂直-lr)** 而不是水平(水平-tb)时，CSS 文本方向才起作用。

```
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    Vertical oriented text
    <style>
        .vertical-text {
            writing-mode: vertical-lr;
            text-orientation: upright;
            background-color:red;
        }

        @supports(text-orientation:upright){
            .vertical-text{
                background-color: greenyellow;
            }
        }

    </style>
</head>

<body>
    <h1>CSS text-orientation property is supported by Google Chrome, Firefox, Opera and Safari</h1>
    <span class="vertical-text">
        Vertical Text using text-orientation
    </span>

</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以利用 LambdaTest 作为跨浏览器测试工具，使用其实时测试功能与您的网站实时互动。实时测试会将你的网站呈现在由 LambdaTest 云服务器托管的虚拟机上。您还可以[执行自动屏幕截图测试](https://www.lambdatest.com/blog/10-must-try-features-of-automated-screenshot-testing-on-lambdatest/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)，在不同的桌面和移动浏览器上一次性捕获我们网页的多个屏幕截图，以[修复浏览器兼容性问题](https://www.lambdatest.com/blog/fixing-javascript-cross-browser-compatibility-issues/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)。你可以使用 Lambda Tunnel 在本地托管的网页上完成所有这些工作，Lambda Tunnel 有助于在你的机器和他们的云服务器之间建立 SSH(安全外壳)连接。

[![CSS text-orientation property supported by Google Chrome](img/568d2872906da40274349839cbc7a466.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nNgHJ2C5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-1.png)

*LambdaTest 实时测试–Google Chrome 支持 CSS 文本定向属性*

[![CSS text-orientation](img/566350e8db6db9466a78ab0299fd289e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Yv68KvO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-0-19-1.png)

*Microsoft Edge 和 Internet Explorer 都不支持 CSS 文本方向属性–LambdaTest 实时测试*

## CSS 文本定位的替代方法

## 1。移行点

CSS 的“word-break”属性定义了当文本到达行尾并溢出其容器时如何换行。

**语法**

**断字:**正常|全断|全保留|断字；

*   normal:换行符的默认规则。
*   为了避免从容器溢出，单词在任何字母处都被打断。
*   保持全部:与正常相同，但对于中文、日文和韩文–不使用任何断词。
*   中断字:如果一行中没有断点，工作可以在任意点中断。

### 浏览器支持

[![Browser Support](img/e65047a1796bdc24ae91ea8217550bc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uOm1zXpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image2-4.png)

与文本方向不同，所有浏览器都支持断字属性，包括 Microsoft Edge 和所有版本的 Internet Explorer。

### 创建带有断字属性的竖排文本

我们可以设置 word-break 属性来分隔所有值，并将容器的宽度减少到 0px。这将迫使所有单词在每个字母处断开，并以垂直方式出现。还要将空白设置为预换行，以确保空白(空白)可见，以便区分单词。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    Vertical oriented text
    <style>
       h2 {
          margin-left: 10%;
           width: 50%;
           text-align: center;
       }
       .vertical-text {
           margin-left: 100px;
           width: 0px;
           word-break: break-all;
           white-space: pre-wrap;
           color: rgb(121, 202, 0);
       }
   </style>
</head>
<body>
    <h2>Using css word-break property as fallback to text-orientation. word-break is supported by all browsers including Edge and IE</h2>
   <p class="vertical-text">word-break css property</p>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

[![Vertical text orientation with CSS word-break property](img/23eb44759637c4b2b62e2ddb2b9e8216.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oXBTDAnH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-0-20-1.png)

*带有 CSS 断字属性的垂直文本方向，受 Microsoft Edge 和 Internet Explorer 支持*

## 2。自动换行/溢出换行

CSS 自动换行允许长单词被打断并换行以避免溢出。自动换行属性现在也被称为**溢出换行**。所有浏览器都支持自动换行语法，包括 Edge 和 IE，而 Chrome 和 opera 支持新的溢出换行语法。

**语法**

自动换行:正常|断字|首字母|继承；

*   正常:仅在正常分隔点断字。
*   断词:任何单词都可以在任意点断。
*   Initial:设置为默认值。
*   Inherit:将该属性从父元素转移到子元素。

### 浏览器支持

[![Browser Support](img/3bf0af6de67b0190b1dd3c9fb54c1995.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HKKnrGTM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/image7-3.png)

就像 word-break 一样，所有浏览器也支持自动换行属性，包括 Microsoft Edge 和所有版本的 Internet Explorer。

### 创建带有自动换行属性的竖排文本

我们可以使用与断字属性相同的策略，将容器的宽度减少到 0px，并强制每个单词在每个字母处断开，并以垂直方向显示，同时每个单独的字符保持垂直。同样，将空白设置为预换行，以确保空白(空白)可见，以便区分单词。

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    Vertical oriented text
    <style>
        h2 {
            text-align: center;
        }

        .vertical-text {
            margin-left: 100px;
            width: 0px;
            word-wrap: break-word;
            white-space: pre-wrap;
            color: rgb(121, 202, 0);
        }
    </style>
</head>
<body>
    <h2>Using css word-wrap property as fallback to text-orientation. word-wrap is supported by all browsers including Edge and IE</h2>
    <p class="vertical-text">word-wrap css property</p>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

[![LambdaTest Visual UI Screenshot Testing](img/4369cd0b741c2b5dea49d633ad11634e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ydFxYne0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.lambdatest.com/blog/wp-content/uploads/2019/02/pasted-image-0-21-1.png)

*使用受 Microsoft Edge 和 Internet Explorer 支持的 CSS 自动换行属性的竖排文本–LambdaTest 可视 UI 屏幕截图测试*

## 3。< br >标记

实现垂直文本方向的最原始的方法是使用 break < br >标签。但是，这种方法不太实用，如果文本字符串太长，就不应该使用这种方法。

```
<p>
    V <br> E <br> R <br> T <br> I <br> C <br> A <br> L <br> <br> T <br> E <br> X <br> T
</p> 
```

Enter fullscreen mode Exit fullscreen mode

## 4。<跨度>包装

另一种流行的方法是将希望垂直定位的文本的每个字母包装在一个 span 标记中，并使用 CSS 将 display: block 分配给每个 span 包装器。同样，就像使用< br >标签一样，这种方法对于大的文本字符串并不实用。

```
<head>
    <style>
        p span {
            display: block;
        }
    </style>
</head>
<body>
    <p>
        <span> V </span>
        <span> E </span>
      <span> R </span>
        <span> T </span>
        <span> I </span>
        <span> C </span>
        <span> A </span>
        <span> L </span>
    </p>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

## 5。使用 JavaScript

一个更好的方法是使用一行 javascript 代码，在需要垂直定向的相关文本的每个字母周围动态添加跨度包装器，而不是在 HTML 中手动添加跨度。文本被分割成一个数组，每个数组元素都被 span 标记包围。

```
<head>
    <style>
        h1 span {
            display: block;
        }
    </style>
</head>

<body>
    <h1> VERTICAL </h1>

    <script>
        var text = document.getElementsByTagName('h1')[0];
        text.innerHTML = '<span>' + text.innerHTML.split('').join('</span><span>') + '</span>';
    </script>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

自古以来，在 CSS 中创建垂直方向的文本一直是一个挑战。没有任何解决方案或解决办法能保证完美的结果。然而，新的 CSS3 文本方向属性的引入彻底解决了这个难题。对于计划在项目的生产版本中部署该特性的开发人员来说，有一点需要注意。相对而言，面向文本是一个新特性，将来可能会发生语法变化，开发人员不应该忘记为 Microsoft Edge 和 Internet Explorer 编写必要的后备代码，以确保跨浏览器的兼容性。如果你对文本方向有其他的想法，请在下面的评论中告诉我。

[![](img/5ca15e434455da9426a2aad75efd7d47.png)](https://accounts.lambdatest.com/register?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)

原文出处:[lambdatest.com](https://www.lambdatest.com/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)

**相关帖子**

1.  [修复 Javascript 跨浏览器兼容性问题](https://www.lambdatest.com/blog/fixing-javascript-cross-browser-compatibility-issues/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)
2.  [避免跨浏览器兼容性问题的 9 种方法](https://www.lambdatest.com/blog/9-ways-to-avoid-cross-browser-compatibility-issues/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)
3.  [如何做一个跨浏览器兼容的网站？](https://www.lambdatest.com/blog/how-to-make-a-cross-browser-compatible-website/?utm_source=Dev&utm_medium=Blog&utm_campaign=ni-02-260319eu&utm_term=OrganicPosting)