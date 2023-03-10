# 实施网络安全

> 原文：<https://dev.to/cheahengsoon/implement-network-security-3fil>

**建立网络**

*   Azure 有两种不同的部署模型来创建和使用资源:Azure 资源管理器和 classic
*   Azure 资源管理器是部署和管理服务，它提供一致的管理层，并允许您创建、更新和删除 Azure 订阅中的资源
*   Microsoft 建议通过资源管理器部署模型创建大多数新的虚拟网络
*   Azure 网络组件包括:
    *   虚拟网络
    *   IP 地址
    *   子网络
    *   网络接口卡
    *   域名服务器(Domain Name Server)
*   Azure 提供名称解析服务，使 Azure 内的虚拟机和云服务能够通过名称

    [![Alt text of image](img/0f7e890a7f6702119c592ef0a7bf6eb8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5znayEis--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h9thsn8rhotnv91b9x1f.png) 进行通信

*   为了提高可用性和可扩展性，您可以创建两个或更多发布同一应用程序的虚拟机

*   您可以使用 Azure 负载平衡器来实现虚拟机之间的流量分配

*   Azure 负载平衡器有两种类型:

    *   公共负载平衡器
    *   内部负载平衡器
*   负载平衡器可以探测各种服务器实例的健康状况

*   源自您的服务的所有互联网出站流量都将通过使用与入站流量相同的 VIP 地址进行源网络地址转换(SNAT)

*   应用程序网关为基于 HTTP 协议的网络流量提供负载平衡解决方案

*   Azure 流量管理器是 Azure 包含的另一个负载平衡解决方案

*   交通管理员可以:

    *   提高关键应用程序的可用性
    *   提高高性能应用的响应能力
    *   无需停机即可升级和执行服务维护
    *   结合内部部署和基于云的应用程序
    *   为大型复杂部署分发流量
*   流量管理器在 DNS 级别工作

*   网络安全组

    *   通过定义允许或拒绝特定流量流向单个虚拟机或子网的规则，您可以使用网络安全组为 Azure 资源提供网络隔离
*   用户定义的路线

    *   用户定义的路由(UDR)通过定义指定通信流下一跳的路由来控制网络通信
*   强制隧穿

    *   通过强制隧道，您可以将互联网流量重定向回公司的内部基础设施
*   区域虚拟网络

    *   区域性虚拟网络可以跨越整个 Azure 区域或数据中心
*   跨场所网络连接

    *   Azure 中的虚拟网络还允许您将内部网络扩展到云中

**创建虚拟网络和子网**
您可以使用 Azure 门户、Azure CLI 1.0 或 PowerShell
[![Alt text of image](img/3be8431496366796cb4bc54f67308d79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--77r-e2YR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1uszmk673cwnbbpv7kix.png) 创建虚拟网络

**创建网络负载平衡器**

*   Azure 负载平衡器有两种 SKU:基本和标准
*   您可以使用 Azure 负载平衡器来:
    *   对流入虚拟机的互联网流量进行负载平衡
    *   虚拟网络中跨虚拟机的负载平衡流量
    *   端口将流量转发到特定虚拟机上的特定端口
    *   为虚拟网络内的虚拟机提供出站连接

**Azure 流量管理器**

*   Traffic Manager 使用 DNS 根据流量路由方法和端点的健康状况将客户端请求定向到最合适的服务端点
*   Traffic Manager 提供了一系列流量路由方法和端点监控选项，以适应不同的应用需求和自动故障转移模式
*   流量管理器对故障具有弹性，包括整个 Azure 区域的故障![Alt text of image](img/c08235da730dde872443e90abd6be380.png)

**配置虚拟网络网关**

*   Azure VPN 网关作为跨场所网关，将 Azure 虚拟网络中的工作负载连接到内部站点
*   有必要通过 IPsec S2S VPN 隧道或 ExpressRoute 电路连接到内部站点![Alt text of image](img/81bd929a5e3b7371acd377b6f1df4e43.png)