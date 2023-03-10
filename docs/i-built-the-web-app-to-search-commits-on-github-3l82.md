# 我在 GitHub 上构建了搜索提交的 Web 应用程序

> 原文：<https://dev.to/9sako6/i-built-the-web-app-to-search-commits-on-github-3l82>

开发者们好！

我在 GitHub 上构建了一个简单的提交搜索引擎:[https://9sako6.github.io/commit-stalker/](https://9sako6.github.io/commit-stalker/)

[![demo](img/6c20fa024067427f9d8afa1249f8f39d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vOnFAY9v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/946slu5cqwtlypt9r6xi.gif)

## 动机

我喜欢在 GitHub 上浏览库的提交。提交的历史教会了我开发库的过程，编写提交消息的方式等等。在 GitHub 官方网站上可以看到它们的历史，但是在 onece 上只显示了 30 次提交。这使得搜索更早的提交变得困难。
我希望在 onece 上看到更多的提交，并有效地搜索旧的提交。

## 用法

可在:[https://9sako6.github.io/commit-stalker/](https://9sako6.github.io/commit-stalker/)买到

很好用。请输入用户/组织和存储库名称。页码是可选的。然后，显示该回购的 100 次提交。如果您按下“> >”按钮，您可以显示包含最早提交的页面。

## 特性

我们可以在 onece 上显示 100 个提交日志。此外，很容易搜索存储库中最早的提交。

## 问题

现在有一个问题。无法通过他们的 API 获得 GitHub repo 的总提交计数。
[![total commits 1](img/d36923990046c7b29a8697cd3c4470d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tiUvJZTR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6hqunahj9c57zzuh0jz3.png) 
因此，我从贡献者的贡献中计算总提交计数。然而，我们最多可以通过 GitHub API 获得 100 个贡献者的信息，所以我无法计算一个有超过 100 个贡献者的回购的确切总提交数。

[![demo](img/40c05cfd0531e553aabc5258ecf98d4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jMTksRF4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dbqvpa1v6pm8ffxiaajx.png)

GitHub API 示例:

```
curl -i https://api.github.com/repos/sass/sass/contributors?anon=true 
```

Enter fullscreen mode Exit fullscreen mode

## 链接

如果有人感兴趣，请检查这个应用程序。这里:

[![](img/514e0e0912a1e1f2a80374713d8a4428.png)](https://github.com/9sako6/commit-stalker)

谢谢大家！！！