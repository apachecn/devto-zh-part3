# Pulumi 与 Terraform 在基础设施代码方面的比较

> 原文：<https://dev.to/kylegalbraith/how-pulumi-compares-to-terraform-for-infrastructure-as-code-434j>

我最近的很多作品都是 Terraform 的忠实粉丝。我非常喜欢它作为代码给基础设施带来的模块化。如果你还没有看过我之前在 Terraform 上的帖子，可以读一读。

*   [如何使用 AWS CodeBuild 在 CI/CD 管道中通过 Cypress 运行浏览器测试](https://blog.kylegalbraith.com/2018/08/14/how-to-run-browser-tests-via-cypress-in-your-cicd-pipeline-with-aws-codebuild/)
*   [使用 Terraform 和 AWS 在大约 30 秒内创建带有 git 存储库的 CI/CD 管道](https://blog.kylegalbraith.com/2018/05/14/creating-a-cicd-pipeline-with-a-git-repository-in-~30-seconds-using-terraform-and-aws/)
*   [如何使用 GitHub 和 AWS 持续部署静态网站](https://medium.freecodecamp.org/how-to-continuously-deploy-a-static-website-in-style-using-github-and-aws-3df7ecb58d9c)

虽然 Terraform 是一个很好的工具，但花一点时间探索其他选择总是值得的。在大多数情况下，被一种工具、语言或框架束缚或卡住可能会导致灾难。有必要知道还有哪些其他选择，以及使用它们的优缺点。几个月前，在代码空间的基础设施中引起我注意的一个是 [Pulumi](https://pulumi.io/) 。

关于 Pulumi 有趣的是，它消除了进入基础设施即代码的最大障碍之一，这是另一种需要学习的语言。与 Terraform 不同，这里不需要学习另一种特定领域语言(DSL)。如果你懂 JavaScript、TypeScript、Python 或 Go，那么你就可以开箱即用地开始编译你的基础设施。

在这篇文章中，我将简单介绍一下 Pulumi 以及如何使用它。然后，我们将使用 Pulumi 在 AWS 中建立一个静态网站 bucket 和 CloudFront 发行版，这是我在其他 Terraform 示例中使用过的一个用例。之后可以看看两者之间的一些优缺点。

### 普鲁米入门

正如大多数与技术相关的事情一样，我们必须首先安装 Pulumi。如果您运行的是 macOS，我们可以直接从`brew`获取。

```
$ brew install pulumi 
```

Enter fullscreen mode Exit fullscreen mode

对版本进行快速的完整性检查应该会显示我们已经安装了所有的东西。

```
$ pulumi version 
```

Enter fullscreen mode Exit fullscreen mode

有打印出来的版本吗？太好了，让我们进入下一步。

像 Terraform 一样，Pulumi 支持多个云提供商，包括 AWS、Azure、Google Cloud、Open Stack、Kubernetes，甚至他们自己的云框架。

在这篇文章中，我们将使用 Pulumi 和 AWS。如果您还没有安装和配置 [AWS CLI](https://aws.amazon.com/cli/) ，您应该现在就安装和配置。默认情况下，Pulumi 将使用您通过`aws configure`配置 AWS CLI 时创建的凭证。

### 通过亚马逊网络服务使用 Pulumi

首先，我们将使用下面的命令创建一个准系统 Pulumi 项目。

```
$ pulumi new aws-javascript --dir pulumi-static 
```

Enter fullscreen mode Exit fullscreen mode

这将首先提示您登录到 Pulumi，然后点击 enter 键在浏览器中打开登录。登录后，您可以返回到终端并完成设置。

1.  将项目名称保留为默认名称。
2.  将项目描述保留为默认值。
3.  同样创建`-dev`堆栈。
4.  出现创建选项提示时，选择`no`。

现在我们应该能够查看我们的`pulumi-static`文件夹的内容，看到我们有两个 Pulumi YAML 文件，一个`index.js`，当然还有我们的包文件。

如果我们打开 [pulumi.io](https://app.pulumi.com/) 并登录，我们还会看到我们有一个项目，`pulumi-static`里面有两个栈。我们也可以在命令行上通过列出堆栈来验证这一点。

```
$ pulumi stack ls 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们深入到我们的`index.js`文件中，并提供一些 AWS 基础设施。当我们第一次打开我们的文件时，我们看到它已经包含了一些代码。让我们暂停一下，谈谈这段代码目前在做什么。

```
"use strict";
const pulumi = require("@pulumi/pulumi");
const aws = require("@pulumi/aws");

const bucket = new aws.s3.Bucket("my-bucket");

exports.bucketName = bucket.bucketDomainName; 
```

Enter fullscreen mode Exit fullscreen mode

这是代表一些 AWS 基础设施的代码。更具体地说，我们看到它创建了一个名为`my-bucket`的新 S3 存储桶，然后该存储桶的名称被导出。注意，这只是传统的 JavaScript，不需要学习另一种特定于领域的语言。

我们可以从命令行运行这段代码来配置我们的 AWS 基础设施。首先，我们运行`npm install`，然后我们可以运行我们的`up`命令。

```
$ npm install
$ pulumi up 
```

Enter fullscreen mode Exit fullscreen mode

选择`pulumi-static-dev`作为我们想要配置的堆栈。

一旦我们选择了我们的堆栈，我们就会看到一个关于将创建、更新或销毁哪些资源的计划。很像`terraform plan`命令。

```
Previewing update (pulumi-static-dev):

     Type                 Name                             Plan       
 +   pulumi:pulumi:Stack  pulumi-static-pulumi-static-dev  create     
 +   └─ aws:s3:Bucket     my-bucket                        create     

Resources:
    + 2 to create 
```

Enter fullscreen mode Exit fullscreen mode

在确认提示上选择`yes`创建铲斗。这将根据我们刚才看到的代码创建我们的 S3 桶。一旦我们的栈被创建，我们应该看到来自 Pulumi 的如下日志。

```
Updating (pulumi-static-dev):

     Type                 Name                             Status      
 +   pulumi:pulumi:Stack  pulumi-static-pulumi-static-dev  created     
 +   └─ aws:s3:Bucket     my-bucket                        created     

Outputs:
    bucketName: "my-bucket-ed42950.s3.amazonaws.com"

Resources:
    + 2 created 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们使用 Pulumi 提供了我们的第一个 AWS 基础设施。让我们修改我们的基础设施，不仅创建一个 S3 桶，而且使静态网站托管在桶上。

### 使用 Pulumi 供应我们的静态网站基础设施

为了使我们的桶 S3 网站托管，我们需要添加一个额外的属性，以及允许公共读取的桶策略。这在 Pulumi 中也很容易实现。这是我们的代码添加到我们的桶网站托管后的样子。

```
"use strict";
const pulumi = require("@pulumi/pulumi");
const aws = require("@pulumi/aws");

const websiteBucket = new aws.s3.Bucket("my-bucket", {
  website: {
    indexDocument: "index.html"
  }
});

let bucketPolicy = new aws.s3.BucketPolicy("publicReadPolicy", {
  bucket: websiteBucket.bucket,
  policy: websiteBucket.bucket.apply(publicReadPolicyForBucket)
});

exports.websiteUrl = siteBucket.websiteEndpoint;

function publicReadPolicyForBucket(bucketName) {
  return JSON.stringify({
    Version: "2012-10-17",
    Statement: [
      {
        Effect: "Allow",
        Principal: "*",
        Action: ["s3:GetObject"],
        Resource: [
          `arn:aws:s3:::${bucketName}/*` // policy refers to bucket name explicitly
        ]
      }
    ]
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将一个`website` config 属性添加到我们正在创建的 bucket 中。然后，我们定义一个附加到我们的网站桶的`BucketPolicy`。该政策允许任何人在我们的桶上执行`s3:GetObject`，这对于 S3 桶外的静态网站托管是必要的。

我们可以通过在命令行上运行另一个`up`命令来将这些更改应用到堆栈中。

```
$ pulumi up 
```

Enter fullscreen mode Exit fullscreen mode

我们应该看到我们的存储桶策略将被创建，并且我们当前的存储桶将被更新。

```
Previewing update (pulumi-static-dev):

     Type                    Name                             Plan       Info
     pulumi:pulumi:Stack     pulumi-static-pulumi-static-dev             
 ~   ├─ aws:s3:Bucket        my-bucket                        update     [diff: +website]
 +   └─ aws:s3:BucketPolicy  publicReadPolicy                 create     

Resources:
    + 1 to create
    ~ 1 to update
    2 changes. 1 unchanged 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们执行更新，我们应该看到我们的 S3 静态网站托管 url 输出。

```
Outputs:
  - bucketName: "my-bucket-e4e2278.s3.amazonaws.com"
  + websiteUrl: "my-bucket-e4e2278.s3-website-us-west-2.amazonaws.com"

Resources:
    + 1 created
    ~ 1 updated
    2 changes. 1 unchanged 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们现在有了一个 S3 存储桶，它被配置为托管一个静态网站，并且都是通过 Pulumi 使用 JavaScript 提供的。

让我们再扩展一次，在我们的 S3 网站 bucket 前面添加一个 CloudFront 发行版。这要求我们创建一个新的发行版，并将我们的 S3 bucket 设置为 origin。下面是在我们的基础设施中添加了 CloudFront CDN 之后，我们的代码看起来是什么样子。

```
"use strict";
const pulumi = require("@pulumi/pulumi");
const aws = require("@pulumi/aws");

const websiteBucket = new aws.s3.Bucket("my-bucket", {
  website: {
    indexDocument: "index.html"
  }
});

let bucketPolicy = new aws.s3.BucketPolicy("publicReadPolicy", {
  bucket: websiteBucket.bucket,
  policy: websiteBucket.bucket.apply(publicReadPolicyForBucket)
});

const cloudFrontDistribution = new aws.cloudfront.Distribution("myBucketDistribution", {
    enabled: true,
    defaultRootObject: "index.html",
    origins: [
        {
            customOriginConfig: {
                httpPort: 80,
                httpsPort: 443,
                originProtocolPolicy: "match-viewer",
                originSslProtocols: ["TLSv1", "SSLv3"]
            },
            originId: websiteBucket.websiteEndpoint,
            domainName: websiteBucket.websiteEndpoint
        }
    ],
    defaultCacheBehavior: {
        viewerProtocolPolicy: "redirect-to-https",
        allowedMethods: ["GET", "HEAD", "OPTIONS"],
        cachedMethods: ["GET", "HEAD", "OPTIONS"],
        forwardedValues: {
            cookies: { forward: "none" },
            queryString: false,
        },
        targetOriginId: websiteBucket.websiteEndpoint
    },
    restrictions: {
        geoRestriction: {
            locations: [],
            restrictionType: "none"
        }
    },
    viewerCertificate: {
        cloudfrontDefaultCertificate: true
    }
});

exports.websiteUrl = cloudFrontDistribution.domainName;

function publicReadPolicyForBucket(bucketName) {
  return JSON.stringify({
    Version: "2012-10-17",
    Statement: [
      {
        Effect: "Allow",
        Principal: "*",
        Action: ["s3:GetObject"],
        Resource: [
          `arn:aws:s3:::${bucketName}/*` // policy refers to bucket name explicitly
        ]
      }
    ]
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里添加的关键是变量`cloudFrontDistribution`。它定义了我们 CDN 分发的所有部分。这包括默认缓存行为，我们 S3 网站桶的来源，以及我们最终用户的默认限制和 SSL 证书。

再运行一次`pulumi up`应该可以供应我们新的 CloudFront 发行版。

```
$ pulumi up
Previewing update (pulumi-static-dev):

     Type                            Name                             Plan       
     pulumi:pulumi:Stack             pulumi-static-pulumi-static-dev             
 +   └─ aws:cloudfront:Distribution  myBucketDistribution             create     

Resources:
    + 1 to create
    3 unchanged 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们确认了更新，我们应该会看到我们的 CloudFront 域名输出给我们。

```
Updating (pulumi-static-dev):

     Type                            Name                             Status      
     pulumi:pulumi:Stack             pulumi-static-pulumi-static-dev              
 +   └─ aws:cloudfront:Distribution  myBucketDistribution             created     

Outputs:
  ~ websiteUrl: "my-bucket-e4e2278.s3-website-us-west-2.amazonaws.com" => "d2stkileejh34y.cloudfront.net"

Resources:
    + 1 created
    3 unchanged 
```

Enter fullscreen mode Exit fullscreen mode

就这样，我们在 AWS 中提供了支持静态网站的基础设施。我们有一个用于托管的 S3 存储桶和一个用于内容交付网络的 CloudFront 分发。所有这些都是通过 Pulumi 运行的 JavaScript 来配置我们的基础设施的。

很酷吧？我们能够编写我们知道并且喜欢的代码来提供我们在 Amazon Web Services 中需要的基础设施。我们可以继续扩展这段代码，为我们的发行版添加 DNS 别名，甚至上传我们的静态网站文件。

最后，让我们回顾一下与 Terraform 相比，Pulumi 的一些相似性、差异、优点和缺点。

### 反映在普鲁米身上

值得注意的是，Pulumi 是一个不同于 Terraform 的工具，坦率地说，总是有更多工具的空间。作为代码空间进入基础设施的每个工具都提供了不同的想法和观点。我们可能同意其中一些，不同意另一些，这是完全正常的。使用最适合您工作流程的方法。

也就是说，下面是我在使用 Pulumi 时的一些想法。

*   用你日常使用的编程语言来编写你的基础设施真是太棒了！避免与另一个 DSL 相关的学习曲线是一个巨大的增值。此外，通过用像 JavaScript 这样的代码来表示我们的基础设施，我们可以很容易地使用我们已经知道并喜欢的框架来添加测试。测试部分是困扰许多使用 Terraform 的开发人员的问题，也有解决方案，但不如测试 JavaScript 代码那样熟悉。
*   在调配基础设施时，命令是最棒的。很高兴看到 Pulumi 和 Terraform 有共同的计划。当您通过代码调配基础架构时，看到将要创建、更新和销毁的内容的可视化非常有帮助。
*   登录使用命令行工具很奇怪。有了 Terraform，我们可以安装 CLI 并立即使用它。Pulumi 的情况并非如此，我认为这具有破坏性。
*   州州州。Terraform 和 Pulumi 通过某种状态文件跟踪您的基础设施。这是他们知道添加、更新和删除什么的方式。两者的区别在于，Pulumi 默认情况下将您的状态存储在 web 后端(也就是上面提到的登录步骤)。另一方面，默认情况下，Terraform 会将您的状态存储在本地。
*   记录必填字段。试图使用 Pulumi 提供 CloudFront 发行版真的让我抓狂。实际上，我不得不打开他们的源代码，看看他们在创建新的发行版时期望什么值。在我看来，Terraform 已经明确了这一类别，[所有的东西都在一个地方记录，并准确地告诉我所需的属性是什么](https://www.terraform.io/docs/providers/aws/r/cloudfront_distribution.html#viewer-certificate-arguments)。
*   不要把我的巧克力和你的云彩混在一起。像 Terraform 和 Pulumi 这样的工具的目的是让我可以使用比 CloudFormation 稍微友好一些的东西。我并不反对 CF，但有时这是一种精神上的飞跃，我宁愿不做。对于 Pulumi，缺少文档意味着我需要查找 CloudFormation 值用于提供像 CloudFront 发行版这样的东西。这是特定于 AWS 内部的基础设施，因此您的收益可能会有所不同。

这是我使用 Pulumi 几个星期以来的想法。我会选择将它用于 Terraform 之上的新项目吗？不太可能，因为我已经在 Terraform 中增加了 HCL，所以我没有一些人可能需要爬的学习曲线。我还发现围绕 Terraform 的文档更好，这大大加快了我的开发环境中的事情。

Pulumi 是一个非常强大的工具，它提供了 Terraform 所提供的许多好处，包括用你已经知道并喜欢的语言编写代码。但是，它仍然有一些粗糙的地方，可以使用一些波兰。

亲自尝试一下，用它来构建一些东西，看看是否利大于弊。知道一个以上的工具不会伤害任何人！一如既往，欢迎提问或分享您的经验。

### 您是否渴望了解更多关于亚马逊网络服务的信息？

如果你想开始你的 AWS 之旅，但却不知道从哪里开始，可以考虑查看我的课程。我们专注于在 AWS 上托管、保护和部署静态网站。让我们在使用时能够了解超过 6 种不同的 AWS 服务。在你掌握了基础知识之后，我们可以进入**的两个额外章节**来讨论更高级的主题，比如基础设施代码和持续部署。