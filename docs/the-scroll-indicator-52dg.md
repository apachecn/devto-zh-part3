# 滚动指示器

> 原文：<https://dev.to/dhilipkmr/the-scroll-indicator-52dg>

[![](img/d18a74f76a9b5a43658639f5b53a3c2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k22TEiPV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yuoy5g872a6oa2y8ba1n.png)

**滚动指示器**基本上是一条线，根据用户在页面上滚动了多少来填充。它们通常位于页面的顶部。

[![](img/d66f7cc1af60c040958d03a02331afde.png)](https://i.giphy.com/media/vvWhQsVAFkyisScAsM/giphy-downsized-large.gif)

**输出:**

在此检查输出

[![](img/9289e51c5143fc81ba06d30916f5387f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0hdPUIqt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/acf1wkn8ncov8d5i20el.gif)

## 让我们来编码:

```
<body>
 <div class="line" id="scrollIndicator"></div>
 <div>
   // ... some code
 </div>
</body> 
```

我们在上面的代码片段中所做的只是添加了一个小的滚动条指示器`<div>`。

在我们开始讨论如何实现滚动指示器之前，有必要了解一些与滚动相关的属性。

```
.line {
      background: #038eff;
      height: 5px;
      border-radius: 3px;
      width: 0%;
      position: fixed;
      top: 0;
    } 
```

上面显示了绘制线条所需的 CSS。它最初的宽度应该是 0。然后根据滚动的百分比增加它。

## 窗口的滚动相关属性:

*   `window.innerHeight` -浏览器可视部分的高度。(例如:640)
*   `document.body.scrollHeight` -整个网页的高度。
*   到目前为止，用户已经向下滚动了多少像素。它是**页面负载**上的`0`，并且随着用户向下滚动而增加。

```
const scrollIndicatorElt = document.getElementById('scrollIndicator');
const maxScrollableHeight = document.body.scrollHeight - window.innerHeight;
window.addEventListener('scroll', moveScrollIndicator); 
```

*   通过 id 获取`scrollIndicator`节点。滚动线出现在此元素中。
*   Find `maxScrollableHeight`指定用户可以滚动的像素数。为了识别这一点，我们找到了`document.body.scrollHeight`和`window.innerHeight`之间的区别
*   将事件监听器连接到`scroll`。

```
function moveScrollIndicator() {
  const percentage = ((window.scrollY) / maxScrollableHeight) * 100;
  scrollIndicatorElt.style.width = percentage + '%';
} 
```

*   当 scroll 事件被触发时，函数被执行。
*   `percentage`是`scrollIndicator`元素的宽度。
*   `percentage`计算为用户滚动的**(`window.scrollY`)**像素与**总滚动像素(maxscrollabelheight)**的比值

[![](img/a1329a610d3d2fc40a80610e0e62e7a9.png)](https://i.giphy.com/media/cGtVFd33eEDFEcckiC/giphy.gif)

在此处检查输出[。](https://scrollindicator.netlify.com/)[回购环节](https://github.com/dhilipkmr/scrollIndicator)

关注我有趣的内容。

我的[网站](https://www.dhilipkmr.dev)、[博客](https://dev.to/dhilipkmr)和[推特](https://twitter.com/dhilipkmr_)

那都是乡亲们！