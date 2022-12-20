# 为什么我使用 Kubernetes 进行游戏开发？

> 原文：<https://dev.to/balconygames/why-i-am-using-kubernetes-for-gamedev-2n14>

大家好。

这是一个非常好的时间来发布关于我正在努力学习和建立一些令人兴奋的新领域的新帖子。假设#gamedev 一直是我开始学习软件开发的目标，但是由于某些原因，我总是忙于其他事情。

好了，不谈过去了。Kubernetes 是一项非常令人兴奋的技术，是我独自开发后端和前端项目时发现的。

我目前的筹码数量是

*   [DigitalOcean](https://digitalocean.com) 是一个价格合理的托管平台，对我的独立项目总是有帮助。我想对公司说谢谢，因为他们总是开放来帮助，增加一些信用和降低价格。
*   Containership.io 是向 DigitalOcean 提供 Kubernetes 的一个非常好的平台。(没有这些家伙的任何信用，但平台帮助我节省了时间)。DigitalOcean 开始提供开箱即用的 Kubernetes，但我发现初学者很难使用。谈论 pod、服务、SSL 证书设置。可能这只是我的经历，当我完全接触到 Kubernetes 时，我很幸运地轻松完成了它。
*   [Kubernetes](https://kubernetes.io) ，不是说微服务。当你能用简单的食谱控制任何东西的时候，你就是英雄。
*   Gitlab 是一个源代码控制平台，每个存储库都有免费的容器注册表。

我的群集主机:

*   https://api.balconygames.com 是与 PostgreSQL (3 repl)对话的 Go 服务器
*   https://dashboard.balconygames.com 是 ReactJS 的游戏管理面板(nginx + static)。
*   https://sweety-jumper.balconygames.com 是 Unity 游戏(nginx + static ),但由于网络问题，它现在处于开发模式。
*   [https://balconygames.com](https://balconygames.com)是杰基尔(nginx + static)
*   PostgreSQL(使用 helm recipes 部署)
*   让 sencrypt 监视 ssl 证书并创建新的证书。

每月花费我大约***$ 40***(3 个节点乘 ***10$*** 和 1 个节点平衡器)对这些机器来说真的很便宜。

我的工作流程作为一个单独的开发者在后端。

*   进行代码更改。
*   重建映像并推送到 Gitlab 注册表
*   `kubectl apply -f recipe/<name of the app>/deployment.yml`包含`<image_name>:latest`或`<image_name>:$COMMIT_TAG`

下一步是将它从我的本地机器上移走，并使用 Gitlab 管道。
支持、部署简单，不浪费时间。

在一种情况下，需要在一天内设置 Redis 或其他数据库，我将使用 helm tool 和预定义的方法为 Redis 创建 pod、服务和部署。

***我们的游戏是[甜甜跳投](https://play.google.com/store/apps/details?id=com.balconygames.sweetyjumper)*T5】**