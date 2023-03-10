# 失踪的狩猎:一个锚、位置和互联网浏览器路径名的故事

> 原文：<https://dev.to/adamdawkins/the-hunt-for-the-missing-a-story-of-anchors-location-and-internet-explorer-s-pathname-2d4o>

## 用锚和位置的漂亮把戏

有一种很好的方法可以从不是当前`window.location`的 URL 中获取路径名。如果您将它们设置为锚元素的`href`，那么锚元素实际上实现了与`window`相同的`location`协议。

```
// on https://adamdawkins.uk/articles
window.location.pathname // => “/articles”

var url = document.createElement('a');
url.href = "https://adamdawkins.uk/articles";
url.pathname; // => "/articles" 
```

这能派上什么用场？在任何需要在客户端操作或分析 URL 的网站上，或者像我这样，当你使用`browser.pushState`但仍然想以同样的方式从`location`对象读取状态时。

在最近的一个项目中，我这样使用它:

```
//    getState :: Location -> Object
const getState = (location) => ({
    page: location.pathname,
    ...getQueryParams(location)
}) 
```

我会在页面加载时调用`getState(window.location)`，但是，当我用`history.pushState`更改 URL 时，我需要将那个 URL 传递给我的`getState`函数。不要试图改变`getState`的简单性(获取一个位置，返回一个对象，可爱的)——我们可以利用锚点实现位置协议的事实来获得我们可以传递给它的东西。

```
 //   toLocation :: URL -> <a>
const toLocation = (url) => {
  const anchor = document.createElement('a');
  anchor.href = url;

  return anchor
}

const newURL = setQueryParams({foo: 'bar'}) // returns the whole URL, e.g. "https://adamdawkins.uk/articles?foo=bar"
const newState = getState(toLocation(newURL)) 
```

注意，锚点 DOM 元素*本身*实现了位置协议，而不是 anchor.location。

## Internet Explorer 和相对路径名

可爱的东西。进入 Internet Explorer，舞台右侧。

Internet Explorer 获取相对于的*URL，并且不将最初的`/`视为`pathname`的一部分*

使用我们上面的代码:

```
// All other browsers 
toLocation("/articles?foo=bar").pathname // => "/articles"
// Internet Explorer
toLocation("/articles?foo=bar").pathname // => "articles" 
```

很好，谢谢。

也许我只是运气不好，但是我在匹配那个`/`来检查我在哪个页面:

```
if (state.page.match(/\/checkout/delivery\//)) {
 // …
} 
```

而且它会工作，除非我在 Internet Explorer 中推送状态，现在`state.page`是“结帐/交付”。

## 修什么？

这里有一个两难的问题。最简单的解决方法是放松我在页面上的正则表达式，不检查前面的`/`，但问题是——*这就是我的意思*——我确实想匹配`/checkout/delivery`，改变它感觉是错误的，因为 IE 对第一个`/`有焦虑问题。

我尝试的修复方法是创建一个简单的`pathname()`函数，如果它被 IE:
删除，它将替代`/`

```
//    pathname :: Location -> String
const pathname = (location) =>  (
    // in IE location.pathname strips the first `/` from relative URLs, here we add it back
    location.pathname.match(/^\//) ? location.pathname : `/${location.pathname}`
) 
```

唷。

本文最初发表于 [adamdawkins.uk](https://adamdawkins.uk/2019/6/5/the-hunt-for-the-missing-a-story-of-anchors-location-and-internet-explorers-pathname)