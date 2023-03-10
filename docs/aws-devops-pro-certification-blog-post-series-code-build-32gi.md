# AWS DevOps Pro 认证博客文章系列:代码构建

> 原文：<https://dev.to/booyaa/aws-devops-pro-certification-blog-post-series-code-build-32gi>

凯利·西克玛在 Unsplash 上的照片

## 概不退换

使用 AWS 要花钱，其中一些服务可能不属于 AWS 免费层。你可以通过拆除你在学习时创造的任何东西来降低成本，但仍然有可能产生巨额账单，所以要注意你设置的实例！

我非常幸运能够使用我雇主的 AWS 帐户。你应该问问你工作的地方，是否可以做类似的安排作为你学习的一部分。

## 谁快窒息征服谁

当我尝试完善我的每个领域的心智模型时，博客帖子的格式很容易改变，所以一定要定期重温博客帖子。

## 什么？

代码生成是一种托管的生成服务。

## 为什么？

除了托管您的应用程序和服务所需的基础设施，构建农场是下一个最大的支出。此外，配置构建场的编排也很重要。Travis CI、Circle CI 和 Appveyor 等托管构建服务帮助开源项目蓬勃发展，并允许维护人员支持广泛的 CPU 和操作系统。

## 什么时候？

SDLC 自动化:`CodeCommit -> [CodeBuild] -> ???`

## 如何？

