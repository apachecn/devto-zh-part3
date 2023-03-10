# AWS 传统负载平衡器与应用程序负载平衡器

> 原文：<https://dev.to/garyker/aws-classic-load-balancer-vs-application-load-balancer-12m0>

弹性负载均衡支持两种类型的负载均衡器:应用负载均衡器和传统负载均衡器。虽然在功能上有一些重叠，但 AWS 并不维护这两种负载平衡器之间的功能对等性。下面的内容列出了两者的功能比较。

使用模式

经典负载均衡器非常适合在多个 EC2 实例之间进行简单的流量负载均衡，
应用负载均衡器非常适合需要将流量路由到多个服务或在同一 EC2 实例的多个端口之间进行负载均衡的微服务或基于容器的架构。
AWS ELB 传统负载平衡器与应用负载平衡器
支持的协议

传统负载平衡器在第 4 层运行，支持 HTTP、HTTPS、TCP、SSL，而应用负载平衡器在第 7 层运行，支持 HTTP、HTTPS、HTTP/2、WebSockets
如果需要第 4 层功能，应使用传统负载平衡器
支持的平台

经典负载平衡器支持 EC2-经典和 EC2-VPC，而应用负载平衡器仅支持 EC2-VPC
坚持会话(Cookies)

粘滞会话(会话关联)使负载平衡器能够将用户的会话绑定到特定实例，从而确保会话期间来自用户的所有请求都发送到同一个实例
两个经典&应用负载平衡器都支持粘滞会话以保持会话关联
空闲连接超时

空闲连接超时有助于指定一个时间段，如果在空闲超时时间段过去之前没有发送或接收数据，ELB 将使用该时间段关闭连接
两个经典&应用程序负载平衡器都支持空闲连接超时
连接排空

连接排出使负载平衡器能够完成对正在注销或不正常的实例发出的请求
两个经典&应用负载平衡器都支持连接排出
SSL 终止

经典负载平衡器和 ALB 都支持 SSL 终止，在将请求发送到目标之前对来自客户端的请求进行解密，从而降低负载。SSL 证书必须安装在负载平衡器上。
后端服务器认证

后端服务器身份验证支持实例的身份验证。仅当实例提供给负载平衡器的公钥与负载平衡器的身份验证策略中的公钥匹配时，负载平衡器才与实例通信。
传统负载均衡器支持，而应用负载均衡器不支持后端服务器认证
跨区域负载均衡

跨区域负载平衡有助于在其启用的 AZs 中的所有实例之间平均分配传入的请求。默认情况下，负载平衡器会在其启用的 AZs 之间均匀地分配请求，而不管它承载的实例是什么。
两个经典&应用程序负载平衡器都支持跨区域负载平衡，但是对于经典应用程序，它需要启用，而对于 ALB 应用程序，它始终处于启用状态
运行状况检查

经典和应用负载平衡器都支持健康检查，以确定实例是健康还是不健康
ALB 提供健康检查改进，允许配置 200-399 的详细错误代码
CloudWatch 指标

传统和应用负载平衡器都与 CloudWatch 集成以提供指标，而 ALB 提供额外的指标
访问日志

访问日志捕获关于发送到负载平衡器的请求的详细信息。每个日志都包含收到请求的时间、客户端的 IP 地址、延迟、请求路径和服务器响应等信息
两个经典&应用负载平衡器都提供访问日志，ALB 提供附加属性
基于主机的路由&基于路径的路由

基于主机的路由使用主机条件来定义规则，根据主机头中的主机名将请求转发到不同的目标组。这使 ALB 能够使用单个负载平衡器支持多个域。
基于路径的路由使用路径条件来定义规则，根据请求中的 URL 将请求转发到不同的目标组。每个路径条件都有一个路径模式。如果请求中的 URL 与侦听器规则中的路径模式完全匹配，则使用该规则路由请求。
只有 ALB 支持基于主机的&基于路径的路由。
动态港口

只有 ALB 支持 ECS 的动态端口映射，它允许一个服务的两个容器在一台服务器上运行，动态端口由 ALB 自动检测和重新配置。
删除保护

只有 ALB 支持删除保护，如果启用了删除保护，负载均衡器就不能被删除
请求跟踪

只有 ALB 支持请求跟踪来跟踪从客户端到目标或其他服务的 HTTP 请求。
IPv6 在 VPC

VPC 只有 ALB 支持 IPv6
AWS WAF

只有 ALB 支持 AWS WAF，它可以在 VPC 中的 ALB(内部和外部)上直接使用，以保护网站和 web 服务