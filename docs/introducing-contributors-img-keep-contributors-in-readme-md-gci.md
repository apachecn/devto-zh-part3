# 介绍贡献者-img:将贡献者保留在 README.md 中

> 原文：<https://dev.to/lacolaco/introducing-contributors-img-keep-contributors-in-readme-md-gci>

嘿伙计们！我介绍了我创建的一个网络应用程序，**贡献者-img** 。
它生成一个图像来显示你的 GitHub 库的贡献者。

[![image](img/93525ea0b79a55d4a11607c60e246edc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m11KrRrs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2by6h6z64d79gka4ncsd.png)

[https://contributors-img.web.app/](https://contributors-img.web.app/)

## 为什么形象？

因为图片是 GitHub 上 README.md 中嵌入动态内容的唯一方式。

一旦你将生成的图片的 URL 粘贴到 README.md 中，它将毫不费力地保持贡献者列表的同步。

开源免费使用！

从技术上来说，contributors-img 建立在 Google Cloud Functions/puppet eer/Angular/GitHub API 之上。

## 告诫

*   有太多参与者的 repo 需要很长时间来生成映像。
*   对于每个存储库，每天都会生成一次图像，并且会被缓存以供以后请求。

谢谢！