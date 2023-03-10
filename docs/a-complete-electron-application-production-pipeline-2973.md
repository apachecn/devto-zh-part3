# 一条完整的电子应用生产流水线！

> 原文：<https://dev.to/mitchpierias/a-complete-electron-application-production-pipeline-2973>

我分享了一篇关于配置一个完整的电子应用程序生产管道的博文，如果你想阅读更多内容，你可以[访问这里的中型文章](https://medium.com/@mitchpierias/the-complete-electron-pipeline-development-to-rollout-63668dd46649)。

* * *

或者，下面是文章的副本。我已经尽我所能在这个 Markdown 编辑器中重现了样式，对于任何不一致的地方，我很抱歉

* * *

这篇文章旨在概述一个完整的电子开发，以推出项目管道。我将介绍从使用 React 创建初始电子样板文件到构建和发布 Amazon AWS S3 桶的所有内容。然后，我们将创建一个静态的 Gatsby 站点来列出我们的发行版，深入研究自动 S3 部署，并利用 CloudFront 和 Route 53。完整的项目可以在 Github 上找到[。](https://github.com/MitchPierias/Agora)

让我们通过布置样板文件来开始我们的项目，我们将通过使用`create-reacton-app`来使这变得更容易。简短的免责声明，我写的…所以如果你有问题，对不起！好的，回到工作中，全局安装`create-reactron-app`，然后用你的`PROJECT_NAME`执行。

```
npm install -g create-reactron-app
create-reactron-app PROJECT_NAME
cd PROJECT_NAME 
```

就这么简单！现在你有了一个完全成熟的电子应用程序和 React 样板文件！您可以运行`npm run start`来打开开发环境，或者运行`npm run build && npm run dist`来编译发行版二进制文件。你想让你的应用做什么，从这里看起来像什么，这取决于你。在下一节中，我们将看看发布您的发行版，以便任何人都可以使用！
出版发行

让我们看看如何使用 Amazon AWS S3 部署二进制文件供公众访问。在我们开始体验 AWS 服务之前，你需要设置一个亚马逊 AWS 账户。

## 领取证件

登录你的 **AWS 账户**并访问 **IAM** 服务，进入**用户**区域。选择**添加用户**并为将访问这个项目部署存储桶的用户指定一个名称，我以我的项目`agora-deploy`命名了 mine。勾选**编程访问**并进入**设置权限**。在这里你要搜索**‘S3’**并选择**S3-经理**权限。这将授予对 S3 实例的完全访问权限，主要是我们需要的读写权限。最后**添加标签**、**查看**和**创建用户**一旦你配置了任何额外的东西。确保复制显示的`access_key_id`和`access_secret_key`，我们稍后将需要它们来配置 AWS-CLI。新用户的权限现在应该至少包含以下内容。

[![IAM Access Permissions](img/04dbdc1ed14d59e3c390a0a39be34234.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AH6VCRSw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AjoSvez9wfGh58jHuGInEFg.png)

## AWS 命令行界面

请按照以下链接中的说明为您的操作系统安装和设置 AWS CLI[。](https://aws.amazon.com/cli/)

一旦你设置好命令行工具，用`nano ~/.aws/credentials`打开凭证文件，并在创建新的 **IAM 用户**时插入我们之前复制的密钥。我已经将我的配置命名为`agora-deploy`，如果您的系统上有多个具有独立 AWS 访问权限的项目，这将非常有用。

```
[agora-deploy]
aws_access_key_id=JFRSHDKGYCNFPZMWUTBV
aws_secret_access_key=FK84mfPSdRtZgpDqWvb04fDen0fr3DZ87hTYEE1n 
```

## S3 木桶

现在您已经设置了命令行，导航回 AWS 控制台并创建一个新的 bucket 来保存您的发行版二进制文件。创建存储桶时只需使用默认设置，然后浏览权限并进入存储桶策略。

[![Bucket policy permissions](img/38c438e60736311129706e4109d7cb9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eRO-uAZp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AE7qMVUhxPi4OH-GuoO0yKg.png)

插入以下策略，从 bucket 中授予对 GetObject 的公共访问权限。一定要插入`YOUR_BUCKET_ARN`然后是你打开的顶层目录，我在这里用`/*`来表示所有的内容。

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "YOUR_BUCKET_ARN/*"
        }
    ]
} 
```

你的设备，水桶现在准备好为你分配。您可以手动上传构建文件，或者继续下一节，我们将看到自动化发行版发布。

## 配置电子生成器

我们需要为新的 S3 实例的电子构建属性添加一些额外的配置。将以下发布设置添加到您的`package.json`。

```
"build": {
  ...
  "publish": [
    {
      "provider":"s3",
      "bucket":"YOUR_BUCKET_NAME"
    }
  ]
} 
```

我还用以下两个命令更新了我的`scripts`。

```
"scripts": {
  ...
  "dist": "electron-builder --publish never --linux --mac",
  "release": "npm run dist --publish always"
} 
```

`dist`脚本编译二进制文件而不发布，`release`脚本执行`dist`脚本，同时覆盖`--publish`标志。如果您已经配置了 AWS 命令行凭证并且拼写正确，您应该能够运行`npm run release`来让电子构建器构建并发布您的发行版。

# 内容交付

现在我们已经完成了应用程序项目的设置、构建和发布，我们可以继续创建我们的 web 界面来帮助用户轻松找到他们需要的安装程序。这一部分的源代码可以在 GitHub 库后面的[中找到。](https://github.com/MitchPierias/Agora-Static)

## 项目设置

我将构建一个静态站点来列出发行版。为此，我将使用 Gatsby 并配置自动发布到 S3，并从 CloudFront 提供服务。让我们从安装 Gatsby 的命令行界面工具开始，然后运行`gatsby new`后跟我们的应用程序名来创建我们的项目样板文件。

```
npm install -g gatsby-cli
gastby new APP_NAME 
```

我将使用`index.js`组件中的 S3 对象 URL 快速添加到二进制文件的链接。你可以在这里随心所欲地建设你的网站。

```
<Link to=”https://s3-ap-southeast-2.amazonaws.com/agora-distribution/Agora-0.2.3.dmg”>Mac</Link>
<Link to=” https://s3-ap-southeast-2.amazonaws.com/agora-distribution/Agora+0.2.3.AppImage”>Linux</Link> 
```

最后，我们可以运行`npm run build`来编译我们的静态站点，然后继续配置我们的 S3 和 CloudFront 环境来显示它。我们构建的输出将在公共文件夹中，这个文件夹的内容是我们将移动到我们的 S3 桶。

> 请注意，一些 AWS 服务可能需要长达 24 小时才能将更改传播到所有边缘位置，并且是可发现的。

在我们开始之前，你需要准备好一个域名。我们可以用为我们的实例生成的资源 URL 来做这件事，但是真的没有理由需要做这样的事情。

## 配置 HTTPS

作为对我们用户的奖励，我们将设置 SSL，这样他们可以相信我们确实是我们所说的那个人。在 AWS 控制台中，导航到**证书管理器**和**请求证书**。按照提示进行设置，输入您的域或多个域，并选择 **DNS 验证**作为验证方法。

申请证书后，您需要按照列出的说明完成颁发。Route 53 和证书管理器可能需要一个小时来同步和颁发证书。

[![Configuring S3 Access](img/cc7cff8f28bba156ab2b20092c4e1e57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aFdEMqG6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AUx2dnQLHWk2jsLlOfRYQrA.png)

## S3 主持

我们需要一个 S3 桶来存放我们所有的静态构建文件。打开 S3 并创建一个新的存储桶，使用您的域名作为存储桶的名称。接下来打开桶设置导航到属性，并配置你的静态网站托管像这样。我们告诉我们的 bucket 充当 web 服务器，当没有指定资源时返回指定的索引文档，当没有找到资源时返回错误文档。我们将把我们的文件设置为 Gatsby 构建过程输出的默认文件。我们还不需要配置我们的 bucket 策略，稍后我们会让 CloudFront 来做。

## 设置 Cloudfront

我们可以配置我们的 S3 桶来直接为我们的内容服务，但这样我们就失去了 SSL 加密和高速交付的优势。让我们设置一个 CloudFront 实例，从我们的 bucket 内容构建一个边缘缓存，并使用 SSL 交付它。在 AWS 中打开 CloudFront 服务，创建发行版，并开始使用 Web 发行版。我们还需要进行一些配置，请耐心等待。

*   原点设置

    *   原域名:你的 S3 桶 ARN
*   默认缓存行为设置

    *   查看器协议策略:将 HTTP 重定向到 HTTPS
*   分发设置

    *   SSL 证书:自定义 SSL 证书*(选择之前分配的证书)*
    *   默认根对象:`index.html`

您可以在这里设置任何您喜欢的附加配置，如自定义标题。接下来选择 Create Distribution，Amazon 将启动新的实例。这将需要一些时间，当实例启动并在所有边缘位置传播时，去喝杯咖啡吧。在下一部分，配置 Route 53 时，我们需要该服务是可发现的。

## 域路由

所以我们已经设置了 CloudFront 来缓存和服务我们来自 S3 的静态站点，现在我们需要配置 Route53 来为我们的 CloudFront 实例提供一个用户友好的域。导航到 **Route53** ，选择与您的域相关联的托管区域，或者创建一个(如果您还没有创建的话)。然后创建记录集，键入`A — IPv4 address`，选择 Alias Yes 并将别名目标设置为您的 CloudFront 实例。同样，CloudFront 实例可能需要 24 小时才能出现在其他服务中。一旦设置完成，几分钟后你应该可以访问你的域名，看到你的静态站点出现。

## 自动化 S3 部署

我们之前手动将构建文件夹的全部内容复制到我们的 S3 存储桶中。这在技术上没有任何问题…但我们都是为了自动化和让事情变得更简单！因此，让我们设置`gatsby-plugin-s3`只用一个命令就可以将我们的构建部署到 S3。

```
npm install -s gatsby-plugin-s3 
```

然后我们想在我们的`gatsby-config.js`文件中配置插件。将以下配置对象添加到插件数组:

```
plugins: [
  ...
  {
    resolve: 'gatsby-plugin-s3',
    options: {
      bucketName: 'agora.pierias.com'
    }
  }
  ...
]

Finally we’re going to add a deploy command to the package.json like so:

"scripts": {
  ...
  "deploy": "gatsby-plugin-s3 deploy"
  ...
} 
```

在运行之前，你需要调用`npm run build`来更新`.cache`中的插件，然后你可以运行`npm run deploy`命令，按照提示部署到 S3。记得用 AWS IAM 服务中的`AWS_ACCESS_KEY_ID`和`AWS_SECRET_ACCESS_KEY`设置`~/.aws/credentials`。更多关于 S3 插件的细节可以在这里找到。

### 谢谢！

现在，您应该有一个完整的生产管道设置。我们还可以向 Gatsby 站点添加一个 GraphQL 查询，以便从我们的部署 S3 桶中获取最新的构建资源 URL。如果您想访问本例中部署的实时网站，可以在 [Agora](https://agora.pierias.com) 找到。