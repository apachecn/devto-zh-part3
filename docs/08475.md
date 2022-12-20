# GitHub 操作指南使用 Node.js 实现 Git 工作流自动化

> 原文：<https://dev.to/datreeio/a-guide-to-github-actions-using-node-js-for-git-workflow-automation-43bc>

## 什么是 Github 动作？

GitHub Actions 是 2018 年 10 月在 GitHub Universe 期间宣布的一项功能，在 apt 定位为 git 工作流自动化的“瑞士军刀”下产生了巨大的宣传。

Github Actions 允许开发人员通过一个简单的文本文件，在 Github 工作流中自动执行任务，例如将提交推送到一个存储库，将一个发布部署到 staging，运行测试，删除特性标志，等等。

例如，[Chewy.com](http://chewy.com/)演示了一个动作，该动作检查每个拉取请求名称中是否包含[吉拉的票号](https://vimeo.com/295656803)，以确保部署到生产中的代码符合他们的策略和最佳实践。

### “有了 GitHub Actions，你可以自动化你从创意到生产的工作流程。”

–[GitHub 动作页面](https://github.com/features/actions)

## 在 Node.js 中编写 Github 动作

我一拿到私人测试版就开始修改这个功能。我注意到大多数动作都是用 shell 脚本编写的。GitHub 本身提倡在简单动作的 [shell 脚本中编写动作。虽然我理解这样做的动机(这样您就可以快速、轻松地开始编写动作)，但我觉得 shell 脚本在编写成熟的软件方面有局限性。](https://developer.github.com/actions/creating-github-actions/creating-a-new-action/#using-shell-scripts-to-create-actions)

因为我通常在 Node.js 中工作，所以我决定“接受挑战”,在 Node 中编写我的第一个动作。但是，在纠结于运行 Node.js 动作的细节并理解了简单容器和 GitHub 执行环境之间的差异之后，我决定写这篇教程，希望它能帮助那些喜欢用 JavaScript 编写动作的人。

## 环境设置

基本的设置很简单——从跟随 GitHub 的教程一直到 *entrypoint.sh* 部分开始。到目前为止，主要的区别在于所选择的 docker 图像。我建议使用[高山图像](https://github.com/nodejs/docker-node/tree/90043cdde5057865b94fec447ce193fb46b69e18#nodealpine)，与常规的[节点图像](https://github.com/nodejs/docker-node/tree/90043cdde5057865b94fec447ce193fb46b69e18#nodealpine)相比，它非常轻便。无论如何，我建议使用 LTS 变体，目前是节点 10。

一个非常重要的工具是 [Act](https://github.com/nektos/act) ，它帮助将开发周期从每次迭代 5 分钟缩短到仅仅几秒钟，这是一个零配置、易于使用的工具，可以在本地运行操作。它没有完全复制环境(由于显而易见的原因，它没有提供 GitHub 令牌，稍后会详细介绍)，但它足够接近于加速开发过程并在本地测试您的操作。

在这一步的最后，您应该有一个 docker 文件，看起来像这样: