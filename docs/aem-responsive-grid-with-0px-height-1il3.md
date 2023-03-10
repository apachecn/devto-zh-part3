# 高度为 0px 的 AEM 响应网格

> 原文：<https://dev.to/bearcherian/aem-responsive-grid-with-0px-height-1il3>

在 AEM 中，当您使用响应网格(`wcm/foundation/components/responsivegrid`)创建一个带有可编辑区域的新页面组件时，您可能会遇到一个问题，可编辑区域出现了，但是没有高度，并且不可用。你得到的只是一条蓝色的细线，应该在可编辑区域。

[![Screenshot of unusable editable area](img/2b1273e15e0aa14b8ad0d5a39f86e994.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_E4uttIS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fjyznpqt1kqgzexdvwe4.png)

这是因为缺少 include。为了初始化 AEM 编辑特性，需要加载一个 JSP 来初始化所有的可编辑区域，以及其他一些东西。

解决方案是在页面组件的`<head>`中添加以下内容

```
 <!--/* Initializes the Experience Manager authoring UI */-->
    <sly data-sly-include="/libs/wcm/core/components/init/init.jsp" data-sly-unwrap/> 
```

你可以删除评论，但是我喜欢把它留在里面，这样我或者其他编辑页面组件的人就知道为什么我们把它放在那里了。现在，您将看到准备添加组件的可编辑区域。

[![Screenshot of working editable area](img/4930c463df9abf8695fae725736b2219.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Vilwqkc9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f0l4q5udxc8ueo4kqlr8.png)

感谢用户 manasip9 在 Adobe 论坛上就这个主题发表了[他们的回答。](https://forums.adobe.com/message/9942397#9942397)