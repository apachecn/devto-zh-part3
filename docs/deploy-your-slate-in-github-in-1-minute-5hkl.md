# 1 分钟内在 Github 中部署您的 Slate doc 工具

> 原文：<https://dev.to/chenge/deploy-your-slate-in-github-in-1-minute-5hkl>

我的样本:[https://chenge.github.io/slate/](https://chenge.github.io/slate/)

[![](img/f72547132e287ce7c7d828d76594fd0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8VgVXQot--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wxm6xnewb1gxw4aojnpo.png)

将您的文档发布到 GitHub 页面

发布 API 文档再简单不过了。

```
Make sure you're working on a fork in your own account, not our original repo: git remote show origin.
Commit your changes to the markdown source: git commit -a -m "Update index.md"
Push the markdown source changes to GitHub: git push
Run ./deploy.sh 
```