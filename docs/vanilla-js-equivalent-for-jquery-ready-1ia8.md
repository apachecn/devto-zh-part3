# jQuery Ready 的普通 JS 等价物

> 原文：<https://dev.to/paramharrison/vanilla-js-equivalent-for-jquery-ready-1ia8>

> 近十年来，jQuery 已经解决了在不同浏览器上 JS 渲染的副作用问题。现在网络浏览器和渲染引擎更加智能，越来越支持标准的 javascript API。

文档就绪功能是 jQuery 中广泛使用的功能。随着现代 web 开发的发展趋势和浏览器对普通 JS API 的更好支持，我们可以很容易地替换或减少 jQuery 依赖性。

```
// Longer version in jQuery
$(document).ready(function() {
  // DOM events and DOM manipulations
});

// Shorter version in jQuery
$(function() {
  // DOM events and DOM manipulations
}); 
```

让我们看看普通 JS 中就绪函数的例子

```
function ready(callbackFunc) {
  if (document.readyState !== 'loading') {
    // Document is already ready, call the callback directly
    callbackFunc();
  } else if (document.addEventListener) {
    // All modern browsers to register DOMContentLoaded
    document.addEventListener('DOMContentLoaded', callbackFunc);
  } else {
    // Old IE browsers
    document.attachEvent('onreadystatechange', function() {
      if (document.readyState === 'complete') {
        callbackFunc();
      }
    });
  }
}

ready(function() {
  // your code here
}); 
```

这个片段在现代浏览器和旧的 IE 浏览器上都受支持。您可以根据需求限制支持。

仅等待 DOM 元素加载，不等待样式表、图像和其他网络调用。下一次，如果您在站点上使用 jquery，请确保评估您是否仅仅为了基本的 JS 操作就需要额外的 30+ kb 页面负载😎

### 保持联系！

如果你喜欢这篇文章，你可以在 [Twitter](https://twitter.com/learnwithparam) 上找到我，了解更新、公告和新闻。🐤