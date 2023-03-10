# AWS DevOps Pro 认证博文系列:OpsWorks

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-opsworks-4hc4>

Fabrizio Magoni 在 [Unsplash](https://unsplash.com/photos/boaDpmC-_Xo) 上拍摄的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我试图完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以请确保定期重温博客帖子。

## 什么？

OpsWorks 群岛

*   提供三种托管服务
    *   AWS ops Chef 工作自动化
    *   AWS 为傀儡企业工作
    *   使用主厨 Solo 的 AWS OpsWorks 堆栈

在本帖中，我们将主要关注 AWS OpsWorks 堆栈，但要知道其他的是可以利用的托管服务，而不是自己运行(想想自托管数据库服务器与使用 RDS 的比较)。

OpsWorks 堆栈...

*   是一个声明性状态引擎(Chef Automate 和 Solo 也是如此)。通过这种方式，您可以定义您希望发生的事情，即运行一个 web 服务器，OpsWorks/Chef 将决定如何在安装了 Chef 代理的操作系统上执行该操作。回想一下，包管理器和配置文件存在于操作系统的不同位置，甚至存在于操作系统的发行版中(CentOS，Debian 等)。
*   与其他 Chef 托管服务相比也是独一无二的，因为它使用了“层”的概念
*   启用自动缩放和计划缩放
*   为您提供厨师 11 或 12 栈的选择。这两个版本的区别在于，Chef 11 有内置的食谱，而 Chef 12 允许你使用自己或社区的食谱。
*   具有层的概念(将这些层视为不同的功能层，即 web 服务器、数据库服务器)
    *   这些层可以基于 OpsWorks、ECS 或 RDS

其他资源:

*   常见问题
    *   [厨师自动化作业](https://aws.amazon.com/opsworks/chefautomate/faqs/?nc=sn&loc=5)
    *   [傀儡企业 ops works](https://aws.amazon.com/opsworks/puppetenterprise/faqs/?nc=sn&loc=5)
    *   [OpsWorks 堆栈](https://aws.amazon.com/opsworks/stacks/faqs/?nc=sn&loc=5)
*   [OpsWorks 堆栈用户指南](https://docs.aws.amazon.com/opsworks/latest/userguide/welcome.html)
*   蜜蜂
    *   [OpsWorks 堆栈](https://docs.aws.amazon.com/opsworks/latest/APIReference/Welcome.html)
    *   [OpsWorks 配置管理](https://docs.aws.amazon.com/opsworks-cm/latest/APIReference/Welcome.html)
*   central library and information services 中央图书馆与信息服务中心
    *   [OpsWorks 堆栈](https://docs.aws.amazon.com/cli/latest/reference/opsworks/index.html)
    *   [OpsWork 配置管理](https://docs.aws.amazon.com/cli/latest/reference/opsworks-cm/index.html)

## 为什么？

如果 CloudFormation 是最可配置的(也是最复杂的)编排工具(需要相当多的操作/基础设施经验),而 Elastic Beanstalk 是开发人员友好的，并且可以快速启动和运行。那么 OpsWorks Stacks 可能是中间地带。

CloudFormation 是一个专门针对 AWS 的工具，而您的 Chef 食谱可以在其他 Chef 安装上使用。

## 什么时候？

*   你对厨师很熟悉
*   想要一定程度的控制，但不想要复杂的云形成。

## 如何？

我没有时间为这一部分寻找 CLI 的等效物，如果你有我可以使用的资源，请给我留言！

我根据 Linux 的[入门指南](https://docs.aws.amazon.com/opsworks/latest/userguide/gettingstarted-linux.html)对其进行了粗略的描述，但有以下例外:

*   使用 Chef 11 堆栈
*   该应用程序是一个静态站点
*   应用程序部署是通过 https(GitHub 下载 zip 链接)进行的

*   转到 AWS 控制台并找到 OpsWorks 服务(如果您没有定义堆栈，您将位于介绍性页面，该页面提供有关堆栈和其他托管服务产品的信息)。

*   创建堆栈(厨师 11)

    *   提供堆栈名称
    *   其他一切都基于您选择的地区，即 VPC 和子网或 AWS(操作系统)提供的合理默认值
*   添加一个层(OpsWorks / ECS / RDS) -井钻入 OpsWorks

    *   将*图层类型*设置为`Static Web Server`
    *   层类型基于蓝图，可以是负载平衡、应用服务器(静态、节点、PHP 和 Rails)和一些其他类型(MySQL、Memcache、Ganglia 和 custom)。
    *   一个图层只能有一种类型。
    *   如果有可用的弹性负载平衡器，可以将其分配给它(它将成为一个新层)。如果您有多个实例，这是有意义的。
*   添加一个或多个实例

    *   *主机名* -将根据层类型进行预定义
    *   *大小*——阿罗格！阿罗格！aws 想把这个定为`c4.large`也许挑个`t2.micro`之类便宜点的？；)
    *   *子网*——您将希望将您的实例分布在不同的子网中。
    *   *高级*是您可以配置扩展类型(24/7，基于时间或负载)或覆盖操作系统和 SSH 密钥的默认设置的地方
    *   启动实例
*   添加应用程序

    *   提供一个名称
    *   将*库类型*设置为`HTTP Archive`(注:其他选项如 Git、Subversion、S3 等)
    *   将*存储库 URL* 设置为`https://github.com/booyaa/static-site-demo/archive/master.zip`
    *   点击添加应用*按钮
*   部署应用程序

    *   点击新创建的“应用”旁边的*部署*链接
    *   默认情况下，将选择与您的`Static Web Server`相关联的实例
    *   点击*部署*按钮
*   等到应用程序已经部署到您的实例，然后转到侧边栏中的*实例*链接，并单击您实例的*公共 IP* 。这将启动静态站点。

拆除:

*   删除应用程序
*   停止实例
*   删除实例
*   删除`Static Web Server`层
*   删除应用程序

## API 和 CLI 特性和动词

### 书库

**特性**

*   应用
*   情况
*   层
*   堆
*   用户概要

* *动词(CRUD) **

*   创造
*   形容
*   更新
*   删除

**离群值**

*   分配实例
*   分配-卷
*   关联弹性 ip
*   附加弹性负载平衡器
*   克隆堆栈
*   创建-部署
*   注销-ECS-集群
*   取消注册弹性 ip
*   注销-实例
*   注销 rds-d b-实例
*   注销-卷
*   描述-代理-版本
*   描述-命令
*   描述-部署
*   描述-ECS-集群
*   描述-弹性-ips
*   描述-弹性负载平衡器
*   描述-基于负载-自动缩放
*   描述我的用户资料
*   描述-操作系统
*   描述-权限
*   描述-raid 阵列
*   描述-rds-db-实例
*   描述-服务-错误
*   描述-堆栈-供应-参数
*   描述-堆栈-摘要
*   描述-基于时间-自动缩放
*   描述-用户简介
*   描述-卷
*   分离弹性负载平衡器
*   分离-弹性-ip
*   获取主机名建议
*   授权访问
*   列表标签
*   重新启动实例
*   注册
*   注册-ECS-集群
*   注册弹性 ip
*   注册实例
*   注册-rds-db-实例
*   寄存器卷
*   基于设置负载的自动缩放
*   设置权限
*   基于设置时间的自动缩放
*   开始实例
*   开始堆栈
*   停止实例
*   停止堆叠
*   标签资源
*   取消分配实例
*   取消分配-卷
*   UNTAG-资源
*   更新-弹性-ip
*   更新我的用户资料
*   更新-rds-d B-实例
*   更新-卷
*   等待

### 配置管理

**特性**

*   计算机网络服务器
*   发动机
*   支持
*   事件
*   帐户属性

* *动词(CRUD) **

*   创建(备份/服务器)
*   描述(备份/服务器/帐户属性/事件)
*   更新(服务器/服务器引擎属性)
*   删除(备份/服务器)

**离群值**

*   关联节点
*   描述-节点-关联-状态
*   取消关联-节点
*   导出-服务器-引擎-属性
*   还原-服务器
*   启动维护
*   等待

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*