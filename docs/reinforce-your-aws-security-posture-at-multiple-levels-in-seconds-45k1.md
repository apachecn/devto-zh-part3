# 在几秒钟内从多个层面加强您的 AWS 安全状况

> 原文：<https://dev.to/totalcloudio/reinforce-your-aws-security-posture-at-multiple-levels-in-seconds-45k1>

我们经常听说公开曝光的 AWS S3 桶或安全攻击，如 DDoS、暴力攻击等。AWS 中的每一个问题，无论是安全性、合规性还是账单峰值，都是由工程问题造成的。当谈到 AWS 安全性时，错误的安全组(SG)配置似乎高居榜首。

> AWS 安全审计不能像通常实践的那样每两周进行一次。这必须是日常活动。作为 AWS 从业者，我们都知道这是一项繁重的工作，尤其是使用 AWS 控制台的手动方法。

如果您是中小型企业或大型企业，您将有数百个实例在多个 SG 中运行。想象一下，为了保持安全态势，仅在 DeSecOps 活动上就投入了多少时间。

此外，在 AWS 上维护安全态势不是一个人的战斗。安全是每个人的责任。这是 CISO 或 CTO 和他的 DevSecOps 或 DevOps 工程师团队之间的团队合作。为此，每个人都需要从他们各自的工作角色的角度，从顶层到粒度级，在多个级别上了解他们的 AWS 帐户的安全状况。

# **提供实时 AWS 安全提示的集中视觉环境**

在动态 AWS 生态系统中，多种资源按需供应、扩展或缩减，忽略错误配置的可能性很高。因为在 SGs 下定义的规则被埋没在 KBs 的代码中，并且从 VPC 流量日志数据的日志中可视化流量对于人类思维来说是非常具有挑战性的。

> ### A single console provides everyone in the team with visual hints of any security vulnerabilities, which helps to increase the speed of identifying such vulnerabilities by 100 times!

让我们带您了解 CXO 或 DevSecOps 工程师必须执行的几项安全审计。看看在可视控制台上执行有多简单！

# **案例中的第 1 点**

## **检查与相关 IP 和安全组关联的开放 TCP/UDP 端口** :

对 TCP/UDP 端口的无限制入站/入站访问可能会招致恶意活动。典型的行动计划是检查实例的 SG 是否有允许无限制访问(即 0.0.0.0/0)这些端口的入站规则，并且只允许访问那些需要实现最小特权原则的 IP 地址。

在 AWS 控制台上，您需要通过 EC2 或 RDS 仪表板在网络和安全部分之间跳转，然后在安全组之间跳转。检查列表下方显示的选项卡。**这可能需要几分钟时间，具体取决于实例的数量。**T3】

但是，使用 TotalCloud，您可以检查入口和出口，并检查包含允许基础架构外部数据的资源的 SG。

观察下面的视频:

[https://www.youtube.com/embed/aZI0oS11BS0](https://www.youtube.com/embed/aZI0oS11BS0)

无论您使用多少个实例，这都需要 5 到 10 秒的时间。

# **案例二**

## **找到 SSH 开放端口，并确保它们仅用于跳箱/堡垒主机**

使用堡垒主机、NAT 实例或 NAT 网关的子网级网络安全有助于保护数据。允许现有私有实例的堡垒连接的 SG 必须只接受来自 AZs 上堡垒主机的 SSH 或 RDP 入站请求。

然而，入站&出站流量必须在协议级别受到限制，入站规则库应该只接受来自特定 IP 地址的 SSH 连接。出站连接应该再次限制为对 AWS 基础设施私有实例的 SSH 或 RDP 访问。

在 AWS 控制台上，您需要转到 VPC 控制台，向下滚动到 SGs 并检查每个 SG 下的入站和出站数据。这可能需要几分钟到几小时，具体取决于实例的数量。

但是，使用 TotalCloud，您可以检查入口和出口，并检查包含允许基础架构外部数据的资源的 SG。

观察下面的视频:

[https://www.youtube.com/embed/YZhnHimyvOI](https://www.youtube.com/embed/YZhnHimyvOI)

## **结论:为实时开发人员提供可视化提示的集中焦点视图**

变化是唯一不变的。在动态云环境中，有几个团队参与其中。拥有实时数据的集中视图作为漏洞的视觉线索，有助于实时识别安全问题。在多个级别放大和缩小的能力，以及过滤流量规则的能力，有助于团队中的每个人采取行动并保护他们的基础设施。

> ### With the immersive visualization of such data, anyone in the team can perform continuous security protection and know the security situation faster and better than the dashboard. Cloud's *security concept* is just like that! [Give it a try!](https://totalcloud.io/user-signup.html)

注:最初发布于 2018 年 11 月 28 日 [blog.totalcloud.io](https://blog.totalcloud.io/reinforce-aws-security-posture-multiple-levels-seconds/) 。