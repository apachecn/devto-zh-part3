# 通过增量加载提高页面速度

> 原文：<https://dev.to/djangotricks/improving-page-speed-with-incremental-loading-1ne4>

[![Improving Page Speed with Incremental Loading](img/19eb3473755e3546fe1d93fdcc151d31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2KitL6zr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-89TTMDEAZuY/XMN2pCTO6bI/AAAAAAAAB94/hWaxKVaurSYJuco4hfkINjVCv0SbX2r0wCLcBGAs/s1600/improving-page-speed-with-incremental-loading.png)

摘要:您可以使用 django-include-by-ajax 来提高网站的性能和可用性，方法是强制加载 django 网站页面的某些部分，并将其显示在页面的其他部分之前。

* * *

Web 浏览器从上到下、从左到右加载和呈现传统的 HTML 页面，作为开发人员，您很难控制首先、其次和最后显示什么。但是，有时您需要不同的加载顺序来改善用户体验和可用性。让我们来看几个案例，让主要内容立即显示，次要内容立即加载是有利的。

## 案例 1。折叠线以上与折叠线以下

人们想要速度。47%的访问者期望网站在不到 2 秒的时间内被加载。如果网站出现的时间超过 3 秒，这是一个很大的机会，你将失去 40%的访问者。如果你在你的网站上卖东西，每一秒钟的延迟都会导致 7%的访问者成为买家。

提高网站速度的一个技巧是尽快显示屏幕的可见部分，然后再一次加载网站的其余部分。通常，网站页面是长的垂直滚动区域。适合屏幕的部分称为“折叠上方”，下面的部分称为“折叠下方”。

[![Primary content above the fold and secondary content below the fold](img/f80b989389049fc1d1bc4c843627eb8e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3wQrQWgo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-UiqYyJGUREY/XMN2tCmqnfI/AAAAAAAAB98/Wm_dMsBwpKEfhBuqwnRQf2JPdm_rB4ZgwCPcBGAYYCw/s1600/above-the-fold-vs-below-the-fold.png)

建议分 6 次请求加载 fold 以上的部分，包括你所有的 HTML，CSS，JavaScript，图片，字体。只有 6 个请求是有原因的——这是大多数浏览器同时向同一个 HTTP/1.1 服务器发出的最大请求数。而 HTTP/2 则没有这样的限制。

如果您将 CSS 和 JavaScript 捆绑并最小化到单个文件中，并且只使用几个图像和字体，那么您只能实现这种最小的负载。更进一步，你可以把你的 CSS 和 JavaScript 分成两部分，一部分在折页上方使用，另一部分在折页下方使用。

## 案例二。主要内容与导航

为了让用户获得最佳的用户体验和流畅的加载，您可以先显示文章或博客文章的内容，然后在页眉、侧边栏或页脚中加载和显示网站导航。

[![Content is primary and the navigation is secondary](img/9ad905adefa11c2a2a9359836f8a4414.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oo09IFm0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2.bp.blogspot.com/-iItjGK_qgtE/XMN2tZknzQI/AAAAAAAAB-Q/-KEyx8ug-FgElBeW34wkyPV-2mOo2SUmgCPcBGAYYCw/s1600/content-vs-navigation.png)

如果访问者导航到您网站的某个特定页面，他们很可能希望看到该页面的内容，而不是导航到其他页面。

如果您有大量的嵌套导航，您还可以在第一次请求时节省几毫秒的加载时间，方法是跳过它，但在下一次请求时由 Ajax 加载。

此外，如果访问者禁用了浏览器中的 JavaScript，他们仍然可以阅读内容。

## 案例三。自有内容与第三方内容

你不同意吗，在内容之前显示广告的网站很烦人？改善用户体验的一个方法是首先显示主要内容，几秒钟后显示广告或第三方小部件。

[![Own content is primary and third-party widgets are secondary](img/147ea2a3a5f9ce5c0153a9bc0368d65e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x7nswftx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-PYN_NknLPhE/XMN2tWOQNOI/AAAAAAAAB-U/WPOdtPZc05MmOGZ6axMo286cu9wg1OM2wCPcBGAYYCw/s1600/content-vs-ads.png)

主要内容将被搜索引擎正确索引，而包含的小部件可能会被跳过，这取决于实现，我们接下来将对此进行研究。

## 解 1。Iframes

加载延迟的次要内容的一种方法是使用 iframes。

优点:

*   没有 JavaScript 也能工作。

缺点:

*   对于每个 iframed 部分，您需要一个带有自定义 CSS 的单独的 HTML。
*   你必须预先定义和硬编码每个次要部分的所有高度，所以它不能很好地处理增加或减少的字体大小或不同数量的内容。
*   你不能拥有超出 iframe 边界的交互式元素，比如工具提示。

## 解二。Ajax 的 API 调用

页面将加载次要内容的空占位符，然后一些 JavaScript 函数将通过 Ajax 加载 HTML、JSON 或 XML 格式的缺失部分的内容，解析它们，并包含到占位符中。脸书已经使用了这种方法[。](https://www.facebook.com/notes/facebook-engineering/bigpipe-pipelining-web-pages-for-high-performance/389414033919)

优点:

*   你可以对任何事情使用相同的全局 CSS。
*   内容的数量是灵活的，所以不同的变化设计会看起来很好。

缺点:

*   对于每个次要部分，您需要定义一个单独的 API 端点。
*   有许多额外的请求(除非您为此使用 GraphQL)。

## 解 3。使用特定查询参数对同一页面的第二次请求

该页面加载了用于次要内容的空占位符。一个 JavaScript 函数使用 Ajax 加载同一个页面的 HTML，这次包含所有呈现的主要和次要内容。然后另一个 JavaScript 函数遍历所有占位符并填充第二次加载的内容。

优点:

*   你可以对任何事情使用相同的全局 CSS。
*   内容的数量是灵活的，所以不同的设计可能看起来很好。
*   每个页面使用一个数据端点。
*   完整的 HTML 只需要一个额外的请求。

缺点:

*   如果有大量的主要内容，而没有太多的次要内容，那么加载和解析次要内容可能需要很长时间。

## 使用 django-include-by-ajax 实现 Django 网站

您可以使用我的开源 Django 应用程序 **django-include-by-ajax** 在 Django 网站中实现第三种解决方案。对于不接触 Python 代码但需要处理布局和样式的前端 Django 开发人员来说，它应该易于理解和使用。

这个想法是，不要用`{% include template_name %}`模板标签包含模板的不同部分，而是使用`{% include_by_ajax template_name %}`模板标签做同样的事情。除非您从搜索 crawler 访问页面，或者使用特定的查询参数访问页面，否则此模板标记呈现为空占位符。否则，它的工作方式与`{% include %}`模板标签大致相同。

通过将 jQuery 和一个基于 jQuery 的 JavaScript 文件添加到页面模板中，可以神奇地完成所有的加载和解析。从 1.0 版本开始，CSS 和 JavaScript 文件也可以包含在那些延迟的部分中。

你可以在我的个人项目[的首页看到 **django-include-by-ajax** 的运行。在这里，我使用了上面的例子，可视内容几乎立即出现在屏幕上，而屏幕外的内容在几秒钟后加载。](https://www.1st-things-1st.com)

## 安装

将任何标准的重型网站页面转换成动态加载次要内容的页面应该是微不足道的。主要遵循以下步骤:

### 1。安装应用程序

用你的 Python 包管理器安装应用:

```
(venv)$ pip install django-include-by-ajax==1.0.0 
```

Enter fullscreen mode Exit fullscreen mode

### 2。编辑 Django 项目设置

将应用程序放入 Django 项目设置中的`INSTALLED_APPS`:

```
# settings.py INSTALLED_APPS = [
    # ...
    # Third-party apps
    'include_by_ajax',
    # ... ] 
```

Enter fullscreen mode Exit fullscreen mode

### 3。修改基本模板

把这些放进你的`base.html` :

```
{% load staticfiles %}
<script src="https://code.jquery.com/jquery-3.4.0.min.js" crossorigin="anonymous"></script>
<script src="{% static 'include_by_ajax/js/include_by_ajax.min.js' %}" defer></script> 
```

Enter fullscreen mode Exit fullscreen mode

它也应该可以与较旧或较新的 jQuery 版本一起工作。

### 4。包括模板标签

在任何需要的模板中使用新的模板标签:

```
{% load include_by_ajax_tags %}
{% include_by_ajax "blog/includes/latest_blog_posts.html" %} 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以定义在加载主要内容时将显示的占位符内容:

```
{% load include_by_ajax_tags %}
{% include_by_ajax "blog/includes/latest_blog_posts.html" placeholder_template_name="utils/loading.html" %} 
```

Enter fullscreen mode Exit fullscreen mode

### 5。处理 JavaScript 事件

如果您需要在所有内容加载后调用一些 JavaScript 动作，您可以使用`document`上的自定义`include_by_ajax_all_loaded`事件，如下所示:

```
$(document).on('include_by_ajax_all_loaded', function() {
    console.log('Now all placeholders are loaded and replaced with content. Hurray!');
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果您在您的一些项目中尝试过它，并看到它如何改善用户体验和加载时间，我会很高兴。如果在生产中使用，请在这篇博文的评论中提及。

## 更多信息

Github 上的应用:[一个提供`{% include_by_ajax %}`模板标签](https://github.com/archatas/django-include-by-ajax)的 Django 应用

实际使用例子:[战略规划者-优先排序者-第一件事第一](https://www.1st-things-1st.com)。

一篇关于性能和可用性的文章:[访问者离开你的网站的 5 个原因](https://www.websitemagazine.com/blog/5-reasons-visitors-leave-your-website)

* * *

托马斯·塔克的封面照片。

感谢[亚当·约翰逊](https://adamj.eu/)审阅这篇文章。

[![](img/3f1d96261993d989942ad9fb623d9ec0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yxjiCzNL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/djangotricks/%257E4/RlHBMLgffPI)