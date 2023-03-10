# 构建 CLI 工具来部署静态网站

> 原文：<https://dev.to/andreybleme/theros-the-cli-tool-to-deploy-static-websites-3p76>

使用 S3 静态内容托管可以说是托管静态网站最便宜、最简单的方法之一。当您不得不使用 AWS 控制台重复创建存储桶、设置静态网站托管策略、上传文件并最终将它们公开时，问题就出现了。当我们只需要上传几个特定于一个站点的文件，而不是所有的文件时，这个重复的过程变得更加烦人。

考虑到这一点，我搜索了一些能够解决这些问题的工具。我发现了一些，但是没有一个足够简单，专注于重要的任务:用静态托管策略创建 bucket，并上传文件。那时，我有了一个想法，创建一个简单的命令行界面，轻便且易于安装，来管理这类网站在 S3 的部署。

在这里，我将一步一步地介绍如何创建一个简单的工具来帮助我们只使用 Nodejs 部署静态站点。

如果您只想使用该应用程序，可以在您的终端中运行:

> npm 安装-g theros

访问[https://www.npmjs.com/package/theros](https://www.npmjs.com/package/theros)查看完整文档。

这里是 Github 上完整代码实现的[链接。让我们来看看代码...](https://github.com/andreybleme/theros)

## 命令结构

我们希望能够使用简单的命令在终端中执行我们刚刚描述的基本操作。

要创建存储桶，请执行以下操作:

> theros create -铲斗

要部署所有文件:

> theros 展开铲斗

Theros 是我们的 npm 包的名称。别担心，我们会在这篇文章的最后公布。

为了提供这些命令，我们将要使用的库是 [commander.js](https://github.com/tj/commander.js) 。

已经创建了运行`npm init`的全新 npm 项目，我们需要通过运行`npm install -s commander`来安装 commander.js。让我们看看这两个命令的基本结构(创建存储桶和部署):

```
#!/usr/bin/env node const program = require('commander')

const awsCredentials = {
  region: 'us-east-1',
  accessKeyId: '',
  secretAccessKey: ''
}

const bucketParams = {
  Bucket : ''
}

program
  .command('create')
  .option('-b, --bucket <s>', 'Bucket name', setBucket)
  .option('-k, --key <s>', 'AWS Key', setKey)
  .option('-s, --secret <s>', 'AWS Secret', setSecret)
  .action(function () {
    console.log('Creating bucket')
  })

program
  .command('deploy')
  .option('-b, --bucket <s>', 'Bucket name', setBucket)
  .option('-k, --key <s>', 'AWS Key', setKey)
  .option('-s, --secret <s>', 'AWS Secret', setSecret)
  .action(function () {
    console.log('Performing deploy')
  })

function setKey(val) {
  awsCredentials.accessKeyId = val
}

function setSecret(val) {
  awsCredentials.secretAccessKey = val
}

function setBucket(val) {
  bucketParams.Bucket = val
}

program.parse(process.argv) 
```

Enter fullscreen mode Exit fullscreen mode

先从了解第一行开始:`#!/Usr/bin/env node`。这一行告诉类 unix 系统我们的文件应该通过命令行运行。每当看到这个#！( [hashbang 或者 shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)) )，你可以假设它是一个可执行文件。因为每当用户在命令行中键入 **theros** 时，我们的 cli.js 文件就会运行，所以我们需要将这一行放在 out `cli.js`文件的开头。

`.command('create')`函数是生成“创建”命令的函数。`.option('- b, --bucket <s>', 'Bucket name', setBucket)`函数指定了一个我们可以与“create”命令一起使用的参数，该参数可以用作“-bucket”或仅用作“-B”。`.option()`函数的最后一个参数接受另一个函数，在我们的例子中，这个函数将被执行来捕获用户键入的参数值:`setBucket(val)`。

“deploy”命令遵循完全相同的结构。

用户需要使用其访问密钥和客户端密码来授权我们的应用程序创建或修改存储桶，并将文件上传到其帐户。您可以在 AWS 控制台上找到这些凭证。

在这里，我们已经能够捕获两个命令的用户输入。要测试只需在终端中运行:

> node CLI . js create–bucket my _ bucket–key my _ key–secret my _ secret

## 创建桶

现在我们需要有效地使用 AWS SDK 来对用户帐户执行操作。为此，首先我们需要安装 SDK: `npm install --save aws-sdk`。

让我们创建一个新的`s3Services.js`文件，其中包含以下操作:身份验证、创建 bucket 和上传:

```
const AWS = require('aws-sdk')

function setAwsCredentials(awsCredentials) {
  AWS.config.update(awsCredentials)
}

function createBucket(bucketParams, staticHostParams) {
  const s3 = new AWS.S3()
  s3.createBucket(bucketParams, function(err, data) {
    if (err) {
      console.log('Error creating bucket: ', err)
    } else {
      console.log('Successfully created bucket at ', data.Location)
      setPoliciesForWebSiteHosting(staticHostParams)
    }
  });
}

function setPoliciesForWebSiteHosting(staticHostParams) {
  const s3 = new AWS.S3()
  s3.putBucketWebsite(staticHostParams, function(err, data) {
    if (err) {
      console.log('Error defining policies: ', err)
    } else {
      console.log('Successfully defined static hosting policies.')
    }
  });
}

module.exports = {
  setAwsCredentials,
  createBucket
}; 
```

Enter fullscreen mode Exit fullscreen mode

`setAwsCredentials()`函数更新 AWS 对象的凭证。

`createBucket()`函数创建具有指定名称的 bucket，如果操作成功，调用`setPoliciesForWebSiteHosting()`函数，通过配置 bucket 来托管静态站点，更新现有 bucket 的策略。

我们来看一下我们的`cli.js`文件在实现了每个桶创建函数的调用之后:

```
#!/usr/bin/env node const program = require('commander')
const s3Services = require('./app/s3Services')

const awsCredentials = {
  region: 'us-east-1',
  accessKeyId: '',
  secretAccessKey: ''
}

const bucketParams = {
  Bucket : ''
}

const staticHostParams = {
  Bucket: '',
  WebsiteConfiguration: {
    ErrorDocument: {
      Key: 'error.html'
    },
    IndexDocument: {
      Suffix: 'index.html'
    },
  }
}

program
  .command('create')
  .option('-b, --bucket <s>', 'Bucket name', setBucket)
  .option('-k, --key <s>', 'AWS Key', setKey)
  .option('-s, --secret <s>', 'AWS Secret', setSecret)
  .action(function () {
    s3Services.setAwsCredentials(awsCredentials)

    staticHostParams.Bucket = bucketParams.Bucket
    s3Services.createBucket(bucketParams, staticHostParams)
  })

// hidden deploy command

function setKey(val) {
  awsCredentials.accessKeyId = val
}

function setSecret(val) {
  awsCredentials.secretAccessKey = val
}

function setBucket(val) {
  bucketParams.Bucket = val
}

program.parse(process.argv) 
```

Enter fullscreen mode Exit fullscreen mode

## 部署网站

上传我们的文件包括两个不同的步骤:首先，我们必须读取当前目录中的所有文件，然后，使用 AWS SDK 上传它。

### 与文件系统交互

我们将使用节点原生库 [FS](https://nodejs.org/api/fs.html) ，递归同步读取当前目录及其子目录中的所有文件。

我们还需要捕获每个读取文件的 [MIME 类型](https://developer.mozilla.org/en-US/docs/Web/HTTP/Basics_of_HTTP/MIME_types),这样当我们上传它时，文件元数据记录的“content type”字段将被正确填充。例如，当上传 index.html 文件时，正确的“内容类型”应该是“html”。为此，让我们使用 [node-mime 库](https://github.com/broofa/node-mime)。

要安装它，运行:`npm install --save mime`。

就像我们与 S3 的交互一样，现在让我们创建一个包含文件读取操作的新文件。我们称之为`filesystem.js` :

```
const fs = require('fs')
const path = require('path')
const mime = require('mime')

function getAllFilesFrom(currentDirPath, callback) {
  fs.readdirSync(currentDirPath).forEach(function (name) {
    const filePath = path.join(currentDirPath, name)
    const stat = fs.statSync(filePath)

    if (stat.isFile()) {
      fs.readFile(filePath, function (err, data) {
        if (err) {
          throw err
        }
        callback(filePath, data)
      })
    } else if (stat.isDirectory()) {
      getAllFilesFrom(filePath, callback)
    }
  });
}

function getMimeType(filePath) {
  return mime.getType(filePath)
}

module.exports = {
  getAllFilesFrom,
  getMimeType
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里的`getAllFilesFrom()`函数通过回调返回在参数指定的目录及其子目录中找到的所有文件。该函数验证被检查的文件是否确实是一个文件`if (stat.isFile())`，如果是，则通过回调函数返回完整的文件路径及其内容:`callback (filePath, data)`。

如果获取的文件实际上是一个目录`else if (stat.isDirectory())`，这个函数被递归调用，这样子目录中的文件也被读取并返回。

最后，`getMimeType()`函数的简单目标是返回对应于给定文件路径的 MIME 类型。

### 执行上传

既然我们可以读取一个目录中的文件并获得它们的路径和类型，我们可以在我们的`s3Services.js`中实现函数来执行上传:

```
const AWS = require('aws-sdk')
const filesystem = require('./filesystem')

function setAwsCredentials(awsCredentials) {
  // updates credentials
}

function createBucket(bucketParams, staticHostParams) {
  // creates bucket
}

function uploadObject(bucket, filePath, data) {
  const s3 = new AWS.S3()
  s3.putObject({
    Bucket: bucket,
    Key: filePath,
    Body: data,
    ACL: 'public-read',
    ContentType: filesystem.getMimeType(filePath)
  }, function(error, dataS3) {
    if (error) {
      return console.log('There was an error uploading your file: ', error.message)
    }
    console.log('Successfully uploaded file: ', filePath)
  });
}

function setPoliciesForWebSiteHosting(staticHostParams) {
  // updates bucket policies
}

module.exports = {
  setAwsCredentials,
  createBucket,
  uploadObject,
}; 
```

Enter fullscreen mode Exit fullscreen mode

`uploadObject()`函数相当简单。我们调用`s3.putObject`方法，包含 bucket 名称、文件名、body(文件的字节内容)、ACL(访问权限)，最后是 ContentType。

如果由于某种原因上传失败，我们只需向用户返回一条错误消息。

### 把所有的放在一起

现在我们有了读取文件和上传文件的代码，两者都被封装了，我们可以在我们的`cli.js`文件中进行调用:

```
#!/usr/bin/env node const program = require('commander')
const s3Services = require('./app/s3Services')
const filesystem = require('./app/filesystem')

const awsCredentials = {
  region: 'us-east-1',
  accessKeyId: '',
  secretAccessKey: ''
}

const bucketParams = {
  Bucket : ''
}

const staticHostParams = {
  Bucket: '',
  WebsiteConfiguration: {
    ErrorDocument: {
      Key: 'error.html'
    },
    IndexDocument: {
      Suffix: 'index.html'
    },
  }
}

// hidden create command

program
  .command('deploy')
  .option('-b, --bucket <s>', 'Bucket name', setBucket)
  .option('-k, --key <s>', 'AWS Key', setKey)
  .option('-s, --secret <s>', 'AWS Secret', setSecret)
  .action(function () {
    s3Services.setAwsCredentials(awsCredentials)

    filesystem.getAllFilesFrom('.', function (filePath, data) {
      s3Services.uploadObject(bucketParams.Bucket, filePath, data)
    })

});

function setKey(val) {
  awsCredentials.accessKeyId = val
}

function setSecret(val) {
  awsCredentials.secretAccessKey = val
}

function setBucket(val) {
  bucketParams.Bucket = val
}

program.parse(process.argv) 
```

Enter fullscreen mode Exit fullscreen mode

对于由`filesystem.getAllFilesFrom()`函数读取的每个文件，我们使用`s3Services.uploadObject()`函数上传它。

要测试部署命令，只需运行:

> node CLI . js deploy–bucket my _ bucket–key my _ key–secret my _ secret

## 将包发布到 NPM 仓库

既然我们已经准备好了这两个基本功能，我们想把它公之于众。为此，我们将在 NPM:[https://www.npmjs.com/package/theros](https://www.npmjs.com/package/theros)将我们的 CLI 应用程序作为节点包提供。

**1。**第一步是在 https://www.npmjs.com/[创建一个账户](https://www.npmjs.com/)。

**2。**创建了您的帐户后，我们现在需要添加在我们正在使用的机器上安装的 npm 中创建的帐户。在终端中执行以下命令时，将会要求您提供电子邮件和密码:

> npm adduser

**3。**为了让操作系统将我们的包识别为运行在终端上的应用程序，我们需要在`package.json`文件中包含以下代码:

```
"bin": {
  "theros": "cli.js"
} 
```

Enter fullscreen mode Exit fullscreen mode

运行我们应用程序的命令名可以是任何一个，这里我选择了 theros，指向`cli.js`文件。

**4。**现在，我们只需运行以下命令，将包发布到我们的帐户:

> npm 发布-访问=公开

如果您在尝试发布时遇到错误，请确保您为包选择的名称在管理器中不存在:[https://www.npmjs.com/search?q=your_package](https://www.npmjs.com/search?q=your_package)。

如果它已经存在，您需要选择另一个。

如果错误仍然存在，请查看[这里的](https://github.com/andreybleme/theros/blob/master/package.json) my complete package.json 文件，确保您没有做错任何事情。

## 奖金

我实现了一些很酷的额外功能，例如:

*   通过使用`--ignore <list_of_files>`参数使用 deploy 命令时忽略特定文件。

*   通过使用`--root <directory_path>`参数指向某个自定义目录，以便部署存储在不同位置的文件。

我们可以做一些改进，例如:

*   当创建一个新的 bucket 时，用户可能会对能够**创建一个与这个 bucket 相关联的新的 CloudFront 发行版**感兴趣。这是每个在 AWS 部署静态网站的用户都需要执行的一个非常常见的步骤，它很容易实现。[查看 Github 问题](https://github.com/andreybleme/theros/issues/4)。

*   使用一个配置文件，比如一个`theros.yaml`，包含认证密钥、一个默认的根文件夹和存储桶名称，以避免总是重复输入相同的内容。

样本文件:

```
default:
  root: 'build/'

production:
  key: 'XXX'
  secret: 'XXX'
  bucket: 'theros.io'

development:
  key: 'XXX'
  secret: 'XXX'
  bucket: 'theros-dev.io' 
```

Enter fullscreen mode Exit fullscreen mode

[检查 Github 问题](https://github.com/andreybleme/theros/issues/3)。

## 就是这样！

在创建这个简单的应用程序时，我面临的最大困难是使用文件系统(FS) API 处理文件。函数一点都不直观，这个 API 的文档也不怎么样。我知道责怪这个工具是不公平的，因为 Node 最初并不是为这种性质的应用程序设计的。

我使用的主要基准是由 Cloudflare staff 开发的名为 Stout 的应用程序[。他们选择使用 Go lang 构建一个 CLI，这在我看来非常聪明，因为 Go 语言提供了比 Javascript 更加丰富的文件操作工具。](https://github.com/cloudflare/Stout)

就我个人而言，我对 Javascript 和 Node 没有什么经验，所以如果您对一些代码改进有任何建议或对新功能有任何想法，请务必发表评论:)