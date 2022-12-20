# 开发黑客:在浏览器中查看难以查看的图像

> 原文：<https://dev.to/ablablalbalblab/dev-hack-viewing-tricky-to-view-images-in-the-browser-51ai>

你有没有遇到过这样的情况:你在网上看到一张图片，你选择在一个新的标签页中打开它来查看细节，但是你什么也没看到？这可能是因为图像是带有透明部分的黑色。拍一张这样的图片:

[![A transparent image with black emoticons.](img/97e14303e3cc89db5e696d6a03b709a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--czm_nDf2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rrt34jh94ezrxp591x3w.png)

如果你在 Chrome 上(没有在其他浏览器上测试过)，你在一个新的标签中打开图像，你不会看到任何东西，因为图像是黑色的，背景是透明的。当你需要放大这些图像时，这会变得有点令人沮丧。

幸运的是，现在有一个解决方案！😁在图像网页上打开浏览器控制台，输入以下代码片段:

```
function c(o){document.body.style.background="rgb("+o.rgb.map(Math.round).join(",")+")"}s=document.createElement("script"),s.src="https://cdnjs.cloudflare.com/ajax/libs/jscolor/2.0.4/jscolor.min.js",document.body.appendChild(s),document.body.innerHTML+="<input class=\"jscolor {onFineChange:'c(this)'}\"style='position:absolute;right:0;top:0'value='0e0e0e'type='search'autocomplete='off'>"; 
```

Enter fullscreen mode Exit fullscreen mode

使用它，你会在屏幕的右上角得到一个方便的颜色选择器，当你在选择器中改变页面的背景颜色时，它会实时更新页面的背景颜色。这是为了更容易查看融合在一起的图像。让我们拆开这个程序:

*   `function c(o){document.body.style.background="rgb("+o.rgb.map(Math.round).join(",")+")"}` -基于`jscolor`对象设置网页背景的功能(后面会详细介绍)。

*   `s=document.createElement("script")` -创建一个新的`<script>`元素。

*   `s.src="https://cdnjs.cloudflare.com/ajax/libs/jscolor/2.0.4/jscolor.min.js"` -将`<script>`元素的`src`设置为我们想要加载的库。

*   `document.body.appendChild(s)` -将`<script>`元素追加到文档中，加载资源。

*   `document.body.innerHTML+="<input class=\"jscolor {onFineChange:'c(this)'}\"style='position:absolute;right:0;top:0'value='0e0e0e'type='search'autocomplete='off'>` -添加一个定制的`jscolor`输入元素来创建颜色选择器。设置每次颜色更新时调用`c()`。

正如在详细版本中看到的，该代码片段使用库 [`jscolor`](http://jscolor.com/) 作为颜色选择器。

希望这个小片段对你有帮助！