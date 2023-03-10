# 显示一系列帖子

> 原文：<https://dev.to/zeerorg/displaying-a-series-of-posts-3o43>

*我最初在我的[博客](https://blog.zeerorg.site/post/adding-series-to-blog)上发表了这篇文章。*

这是另一个我一开始没有想到的特性。我的灵感来自 dev 的系列专题，我甚至创作了我的第一个系列，这是该系列的第四篇文章。首页上的科技/休闲/专业版块不见了，它们占据了宝贵的空间，我很确定它们没有整理帖子，反而增加了混乱。

我想补充这一点的一个重要原因是因为我正在为下一个系列的帖子而努力。我不想破坏乐趣，所以我不会告诉我到底在做什么，但这些帖子需要连贯地联系在一起，才能更有意义。

[![Screenshot of the blog with series](img/20483b61335cc771dc08ae194b8441ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qlZvwIxS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/acaq65f16a2zcjdudxyh.png)

## Dev 的系列有限

我从 dev.to 那里得到了 series 的概念，但是我发现它的实现非常适合 dev，但是不适合我的博客。因此，我想出了一个更全面的设计，当它实现时，完全适合我的博客。

## 内容管理系统变更

在本系列的上一篇文章中，我已经简要地介绍了我的内容管理系统。我想让这个博客上的事情尽可能简单，并将大部分复杂性推向前端，这是架构中的主要处理层。为了保持简单，我的内容管理系统具有:

*   `main.json`:包含所有帖子的元数据。

```
[
  {
    "id": 4,
    "slug": "blog-development-live-preview",
    "published": true,
    "filename": "LivePreviewBlogChanges",
    "type": "tech",
    "tldr": "Adding live preview of changes when I'm writing the blog.",
    "title": "Live Preview Blog Edits",
    "timestamp": 1544608657,
    "dev_to": "https://dev.to/zeerorg/live-preview-blog-edits-2oh1",
    "series": "this-blog-series"
  },
  {
    "id": 6,
    "slug": "frontend-caching",
    "published": true,
    "filename": "FrontEndCaching",
    "type": "tech",
    "tldr": "A simple trick to improve site performance with frontend caching",
    "title": "Caching data in frontend",
    "timestamp": 1545049130,
    "dev_to": "https://dev.to/zeerorg/caching-data-in-frontend-3973"
  }
] 
```

*   `html/`:包含编译成 html 的降价内容。

*   `series.json`:包含系列的详细信息和该系列中的帖子列表。

```
{
  "this-blog-series": {
    "posts": [
      "this-blog-architecture",
      "blog-development-live-preview",
      "rss-feed-azure-functions",
      "adding-series-to-blog"
    ]
  }
} 
```

我引入了一个新的`series.json`，以便前端保持与现有的`main.json`兼容，我不必在前端计算系列中的帖子，这将更加复杂和容易出错。我在后端和前端复杂性之间找到了一个中间地带。

## 优化牺牲

我承认我的前端缓存设计不是最优化的方法。两个主要的高成本操作是:

1.  **每次从缓存中取数据时发送网络请求**。渲染道具组件 [`GetContent`](https://github.com/zeerorg/BlogSite/blob/master/src/Components/GetContent.jsx) 负责从后端缓存和加载数据，最初从缓存中获取数据，并向后端发送请求以获取最新数据。这种“新的更新”缓存为查看者提供了更好的体验，因为他们无需关闭浏览器会话就可以立即获得最新的更新，而且也不必等待网络请求完成才能获得第一次渲染。

2.  **解析文本到 JSON，每次我需要访问元数据**。每次需要获取`main.json`，`GetContent`进行调用，因此只返回原始文本版本，使用`JSON.Parse`实用程序进行解析。防止这种情况的唯一方法是引入 Redux 或为数据维护一个全局变量。这也意味着要区别对待 json 数据和 html 数据，这个责任将落在`GetContent`和任何获取数据的组件的肩上。与单次渲染时间相比，这种复杂性的增加肯定不会有多大帮助，因为每次渲染只调用一次解析。

## 结论

即使做出了一些牺牲，这个项目还是在没有令人头痛的情况下增加了一个新特性，我认为这是一个成就。我对这个功能的结果也很满意，期待您的反馈。