这大致基于用户指南的[入门][https://docs . AWS . Amazon . com/code build/latest/User Guide/Getting-Started . html](https://docs.aws.amazon.com/codebuild/latest/userguide/getting-started.html)部分。

主要的区别是，我将使用 CLI 而不是 Web UI 来帮助学习这些命令，我还将使用 Rust 来演示如何分发构建工件。

### 创造 S3

我们将使用 S3 来存储我们的构建工件，所以我们将在与我们的 CodeBuild 项目(`eu-west-2`)相同的区域中创建一个 bucket。这是一个需要注意的重要事情，因为你不能在你的代码构建项目的不同区域使用 S3 桶。

```
aws s3api create-bucket \
  --bucket hello-codebuild \
  --region eu-west-2 \
  --create-bucket-configuration LocationConstraint=eu-west-2 
```

### 设置代码提交

我们将创建一个全新的 CodeCommit 库。

```
aws codecommit create-repository \
  --repository-name hello-codebuild
  --region eu-west-2 
```

### 拉入源代码

为了避免增加更多的步骤(这是不相关的)，我创建了一个简单的 Rust 应用程序，我们将在这个实验中使用。

我们将克隆 CodeCommit repo，下载简单的应用程序，解压缩 zip 文件，并将代码推回到 CodeCommit repo。

```
REPO_URL=$(aws codecommit get-repository --repository-name $REPO_NAME | jq -r .repositoryMetadata.cloneUrlSsh)
git clone $REPO_URL
cd hello-codebuild
curl -sLO https://github.com/booyaa/hello-codebuild/archive/master.zip
unzip master.zip
rm -rf master.zip
mv hello-codebuild-master/* .
rm -rf hello-codebuild-master
git add -A
git commit -m 'initial commit'
git push -u origin master 
```

#### 构建规范

[构建规范](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)类似于 CI 服务使用的其他配置文件，它告诉构建服务如何构建我们的代码。

```
version: 0.2

phases:
  install:
    commands:
      - apt-get update -y
      - apt-get install -y build-essential
      - curl https://sh.rustup.rs -sSf | sh -s -- -y
      - PATH=/root/.cargo/bin:$PATH
  build:
    commands:
      - cargo test
      - cargo build --release
artifacts:
  files:
    - ./target/release/hello-codebuild 
```

我们感兴趣的是以下部分或[阶段](https://docs.aws.amazon.com/codebuild/latest/userguide/view-build-details.html#view-build-details-phases):

*   我们需要运行什么命令来安装我们语言的构建工具。我们使用`apt`是因为我们的 CodeBuild 项目将使用基于 Ubuntu 14.04 的服务器映像。
*   `build` -构建代码需要什么命令
*   `artifacts` -我们想上传什么到我们的 S3 桶？

### 为 CodeBuild 创建服务角色

首先，我们将创建一个新的服务角色(如果我们通过 UI 创建项目，这是为我们完成的)，幸运的是,[用户指南](https://docs.aws.amazon.com/codebuild/latest/userguide/setting-up.html#setting-up-kms)中有关于如何通过 CLI 完成这一任务的说明。

让我们创建以下文件:

`create-role.json`

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Effect":  "Allow",  "Principal":  {  "Service":  "codebuild.amazonaws.com"  },  "Action":  "sts:AssumeRole"  }  ]  } 
```

`put-role-policy.json`

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Sid":  "CloudWatchLogsPolicy",  "Effect":  "Allow",  "Action":  [  "logs:CreateLogGroup",  "logs:CreateLogStream",  "logs:PutLogEvents"  ],  "Resource":  [  "*"  ]  },  {  "Sid":  "CodeCommitPolicy",  "Effect":  "Allow",  "Action":  [  "codecommit:GitPull"  ],  "Resource":  [  "*"  ]  },  {  "Sid":  "S3GetObjectPolicy",  "Effect":  "Allow",  "Action":  [  "s3:GetObject",  "s3:GetObjectVersion"  ],  "Resource":  [  "*"  ]  },  {  "Sid":  "S3PutObjectPolicy",  "Effect":  "Allow",  "Action":  [  "s3:PutObject"  ],  "Resource":  [  "*"  ]  }  ]  } 
```

```
aws iam create-role \
  --role-name CodeBuildServiceRole 
  --assume-role-policy-document file://create-role.json

aws iam put-role-policy --role-name CodeBuildServiceRole \
  --policy-name CodeBuildServiceRolePolicy \
  --policy-document file://put-role-policy.json 
```

### 创建 CodeBuild 项目

```
REPO_NAME=hello-codebuild
S3_BUCKET=$REPO_NAME
SERVICE_ROLE_ARN=$(aws iam get-role --role-name CodeBuildServiceRole | jq -r .Role.Arn)
REPO_URL=$(aws codecommit get-repository --repository-name $REPO_NAME | jq -r .repositoryMetadata.cloneUrlHttp)
aws codebuild create-project \
  --name $REPO_NAME \
  --source type=CODECOMMIT,location=$REPO_URL \
  --artifacts type=S3,location=$S3_BUCKET \
  --environment type=LINUX_CONTAINER,image=aws/codebuild/ubuntu-base:14.04,computeType=BUILD_GENERAL1_SMALL,imagePullCredentialsType=CODEBUILD \
  --service-role $SERVICE_ROLE_ARN 
```

需要指出的关键点:

*   `sourceType`
    *   我们选择了 CodeCommit `type`，但也有 BitBucket、CodePipeline、GitHub、S3 和最终无源代码(当没有源代码时)的选项。
    *   我们提供的另一个参数是 Git repo URL 的`location`。
    *   关于`sourceType`的更多细节可以在 [API](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_ProjectSource.html) 中找到。
*   `artifacts`
    *   我们已经选择了`type` S3，唯一的其他选择是代码管道和没有工件。
    *   我们提供的另一个参数是 S3 桶的`location`。
    *   关于`artifacts`的更多细节可以在 [API](https://docs.aws.amazon.com/codebuild/latest/APIReference/API_ProjectArtifacts.html) 中找到
*   `environment`
    *   镜像`type`是 Linux，而不是 Windows。
    *   `image`是 Ubuntu 14.04 基础版(如果你浏览 UI 的话，这看起来是唯一的选择)。
    *   `computeType`是最小的通用构建服务器
    *   `imagePullCredentialsType`是 AWS CodeBuild 自己获取图像的凭证，这很好，因为我们使用的容器注册表是 AWS 为它自己的图像提供的。
*   `service-role`是我们之前创建的 IAM 角色的 ARN。

### 运行构建

```
BUILD_ID=$(aws codebuild start-build --project-name $REPO_NAME | jq -r .build.id) 
```

### 查看构建日志

```
aws codebuild batch-get-builds --ids $BUILD_ID 
```

### 获得神器并测试它

```
aws s3 cp s3://hello-codebuild/hello-codebuild/target/release/hello-codebuild hello-codebuild
chmod +x
docker run --rm -it -v $PWD:/root ubuntu bash -c /root/hello-codebuild 
```

## API 和 CLI 特性和动词

### 特性

*   项目
*   构建
*   Webhook
*   源凭据

### 动词(CRUD)

*   创造
*   批处理-获取/获取/列出/描述
*   更新/上传
*   删除

### 离群值

*   列表-策划-环境-图像
*   导入-源-凭据
*   无效项目缓存
*   停止/开始构建

*要进入系列的下一部分，点击当前标记(黑点)旁边的灰点。*