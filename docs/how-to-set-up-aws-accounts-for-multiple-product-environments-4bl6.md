# 如何为多个产品环境设置 AWS 帐户

> 原文：<https://dev.to/paulswail/how-to-set-up-aws-accounts-for-multiple-product-environments-4bl6>

作为我正在进行的项目的一部分，我正在建立两个新的 AWS 账户，以便:

*   托管我将在迁移过程中创建的所有新资源(API Gateway、Lambda 等)
*   逐渐将现有资源从非隔离的旧帐户迁移到新帐户中
*   确保生产资源不会受到意外干扰
*   让我的开发工作流程尽可能高效

在这篇文章中，我将介绍如何建立一个组织结构，创建两个帐户(开发/试运行和生产)，创建 IAM 用户，并配置您的 CLI，以便将来易于管理。

## 组织结构

我将使用 [AWS 组织](https://aws.amazon.com/organizations/)来创建帐户。

*注意:如果你在一个公司环境中，你无权访问组织或主帐户，那么你可能需要让主帐户中的管理员为你做这件事。然后，您可以跳到下面的[设置 CLI 访问](#setting-up-cli-access)部分。*

我已经有了一个以我公司名称命名的主帐户，以及一个现有的子帐户(以后称为“传统”帐户)，我当前的所有基础架构都托管在该子帐户中。此传统帐户包含开发/试运行和生产阶段的资源，以及一些与产品无关的静态网站。我的长期目标是将适当的资源转移到新客户中，并彻底清除旧客户。

我还将为该产品使用一个“组织单位”，以防将来我希望对该产品的所有帐户应用策略，但不触及与我的其他产品相关的帐户。

以下是我的组织结构:

*   🏠`winterwindsoftware`(主公司账户，此处为账单)
    *   🔑`root`(根用户，MFA)
    *   🗂 `autochart`(特定产品的组织单位)
        *   🏠`autochart_legacy`
            *   🔑`root`(根用户，MFA)
            *   👥`sysadmins` (IAM 组，管理员访问策略)
            *   👤`paul` (IAM 用户，在`sysadmins`，MFA)
        *   🏠 **`autochart_dev`** (新账户，用于开发和暂存资源)
            *   🔑`root`(根用户，MFA)
            *   👥`sysadmins` (IAM 组，管理员访问策略)
            *   👤`paul` (IAM 用户，在`sysadmins`，MFA 可选)
        *   🏠 **`autochart_prod`** (新账户，针对生产专用资源)
            *   🔑`root`(根用户，MFA)
            *   👥`sysadmins` (IAM 组，管理员访问策略)
            *   👤`paul` (IAM 用户，在`sysadmins`，MFA)

使用 [AWS 组织控制台](https://console.aws.amazon.com/organizations)创建您自己的结构时，您可以用您的产品名称替换上面的`autochart`。

首先，以 root 用户身份登录主帐户，创建两个新帐户，输入您希望用于子帐户 root 登录的帐户名称和电子邮件地址。

🔥*提示:千万不要使用与现有个人 Amazon.com 账户关联的电子邮件地址作为你的 root 登录，因为[目前没有办法将其与 AWS 账户](https://forums.aws.amazon.com/thread.jspa?threadID=85882)断开。要解决这个问题，如果您使用的是 Gmail/Gsuite 电子邮件地址，您可以通过在地址的第一部分后添加一个加号来为每个帐户使用别名地址，例如`paul+myproduct_dev@mycompany.com`和`paul+myproduct_prod@mycompany.com`* 。

应对每个新账户重复以下步骤。

🔥提示:我强烈建议使用密码管理器来为你将要设置的所有用户存储和生成强密码。我使用[1 密码](https://1password.com/)，它也提供 2 因素认证功能。

## 以 root 用户身份首次登录新账户

为了以 root 用户身份首次登录您新创建的帐户，您需要根据[AWS 文档](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_access.html#orgs_manage_accounts_access-as-root)使用密码重置流程🤔。

🔥*提示:在此之前，由于您将登录多个 AWS 帐户，[在此阶段为您的开发和生产用户设置不同的浏览器配置文件](https://winterwindsoftware.com/aws-console-multiple-users/)，以便于同时访问每个帐户，并通过主题直观地区分每个帐户。*

## 为根用户激活多因素授权，并创建单个 IAM 用户

当您以 root 用户身份打开 [IAM 控制台](https://console.aws.amazon.com/iam)时，您会看到如下清单:

[![IAM checklist](img/f63388fa33de1e771d031814f9c02b4c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lCkFzQYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/iam-dashboard.png)

您现在应该在您的 root 帐户上激活 MFA，然后完成剩余的步骤。
具体来说，你应该用自己的名字为自己创建一个用户。确保为用户启用编程和控制台访问。我将这个用户添加到一个新创建的`sysadmins`组中，并在其中添加了`AdministratorAccess`策略。

[![Create IAM Group](img/2034b981c4ba3cbc85d204d143ac9fec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aD_9Y1tW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/iam-group.png)

## 为控制台登录创建账户别名

在 [IAM 控制台](https://console.aws.amazon.com/iam)的仪表板中，您可以创建一个友好的 URL 来登录控制台。这使您不必每次都在登录表单中输入帐户 ID。

[![IAM console URL alias](img/e2e09f69b06db5b63041cb59e659c102.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1b1_zJ2_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://winterwindsoftware.com/img/blog-images/iam-url-alias.png)

## 为您的生产帐户的 IAM 用户启用 MFA

这对于您的 dev 帐户来说并不是绝对必要的，但是因为我授予了我的 IAM 用户完全的管理员权限，所以我想确保它尽可能的安全。

要为您的 IAM 用户启用 MFA，请以 root 用户身份注销控制台，然后以您为自己创建的 IAM 用户身份重新登录，转到“用户”->“您的用户”->“安全凭据”选项卡，并查找分配的 MFA 设备。

您应该将这个 IAM 用户用于将来几乎所有的控制台访问。

## 设置 CLI 访问

虽然我已经使用 AWS 控制台完成了上述步骤，但几乎所有未来的配置和资源管理都将通过编程完成，要么通过 AWS CLI、Javascript SDK，要么通过另一种基础设施作为代码工具(例如无服务器框架或 CloudFormation)。

所以下一步是[使用终端安装 AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) 。然后[配置 CLI，使用之前为您的 IAM 用户](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)生成的访问密钥和密码。不要创建默认配置文件，因为这可能会导致您意外地对错误的帐户运行命令。请创建一个命名的概要文件。我喜欢使用下面的惯例来命名概要文件`<productname>_<environment>_<username>`，例如

```
 aws configure —-profile myproduct_dev_paul
# ... enter your access ID, secret key and default region when prompted 
```

Enter fullscreen mode Exit fullscreen mode

拥有这样独立的概要文件使得在你的无服务器框架服务中[设置每个阶段的概要文件](https://serverless-stack.com/chapters/configure-multiple-aws-profiles.html#set-profiles-per-stage)变得容易。

## 创建更多用户

虽然我只是一个团队中的一个人在开发我的产品，但有可能你团队中的其他同事也需要访问。您可以像创建自己的 IAM 用户一样，将他们设置为 IAM 用户。您需要自己判断授予他们什么级别的访问权限。AdministratorAccess 对于 dev 帐户可能没问题，但是您可能希望限制某些团队成员对生产帐户的访问。

## 退订营销邮件

最后，使用[这个表格](https://pages.awscloud.com/communication-preferences.html)取消订阅作为你的根用户的两个电子邮件地址。

* * *

💌 ***如果你喜欢这篇文章，你可以注册[到我的关于在 AWS 中构建无服务器网络应用的每周时事通讯](https://winterwindsoftware.com/newsletter/)。***
你也可能会喜欢:

*   [29 担心无服务器带走](https://winterwindsoftware.com/concerns-that-serverless-takes-away/)
*   [生产中的 AWS 无服务器应用案例研究](https://winterwindsoftware.com/real-world-serverless-case-studies/)
*   [“无服务器”的不同定义](https://winterwindsoftware.com/serverless-definitions/)

*原载于 winterwindsoftware.com*。