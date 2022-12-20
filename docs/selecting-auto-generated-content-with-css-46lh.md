# 使用 CSS 选择自动生成的内容

> 原文：<https://dev.to/gabe/selecting-auto-generated-content-with-css-46lh>

我们公司的网站最近从 WordPress 搬到了 Hubspot。因此，我们的团队一直在削减 JIRA 门票，解决迁移中可能存在的任何问题。因此，我的任务是为网站建立一个新的联系页面，我们目前有一个联系模式，可以打开并覆盖在你所在的页面上。我从来都不是这种方法的粉丝，当我在任何视频上打开该模式时，都会遇到一些主要的性能问题。联系方式有时会运行缓慢，几乎无法使用。令人欣慰的是，新的联系方式被设置成了独立的页面——解决了我们的绩效问题。当我们构建联系人表单时，我们遇到了 HubSpot 处理表单的方式的问题。通常来说，对于任何不是我自己构建的站点上的主题元素，我使用浏览器检查工具来选择我需要的元素，然后我可以看到它的样式、类名、id 等等。今天，在尝试对一些输入元素进行主题化时，我意识到 HubSpot 会在每次页面刷新时自动生成每个输入的 id。这使得瞄准元素变得困难，但据我所知，并非不可能。虽然它是自动生成的，但前几个字符似乎是输入字段的机器名称。这就把我们带到了 CSS 选择器

```
 div[class^="foo"]
```

The selector targets an element, a div in this case, that has a classname that starts with foo. Pretty useful. There's also another selector that does similar

```
div[class*="foo"]
```

The difference in this one being the carrot symbol is now an asterisk, what that does is targets a div that has a classname that contains foo.