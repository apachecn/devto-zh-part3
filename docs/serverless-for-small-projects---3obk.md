# 小型项目的无服务器=💯

> 原文：<https://dev.to/bryce/serverless-for-small-projects---3obk>

无服务器是一个[事件驱动的框架](https://serverless.com/)，我可以自信地说，它现在是我的小项目的**首选。配合 [AWS](https://aws.amazon.com/) 提供的一键式 devops 解决方案，我可以将时间投入到项目本身，而不是试图立即实现一个成熟的生产架构。**

[![](img/697168dcf49f026e11d2fded7c5dab20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ipV6F4tM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/serverless/assets/master/Icon/Framework/PNG/Serverless_Framework-icon01.png)

我最近开始了一个兼职项目，并选择了无服务器来看看它到底是怎么回事。这里有一些*啊哈！*让我对自己的决定感到满意的时刻:

#### 与 [`serverless-webpack`](https://github.com/serverless-heaven/serverless-webpack) ⚙️熟悉建造过程

*   node/React/TypeScript/Babel/其他流行语

#### 100%正常运行时间⏱

*   不像其他自由等级那样“睡觉”**咳嗽** [英雄](https://devcenter.heroku.com/articles/free-dyno-hours#dyno-sleeping)**咳嗽**

#### 即时本地服务器与 [`serverless-offline`](https://github.com/dherault/serverless-offline) 🛠

*   无需配置即可模拟 [AWS λ](https://aws.amazon.com/lambda) 和 [API 网关](https://aws.amazon.com/api-gateway)

#### 毫不费力的展开🚀

*   使用`sls deploy`部署到 CloudFront
    *   用 [`serverless-domain-manager`](https://github.com/amplify-education/serverless-domain-manager) 自定义域名
*   用 [`serverless-finch`](https://github.com/fernando-mc/serverless-finch) 部署到 S3

#### 易域/SSL🔒

*   在 Route53 上购买域名，一键 DNS 配置
*   免费的[亚马逊颁发的 SSL 证书](https://aws.amazon.com/certificate-manager/)，一键使用 CloudFront

* * *

为了兼顾双方，以下是我的一些抱怨:

*   初始 AWS 凭证设置是一件麻烦的事情，但只需要做一次。
*   我暂时被[锁在](https://www.theregister.co.uk/2017/11/06/coreos_kubernetes_v_world/)里。但很明显，如果我违反了 AWS 法案的 10 万美元，我就有资源来建立 Kubernetes。

这就是我在这种情况下使用无服务器的原因。对于小项目，权衡是不言而喻的。请继续关注如何开始你自己的生活！⚡️