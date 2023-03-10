# 如何用 LocalStack 在本地伪造 AWS

> 原文：<https://dev.to/goodidea/how-to-fake-aws-locally-with-localstack-27me>

如果你和我一样，你会尽可能避免登录 AWS 控制台。您是否使用 2FA 设置了您的 IAM root 用户，并在您的 S3 存储桶上正确配置了 CORS 和 ACL 设置？

# 🤷‍♂️没有。

我也喜欢让我的本地开发环境尽可能地接近它在生产中的工作方式。此外，我一直在寻找新的方法来填满我的小硬盘。我想不出比把一堆 S3 服务器放在我的电脑里更好的方法来实现以上所有目标。

本教程将介绍如何在节点应用程序中设置 [Localstack](https://github.com/localstack/localstack) 。Localstack 允许您在您的计算机上模拟许多 AWS 服务，但是在这个例子中我们只使用 S3。此外，Localstack 不是特定于 Node 的——所以即使您不在 Node 中工作，本教程的大部分内容仍然是相关的。这也包括了一点关于 Docker 的内容——如果你真的不知道你在用 Docker 做什么或者它是如何工作的，不要担心。我也不知道。

你可以看到完成代码的[演示报告](https://github.com/good-idea/localstack-demo)。

这种方法的一些好处是:

*   你可以脱机工作
*   您不需要团队中的每个人都使用的共享“开发”桶
*   您可以轻松擦拭和更换您的本地水桶
*   你不需要担心支付 AWS 的使用费
*   你不需要登录 AWS😛

## 初始设置

首先，我们需要安装一些东西。

1.  如果你还没有安装 [Docker](https://docs.docker.com/install/) 。
2.  安装 [AWS CLI](https://aws.amazon.com/cli/) 。尽管我们不会与“真正的”AWS 一起工作，但我们将使用它与本地 docker 容器进行对话。
3.  一旦安装了 AWS CLI，运行`aws configure`来创建一些凭证。即使我们在和我们的“假”本地服务对话，我们仍然需要凭证。你可以输入真实的凭证(如这里描述的)，或者虚拟的凭证。Localstack 要求这些细节存在，但实际上并不验证它们。*感谢 [@alexiswilke](https://dev.to/alexiswilke) 在评论中指出我错过了这一步！*
4.  做几个文件。为您的项目创建一个新目录，并在其中:`touch index.js docker-compose.yml .env && mkdir .localstack`
5.  将图像添加到项目目录中，并将其重命名为`test-upload.jpg`
6.  `npm init`建立一个 package.json，然后`npm install aws-sdk dotenv`

## 码头工人

(声明:我不是码头专家。如果任何人对如何改进或更好地解释这些有任何建议，请在评论中告诉我！)

### Docker 配置

你可以直接从命令行运行 Localstack，但是我喜欢使用 Docker，因为它让我觉得自己很聪明。这也很好，因为您不需要担心在您的系统上安装 Localstack。我更喜欢使用 docker-compose 来设置它。以下是配置:

`docker-compose.yml`

```
version: '3.2'
services:
  localstack:
    image: localstack/localstack:latest
    container_name: localstack_demo
    ports:
      - '4563-4599:4563-4599'
      - '8055:8080'
    environment:
      - SERVICES=s3
      - DEBUG=1
      - DATA_DIR=/tmp/localstack/data
    volumes:
      - './.localstack:/tmp/localstack'
      - '/var/run/docker.sock:/var/run/docker.sock' 
```

*(2019 . 8 . 10 编辑:LocalStack 现在有了更大范围的端口，上面的 yaml 已经更新以反映这一点。感谢@arqez 在评论里提到这个)*

把这些线分开:

#### `image: localstack/localstack:latest`

使用 Dockerhub 中最新的 [Localstack 映像](https://hub.docker.com/r/localstack/localstack/)

#### `container_name: localstack_demo`:

这为我们的容器提供了一个特定的名称，我们可以稍后在 CLI 中引用它。

#### `ports: '4563-4599:4563-4599'`和`'8055:8080'`:

当你的 docker 容器启动时，它会打开几个端口。左边**上的数字**将你的`localhost`上的端口绑定到集装箱内的端口，也就是右边**上的数字**。大多数情况下，这两个数字可以相同，即`8080:8080`。我经常在`localhost:8080`上运行一些其他的东西，所以在这里，我把默认设置改成了`8055:8080`。这意味着当我在我的应用程序中连接到`http://localhost:8055`时，它将与容器上的端口`8080`对话。

行`'4563-4584:4563-4584'`做同样的事情，但是绑定了整个范围的端口。Localstack 将这些特定的端口号用作各种 API 的端点。稍后我们会看到更多相关内容。

#### `environment`

这些是提供给容器的环境变量。Localstack 将使用这些在内部设置一些东西:

*   `SERVICES=s3`:您可以定义要模拟的 AWS 服务列表。在我们的例子中，我们只是使用了 S3，但是您可以包含额外的 API，例如`SERVICES=s3,lambda`。在 Localstack 文档中有更多关于这方面的内容。
*   🧻给我看所有的日志！
*   `DATA_DIR=/tmp/localstack/data`:这是 Localstack 在内部保存数据*的目录。更多内容请见下期:*

#### `volumes`

`'./.localstack:/tmp/localstack'`

还记得大约 2 秒前将`DATA_DIR`设置为`/tmp/localstack/data`的时候吗？就像我们在端口上使用的`localhost:container`语法一样，这允许你的容器访问你硬盘的一部分。你电脑的目录在左边，容器在右边。

这里，我们告诉容器使用我们的`.localstack`目录作为它的`/tmp/localstack`。这就像一个符号链接，或一个神奇的门户，或什么的。

在我们的例子中，这确保了一旦容器重新启动，容器创建的任何数据仍然存在。请注意，`/tmp`经常被清理，并不是一个存储的好地方。如果你想把它放在一个更安全的地方

*   `'/var/run/docker.sock:/var/run/docker.sock'`

### 启动我们的容器

现在我们已经把`docker-compose.yml`准备好了，我们可以旋转容器:`docker-compose up -d`。

为了确保它正常工作，我们可以访问 [http://localhost:8055](http://localhost:8055) 来查看 Localstack 的 web UI。现在它看起来会很空:

[![Empty Localstack UI](img/53d7b3a535d173165cce72b209456d52.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MsDB3dmR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0ym8w6yneym9nh98xo0e.png)

类似地，我们的 S3 端点 [http://localhost:4572](http://localhost:4572) 将显示一些基本的 AWS 信息:

[![Empty S3 bucket](img/1492a1d34be36a39a166ac632afdccf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WDHxMei9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mmqvbgjrcgs2bhqguxi4.png)

(如果您没有看到类似的内容，请查看 docker 容器的日志)

## 使用本地堆栈

AWS 现在就在我们的电脑里面。你可能已经有点觉得自己是世界上最富有的人了。(如果没有，也不用担心，继续看就好😛)

在开始上传文件之前，我们需要创建和配置一个 bucket。我们将使用之前安装的 AWS CLI 来完成这项工作，使用`--endpoint-url`标志与 Localstack 对话。

1.  创建一个桶:`aws --endpoint-url=http://localhost:4572 s3 mb s3://demo-bucket`
2.  将一个 [ACL](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html) 附加到 bucket，这样它就可读了:`aws --endpoint-url=http://localhost:4572 s3api put-bucket-acl --bucket demo-bucket --acl public-read`

现在，当我们访问 web UI 时，我们将看到我们的存储桶:

[![Localstack UI with S3 Bucket](img/ab8c6dce33b923d95325b20f7bc26d9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z7RsBI_g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7lrpsp1n71xhead0xll1.png)

如果您在 docker 设置中使用了`volumes`，让我们暂停一会儿，看看`./.localstack/data`中发生了什么。

[![Localstack S3 JSON](img/37f71fea93aacf8711e5d864b11f5e00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fpXcLthF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/12dwa5s12m1of06w4qcu.png)

在这里，我们可以看到 Localstack 正在这个 JSON 文件中记录所有的 API 调用。当容器重启时，它将重新应用这些调用——这就是我们如何在重启之间保存数据。一旦我们开始上传，我们将不会在此目录中看到新文件。相反，我们的上传将作为原始数据记录在这个文件*中。(如果您想与他人共享容器的状态，您可以将该文件包含在您的 repo 中——但是取决于您上传的量，它将成为一个相当大的文件)*

如果您希望以后能够“恢复”您的存储桶，您可以备份该文件。当您准备好恢复时，只需删除更新的`s3_api_calls.json`文件，用您的备份替换它，并重新启动您的容器。

### 从我们的应用上传

有很多 S3 上传教程，所以这一节不会深入。我们将创建一个简单的`upload`函数，并尝试上传几次图像。

将这些内容复制到他们的文件中:

**。env** ，我们的环境变量

```
AWS_ACCESS_KEY_ID='123'
AWS_SECRET_KEY='xyz'
AWS_BUCKET_NAME='demo-bucket' 
```

注意:你的 AWS 密匙&是什么并不重要，只要它们不是空的。

**aws.js** ，我们上传功能的模块

```
const AWS = require('aws-sdk')
require('dotenv').config()

const credentials = {
   accessKeyId: process.env.AWS_ACCESS_KEY_ID,
   secretAccessKey: process.env.AWS_SECRET_KEY,
}

const useLocal = process.env.NODE_ENV !== 'production'

const bucketName = process.env.AWS_BUCKET_NAME

const s3client = new AWS.S3({
   credentials,
   /**
    * When working locally, we'll use the Localstack endpoints. This is the one for S3.
    * A full list of endpoints for each service can be found in the Localstack docs.
    */
   endpoint: useLocal ? 'http://localhost:4572' : undefined,
   /**
     * Including this option gets localstack to more closely match the defaults for
     * live S3\. If you omit this, you will need to add the bucketName to the `Key`
     * property in the upload function below.
     *
     * see: https://github.com/localstack/localstack/issues/1180
     */
   s3ForcePathStyle: true,
})

const uploadFile = async (data, fileName) =>
   new Promise((resolve) => {
      s3client.upload(
         {
            Bucket: bucketName,
            Key: fileName,
            Body: data,
         },
         (err, response) => {
            if (err) throw err
            resolve(response)
         },
      )
   })

module.exports = uploadFile 
```

*感谢 [@mcmule](https://dev.to/mcmule) 对上面`s3ForcePathStyle`选项的提示。如果你得到了一个`ECONNREFUSED`错误，看看[他的评论](https://dev.to/mcmule/comment/f841)*

**test-upload.js** ，实现上传功能

```
const fs = require('fs')
const path = require('path')
const uploadFile = require('./aws')

const testUpload = () => {
   const filePath = path.resolve(__dirname, 'test-image.jpg')
   const fileStream = fs.createReadStream(filePath)
   const now = new Date()
   const fileName = `test-image-${now.toISOString()}.jpg`
   uploadFile(fileStream, fileName).then((response) => {
      console.log(":)")
      console.log(response)
   }).catch((err) => {
      console.log(":|")
      console.log(err)
   })
}

testUpload() 
```

`testUpload()`函数获取文件内容，根据当前时间给它一个惟一的名称，然后上传。让我们试一试:

`node test-upload.js`

[![testing the upload](img/12a0e24e60430ebe7a01e688e6a0230e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CmUTD8dT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yx94y5fj0j8rq4y67foy.png)

复制响应的`Location`属性中的 URL，并将其粘贴到浏览器中。浏览器将立即下载图像。如果你想在你的浏览器中看到它，你可以使用类似 JS Bin:

[![I love my dog](img/09ff3eac64c081d80422448c50ec049a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--soDvnAT---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v1vnvbzo7z0wlabm3o43.png)

然后，如果你再看一下`.localstack/data/s3_api_calls.json`，你会看到它充满了图像的二进制数据:

[![Image binary data](img/30a7db2c632afae707c07fc169106ff8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--towKU0mr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qcydypsebmmcb8r855jg.png)

**最后**，让我们重新启动容器，以确保我们的上传仍然工作。为此，运行`docker restart localstack_demo`。重启后，运行`docker logs -f localstack_demo`。这将向您显示容器的日志(`-f`标志将“跟随”它们)。

在初始化 Localstack 之后，它将重新应用在`s3_api_calls.json`中找到的 API 调用:

[![Localstack Logs](img/c5ac5000638ef70da04fa217434a1e3f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DDxb6DQb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ydjjg14zztr5vvqv9q6s.png)

当您重新加载浏览器时，您应该会看到图像像以前一样出现。

🎉就是这样！谢谢你留下来。这是我的第一个教程，我很想知道你的想法。如果你有任何问题或建议，请在评论中告诉我！