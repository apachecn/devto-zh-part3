# 如何用 Sinatra 创建无 DB 博客？

> 原文：<https://dev.to/shpigford/how-to-create-a-no-db-blog-with-sinatra-12bg>

我正在用 Sinatra 建立一个网站，这个网站将会有一个博客区，我很少会在那里发表文章。

理想情况下，我想做的是有一个文章所在的降价文件目录，要发布一篇新文章，我只需添加一个新的降价文件。

主`/articles`页面将遍历该目录，并从 markdown 文件的元数据中提取标题。

```
---
title: This is my title
excerpt: This is an excerpt from the article
--- 
```

所以，我不确定的是如何处理这个问题。我如何循环浏览降价文件的目录并正确显示它们？

FWIW，我这里用的是“经典风格”的 Sinatra 应用。