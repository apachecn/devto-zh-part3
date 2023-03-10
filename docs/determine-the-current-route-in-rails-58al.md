# 确定铁路中的当前路线

> 原文：<https://dev.to/michael/determine-the-current-route-in-rails-58al>

假设您正在 Rails 应用程序中构建一个可爱的导航，并且您实现的设计显示您有不同的状态，它们有不同的视觉提示。

这些状态可能是非活动的、悬停的和活动的或当前的。不活动和悬停看起来很简单，你可以在 CSS 中完成所有这些。

```
.Nav-Item {
  // Styles for the "inactive" nav item state
  // would go here.
}

.Nav-Item:hover {
  // Styles for the "hover" nav item state
  // would go here.
} 
```

Enter fullscreen mode Exit fullscreen mode

但是活动或导航项目状态表明您当前与导航中的项目在同一个页面上，这不是一个简单的 CSS 解决方案。为了解决这个问题，您需要使用一个名为`current_page?`的内置 Rails 助手。

使用`current_page?`助手，你可以给它传递你想要检查的相对或绝对路径，或者你甚至可以给它传递带有参数的动作和控制器，让它检查。这样做将返回一个`true`或`false`。

这在你的标记中会是什么样子，

```
<%= link_to({ controller: 'page', action: 'about' }, { class: "Nav-Item #{'is-current-page' if current_page?(controller: 'page', action: 'about')}" }) do %>
  About Us
<% end %> 
```

Enter fullscreen mode Exit fullscreen mode

我在一个`link_to`助手块中使用了 [`current_page?`助手](https://api.rubyonrails.org/classes/ActionView/Helpers/UrlHelper.html#method-i-current_page-3F)。具体来说，它被传递给 class 属性，它说，如果当前页面`current_page?`与由`page`控制器中的`about`动作处理的页面相同，那么将`is-current-page`的值添加到 class 属性中。

现在我可以把它放在我的 CSS 中，

```
.Nav-Item {
  // Styles for the "inactive" nav item state
  // would go here.
}

.Nav-Item:hover {
  // Styles for the "hover" nav item state
  // would go here.
}

.Nav-Item.is-current-page {
  // Styles for the nav item that corresponds
  // with the current page the user is viewing
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们能够在 Rails 应用程序导航中处理非活动、悬停和当前页面状态。

* * *

最初[发布在 michaelsoolee.com](https://michaelsoolee.com/rails-current-route/)上。

感谢您花时间阅读这篇文章！我很想和你保持联系，通过我的时事通讯给你发送编程、设计和制作副业的建议。[点击这里报名](http://eepurl.com/bGXerj)。