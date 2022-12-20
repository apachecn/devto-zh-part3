# 专用网络+ Traefik + Keycloak +网守概述(图表)

> 原文：<https://dev.to/karlredman/private-network-traefik-keycloak-gatekeeper-overview-diagram-1nmn>

Traefik、Keycloak 和网守协同工作的高级网络概述

这是我正在做的一个项目中的另一个工件(虽然很丑)。此图描述了一个基本的实验室基础架构，其中 Traefik、Keyclaok 和 key claok 网关守护设备在本地 DNS (dnsmasq)后协同工作。关于所有这些实际上是如何组合在一起的细节正在讨论中。现在，希望它能对那些想知道当配置这些不同的组件一起工作时布局是什么样子的人有所帮助。注意`whoami`是在[之前的帖子](https://dev.to/karlredman/keycloak-v5-gatekeeper-v5-flowcharts-easily-create-and-restrict-an-isolated-iodc-client-service-by-group-role-53h4)中描述的服务。

*   原始 PlantUML 源文件+图像文件:[此处](https://github.com/karlredman/My-Articles/tree/master/Artifacts/network-diagrams)
*   编辑的原始源:
    *   vim[iam CCO/markdown-preview . nvim :( neo)vim](https://github.com/iamcco/markdown-preview.nvim)的 markdown 预览插件
    *   vs code[plant UML-Visual Studio 扩展](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)

[![kc-kcgc-traefik_overview-1.png](img/2f735fd01f2b892e86c2729c185063a4.png)](https://raw.githubusercontent.com/karlredman/Articles/master/content/dev.to/traefik%2Bkeycloak%2Bgatekeeper_diagram_overview/kc-kcgc-traefik_overview-1.png)