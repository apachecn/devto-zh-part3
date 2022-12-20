# 盖茨比火力基地部署

> 原文：<https://dev.to/mattchewone/gatsby-firebase-deployment-3idi>

最近发布了我与 Gatsby 的个人站点后，我想用 Gitlab 建立一个基本的管道，以便在我向`master`分支推送更改时部署我的站点。这将是有益的，这样，如果我创建另一个分支，部署过程将不会在其他分支上启动。

## Gitlab CI