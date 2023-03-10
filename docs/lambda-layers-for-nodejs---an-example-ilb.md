# NodeJS 的 Lambda 层-示例

> 原文：<https://dev.to/sriramn78/lambda-layers-for-nodejs---an-example-ilb>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[nsri ram](https://github.com/nsriram)/[AWS-lambda-layer-example](https://github.com/nsriram/aws-lambda-layer-example)

### aws lambda 图层示例

<article class="markdown-body entry-content container-lg" itemprop="text">

# NodeJS 的 Lambda 层-示例

本文概述了使用 lambda 层为库依赖项构建节点 js lambda 所涉及的步骤，使用了 [AWS CLI](https://github.com/aws/aws-cli) 。

该示例将构建一个 lambda 函数，该函数将使用 [momentjs](https://github.com/moment/moment/) 库返回当前时间。lambda 不会通过`package.json`、`node_modules`捆绑 [momentjs](https://github.com/moment/moment/) ，而是通过 lambda 层使用 [momentjs](https://github.com/moment/moment/) 。

假设您的计算机上有以下软件。

1.  [AWS](https://aws.amazon.com) 账户
2.  [IAM 角色](https://docs.aws.amazon.com/lambda/latest/dg/intro-permission-model.html)管理和执行 lambda 函数
3.  [AWS CLI](https://github.com/aws/aws-cli) 版本 **1.16.69**

## 1:创建并发布 momentjs lambda 图层。

什么是 lambda 层**(来源:AWS Docs)** :层是包含库、定制运行时或其他依赖项的 ZIP 存档。使用层，您可以在函数中使用库，而无需将它们包含在部署包中。

### 1.1 创建一个空 nodejs 项目。

```
> cd ~

> mkdir momentjs-lambda-layer

> cd
```

…</article>

[View on GitHub](https://github.com/nsriram/aws-lambda-layer-example)