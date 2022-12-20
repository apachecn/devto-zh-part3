# 2 分钟 AWS:概述、历史、旗舰产品和案例

> 原文：<https://dev.to/billywitherspoon/aws-in-2-minutes-a-summary-a-history-a-flagship-product-and-a-case-example-1ef3>

## 一个总结

AWS(亚马逊网络服务)是亚马逊的子公司，提供基于按需云的平台，允许各种规模的企业通过计算能力、数据库存储、内容交付和其他功能来扩展和增长。

## 一段历史

<figure>[![](img/40088a2cb0f73816fc28f038d0787b74.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tCX3-lcH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i4dzxh7xh0jh6rvpmvbb.jpg) 

<figcaption>南非开普敦。AWS 的发源地。</figcaption>

</figure>

#### 2003 年

AWS 始于本杰明·布莱克和克里斯·平克汉姆。这两位亚马逊员工为一项服务写了一篇愿景论文:

> "完全标准化、完全自动化，并且在存储等方面广泛依赖于 web 服务."

#### 2004 年

杰夫 B 给这个项目开了绿灯。在南非长大的 Pinkham 在开普敦设立了一个卫星办公室。他与一个团队合作开发一款名为弹性计算机云(EC2)的旗舰产品。

#### 2006 年

AWS 正式推出他们的前三款产品:

*   EC2(弹性计算机云)
*   S3(简单存储服务)
*   SQS(简单队列服务)

所有这三个仍然是前 10 名的 AWS 服务(大约 100)。

## 一款旗舰产品

[![](img/38cff4d4b6a36e6497104d1eada63474.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TcMHPicc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xkdgihai31hf4qydajf4.png)

#### 什么事？

> EC2 是一项服务，允许客户按需租用亚马逊资源来创建虚拟机(AWS 称之为“实例”)。

本质上，AWS 维护他们的服务器。客户端可以从这些服务器“租借”计算能力。客户端创建具有不同属性(如 CPU、RAM 或操作系统)的“实例”。选项范围从按需实例到按小时付费的保留专用主机。

#### 有什么好处？

##### 可伸缩

一夜之间，几个月之后，计算需求会发生变化...还是一整年？客户端可以在不到 10 分钟的时间内配置新实例。它被宣传为可无限扩展，适用于任何规模的解决方案。

##### 没有维护和升级

客户无需维护、维修、监控和升级设备。

##### 可靠

和很多主流云服务一样，可靠性极高。快速浏览一下 2015 年的旧对比图,显示可靠性评级超过 99.99%。

## 一个案例示例(Expedia ESS)

[![](img/4dadb29bdc672b7aa44900f6909497c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ygWDZ1Ad--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c3occpkr5h469elxqwjd.png)

大公司转向 EC2 的一个很好的例子是 2010 年的 Expedia。他们在亚洲和欧洲推出了名为 ESS (Expedia 建议服务)的提前输入建议服务，帮助客户正确输入旅行、搜索和位置信息。

内部研究发现，错误页面是网站废弃的主要原因。

现有的 Expedia 数据中心都位于亚利桑那州的钱德勒。引用技术总监 Murari Gopalan 的话说:

> “没有 AWS，我们无法解决这个问题。”

迁移和实施后，延迟从 700 毫秒减少到 50 毫秒。

#### 资源:

[AWS EC2](https://aws.amazon.com/ec2/)T2*亚马逊网络服务 EC2*

[AWS 的时间轴](https://en.wikipedia.org/wiki/Timeline_of_Amazon_Web_Services)
*维基百科*

[亚马逊如何暴露自己的胆量:AWS 的 EC2](https://www.zdnet.com/article/how-amazon-exposed-its-guts-the-history-of-awss-ec2/)
*的历史杰克·克拉克，ZDNEt*

[AWS 云服务 30 强](https://www.networkworld.com/article/3074508/top-30-aws-cloud-services.html)
*Brandon Butler，网络世界高级编辑*

[AWS 云可靠性最高](https://www.enterpriseai.news/2015/01/06/aws-rates-highest-cloud-reliability/)
*乔治·利奥波德，企业家*

[什么是 AWS](https://www.youtube.com/watch?v=wWeyzYzd17o)
*edureka！，YouTube*