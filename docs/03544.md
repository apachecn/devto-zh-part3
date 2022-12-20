# 覆盖你的基础(元素)

> 原文：<https://dev.to/nikacodes/cover-your-base-element-5502>

`<base>`标签是`<head>`标签的子标签，它可以将特定的 URL 传递给 HTML 文档中的所有链接元素。

```
<head>
    // If the <base> tag contains a URL

    <base href="http://fakeurl.dev/">
<head> 
```

Enter fullscreen mode Exit fullscreen mode

你可以这样写`<a>`标签:

```
// A simple <a> tag without a specific 
<a>Link One</a>

// An anchor
<a href="#anchor">Link Two</a>

// A path from the original index.html of the URL
<a href="/fakepath">Link Three</a> 
```

Enter fullscreen mode Exit fullscreen mode

链接将指向如下:

```
// A simple <a> tag without a specific 
<a href="http://fakeurl.dev/">Link One</a>

// An anchor
<a href="http://fakeurl.dev/#anchor">Link Two</a>

// A path from the original index.html of the URL
<a href="http://fakeurl.dev/fakepath">Link Three</a> 
```

Enter fullscreen mode Exit fullscreen mode

您还可以传递一个特定的属性，比如一个`target`。

```
<head>
    <base target="_blank">
    // will ensure that all links will open in a separate tab.
</head> 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**一个头中只能有一个`<base>`标签。如果有多个值，它将只识别第一个`href`和`target`值。

[延伸阅读。](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base)

[CodePen 示例](https://codepen.io/sdras/pen/KLzyqr)作者[莎拉·德拉斯纳](https://twitter.com/sarah_edo)