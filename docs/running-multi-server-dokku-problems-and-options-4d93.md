# 运行多服务器 Dokku:问题和选择

> 原文：<https://dev.to/firmhouse/running-multi-server-dokku-problems-and-options-4d93>

这篇博客文章收集了关于在高可用性(HA)或多服务器设置上通过 [Dokku](http://dokku.viewdocs.io/dokku/) 运行应用程序的资源和想法。由于 Dokku 不支持开箱即用的多服务器，但有一些努力使其工作，这篇文章是对现有选项的概述。

对我来说，在多服务器设置中运行应用程序有两个原因:

1.  最重要的是:能够应用维护更新、更新内核和重启服务器，而不会造成应用程序停机。
2.  确保我们的应用程序可以在负载增加的情况下“水平”扩展到多台服务器。

那么，要横向扩展 Dokku 部署，我们需要解决哪些问题呢？为了弄清楚这一点，让我们首先定义一个可以想象的最简单的多服务器 Dokku 设置。

## 可以想象的最简单的多服务器 Dokku 设置

一个简单的可以想象的设置是:一个负载平衡器(或反向代理)，和两个运行 Dokku 和应用程序的“后端”服务器。

在这种情况下，我们不会期望 Dokku 处理路由或负载平衡逻辑。这就是负载平衡器的作用。我们只是想让 Dokku 在附近的另一个服务器上有一个兄弟，我们想让维护变得尽可能容易。

那么，为了实现这一目标，我们需要解决哪些问题呢？

## 多服务器 Dokku“缺”什么

最重要的是所有的应用程序定义、配置选项、环境变量、域名、SSL 证书等。都存储在服务器上。这意味着当运行两个 Dokku 服务器时，所有应用程序配置都必须在“最简单的可想象的多服务器 Dokku 设置”中的两个服务器上定义并保持同步。

所以我们有一个问题:**保持配置同步**

第二个问题是将域映射和 SSL 终端转移到负载均衡器。Dokku 提供了强大的机制来将域映射到应用程序，安装 SSL 证书，或者通过 [dokku-letsencrypt 插件](https://github.com/dokku/dokku-letsencrypt)使用 LetsEncrypt。

然而，由于流量将进入负载平衡器(或反向代理)，它需要接管识别域名的角色，拥有 SSL 证书，并通过内部网络或内部加密的自签名证书将其余流量转发到后端服务器。

所以我们有第二个问题:**将基础设施的路由和证书部分转移到负载均衡器**

## 那么有哪些选项呢？

以下是我认为可能对开始考虑多服务器 Dokku 设置有用的选项列表:

1.  (实际上不是一个选项)设置两个 Dokku 服务器，并手动保持它们同步。然后从你的云提供商那里放一个负载平衡器，或者在前面放一个类似于 [Treafik](https://traefik.io) 的反向代理来处理 SSL 证书并路由到两个 Dokku 服务器。
2.  通过 Ansible 自动维护您的 Dokku 服务器。GitHub 上有一个新的知识库，josegonzalez 正在那里开发 Ansible 脚本来维护 Dokku 服务器。通过 Ansible 管理 Dokku 安装及其应用程序设置，它已经使保持两台 Dokku 服务器及其应用程序设置“同步”变得更加容易。您仍然需要在两台服务器前放置一个负载平衡器或反向代理。这里一个更大的挑战是管理秘密。如果您通过 Ansible 配置您的服务器，您将需要设置某种 secrets vault 或其他方法，以便在 Ansible 运行更新您的服务器和应用程序定义时将秘密注入其中。
3.  使用不同的工具在您自己的服务器上设置您自己的 PaaS。这篇关于 ServerFault 的文章似乎是用最近的选项更新的，但我还没有亲自看一看:[https://server fault . com/questions/640038/scaling-out-do kku-infra structure](https://serverfault.com/questions/640038/scaling-out-dokku-infrastructure)
4.  等待[城际](https://www.intercity.io)支持多服务器设置。城际是我们在 Firmhouse 内部为 Dokku 建立的管理面板。这是一个开源项目，你可以在自己的服务器上运行。我们目前正在开发一项功能，以保持多个服务器之间的应用程序设置同步。