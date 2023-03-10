# 使用智能 CSS 基于用户滚动位置应用样式

> 原文：<https://dev.to/rikschennink/applying-styles-based-on-the-user-scroll-position-with-smart-css-5c>

通过将当前的滚动偏移量映射到`html`元素的属性，我们可以根据当前的滚动位置来设计页面上的元素。例如，我们可以用它来构建一个浮动导航组件。

> 本文最初发表于[我的个人博客](https://pqina.nl/blog/applying-styles-based-on-the-user-scroll-position-with-smart-css/)

这是我们将要使用的 HTML，一个很好的`<header>`组件，当我们向下滚动时，我们希望它浮在内容之上。

```
<header>I'm the page header</header>
<p>Lot's of content here...</p>
<p>More beautiful content...</p>
<p>Content...</p> 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们将监听`document`上的`'scroll'`事件，并在用户每次滚动时请求当前的`scrollY`位置。

```
document.addEventListener('scroll', () => {
  document.documentElement.dataset.scroll = window.scrollY;
}); 
```

Enter fullscreen mode Exit fullscreen mode

我们将滚动位置存储在`html`元素的数据属性中。如果您使用您的开发工具查看 DOM，它看起来会像这样`<html data-scroll="0">`。

现在我们可以使用这个属性来设计页面上的元素。

```
/* Make sure the header is always at least 3em high */
header {
  min-height: 3em;
  width: 100%;
  background-color: #fff;
}

/* Reserve the same height at the top of the page as the header min-height */
html:not([data-scroll='0']) body {
  padding-top: 3em;
}

/* Switch to fixed positioning, and stick the header to the top of the page */
html:not([data-scroll='0']) header {
  position: fixed;
  top: 0;
  z-index: 1;

  /* This box-shadow will help sell the floating effect */
  box-shadow: 0 0 .5em rgba(0, 0, 0, .5);
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上就是这样，当向下滚动时，标题会自动从页面上分离出来，并浮在内容的上面。JavaScript 代码不关心这个，它的任务只是将滚动偏移量放入数据属性中。这很好，因为 JavaScript 和 CSS 之间没有紧密的耦合。

仍然有一些需要改进的地方，主要是在性能方面。

但是首先，我们必须修复我们的脚本，以应对页面加载时滚动位置不在顶部的情况。在这些情况下，标题将会不正确地呈现。

当页面加载时，我们必须快速获取当前的滚动偏移量。这确保了我们总是与当前的事态保持同步。

```
// Reads out the scroll position and stores it in the data attribute
// so we can use it in our stylesheets
const storeScroll = () => {
  document.documentElement.dataset.scroll = window.scrollY;
}

// Listen for new scroll events
document.addEventListener('scroll', storeScroll);

// Update scroll position for first time
storeScroll(); 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将了解一些性能改进。如果我们请求`scrollY`位置，浏览器将不得不计算页面上每个元素的位置，以确保它返回正确的位置。如果我们不强迫它每次滚动交互都这样做，那是最好的。

要做到这一点，我们需要一个去抖方法，这个方法将我们的请求排队，直到浏览器准备好绘制下一帧，在这一点上，它已经计算了页面上所有元素的位置，所以它不会再这样做。

```
// The debounce function receives our function as a parameter
const debounce = (fn) => {

  // This holds the requestAnimationFrame reference, so we can cancel it if we wish
  let frame;

  // The debounce function returns a new function that can receive a variable number of arguments
  return (...params) => {

    // If the frame variable has been defined, clear it now, and queue for next frame
    if (frame) { 
      cancelAnimationFrame(frame);
    }

    // Queue our function call for the next frame
    frame = requestAnimationFrame(() => {

      // Call our function and pass any params we received
      fn(...params);
    });

  } 
};

// Reads out the scroll position and stores it in the data attribute
// so we can use it in our stylesheets
const storeScroll = () => {
  document.documentElement.dataset.scroll = window.scrollY;
}

// Listen for new scroll events, here we debounce our `storeScroll` function
document.addEventListener('scroll', debounce(storeScroll));

// Update scroll position for first time
storeScroll(); 
```

Enter fullscreen mode Exit fullscreen mode

通过将事件标记为`passive`,我们可以告诉浏览器我们的滚动事件不会被触摸交互取消(例如当与 Google Maps 这样的插件交互时)。这允许浏览器立即滚动页面，因为它现在知道事件不会被取消。

```
document.addEventListener('scroll', debounce(storeScroll), { passive: true }); 
```

Enter fullscreen mode Exit fullscreen mode

随着性能问题的解决，我们现在有了一个稳定的方法来将 JavaScript 获得的数据提供给我们的 CSS。我在下面设置了一个演示，所以你可以看看工作代码。

[https://codepen.io/rikschennink/embed/yZYbwQ?height=600&default-tab=js,result&embed-version=2](https://codepen.io/rikschennink/embed/yZYbwQ?height=600&default-tab=js,result&embed-version=2)

我对我们使用这种技术的其他方式非常感兴趣，所以如果你有任何想法，请在下面分享。