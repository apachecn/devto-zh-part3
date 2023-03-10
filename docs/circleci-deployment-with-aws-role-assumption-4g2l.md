# 具有 AWS 角色假设的 CircleCI 部署

> 原文：<https://dev.to/yloganathan/circleci-deployment-with-aws-role-assumption-4g2l>

## 问题:

我们有三个独立的 AWS 帐户，分别用于开发、试运行和生产。我们有一个主帐户，我们使用角色假设来访问开发阶段的其余帐户。我们需要 CircleCI 使用 AWS 角色假设部署到不同的阶段。

注意:本文假设您已经掌握了 CircleCI 的工作知识以及如何为您的项目设置 CircleCI。

## 解:

我们使用 aws 配置、CircleCI 上下文和一些 yml 魔术解决了这个问题。

### 第一步:

创建一个配置文件`aws_config`并将其添加到。circleci 文件夹。

```
 [default]
   region = us-east-1
   output = json

   [profile dev]
   role_arn = arn:aws:iam::<account_id>:role/CircleCi-role
   source_profile = default

   [profile staging]
   role_arn = arn:aws:iam::<account_id>:role/CircleCi-role
   source_profile = default

   [profile production]
   role_arn = arn:aws:iam::<account_id>:role/CircleCi-role
   source_profile = default 
```

### 第二步:

CircleCI 通过上下文支持环境变量。

*   登录 CircleCI 并进入设置
*   创建一个组织全局上下文并添加 AWS_ACCESS_KEY 和 AWS_SECRET_KEY。
*   为每个阶段创建三个上下文，并添加 AWS_PROFILEenv 变量。在各自的上下文中将变量设置为“开发”、“暂存”和“生产”。

### 第三步:

在 CircleCI config.yml 中添加一个步骤，在 org-default 上下文中设置凭据和配置文件。您只需要这样做一次。的。aws 文件夹可以在作业间持久化和共享。

注意:我使用的是 python executor，pipenv 已经安装了 awscli 作为依赖项。

```
 - run:
          name: AWS configure
          command: |
            pipenv run  aws configure set aws_access_key_id $AWS_ACCESS_KEY
            pipenv run  aws configure set aws_secret_access_key $AWS_SECRET_KEY
            cp .circleci/aws_config ~/.aws/config 
```

这一步使用默认概要文件的 key_id 和 access_key 创建一个凭证文件。
上一步中的配置文件使用凭证文件中的概要文件。

### 第四步:

在 CircleCI 部署中将参数中的配置文件名传递给 aws cli 命令。

`aws s3 sync ./local s3://bucket --profile dev`

AWSCLI 命令也可以直接从 env 变量中读取

`aws s3 sync ./local s3://bucket`

如果您有自定义脚本或无服务器框架，您可以传递概要文件。

`sls deploy --aws-profile $AWS_PROFILE`

## 完整示例: