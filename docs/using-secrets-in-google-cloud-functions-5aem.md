# 在谷歌云功能中使用秘密

> 原文：<https://dev.to/googlecloud/using-secrets-in-google-cloud-functions-5aem>

Google Cloud Functions 让构建无服务器 Python 程序变得很容易。这篇文章将向你展示如何使用 [Google Secret Manager](https://cloud.google.com/secret-manager) 在你的功能中安全可靠地使用秘密。

硬编码或使用环境变量来存储应该是“秘密”的纯文本字符串，如 API 密钥、cookies 的秘密标记等。一般不推荐。您使用的任何第三方依赖项或库都可以访问这些相同的环境变量。

然而，在函数中存储秘密非常方便。你仍然可以这样做，只要你安全可靠地存储这些秘密。

我们将通过使用 [Google Secret Manager](https://cloud.google.com/secret-manager) 存储我们的秘密，并在应用层访问我们的秘密来做到这一点。这样做将会把对秘密的访问限制到只有你的团队中能够访问秘密的成员(当然，还有运行在 Google Cloud 上的功能)。

# 设置

我假设你已经创建了一个有名字的 Google Cloud 项目(我的名字是`my_cloud_project`)。如果你还没有，现在就去做吧。

# 储存你的秘密

现在您已经创建了一个项目，下一步是在其中创建一个秘密。我们可以在命令行上使用`gcloud`工具来完成这项工作！此外，我们将通过云 Shell 来实现，因此任何凭据都不必离开云。

首先，[为你的项目启动云壳](https://console.cloud.google.com/home/dashboard?cloudshell=true)。

您应该会看到类似这样的提示:

```
Welcome to Cloud Shell! Type "help" to get started.
Your Cloud Platform project in this session is set to my_cloud_project.
Use “gcloud config set project [PROJECT_ID]” to change to a different project.
di@cloudshell:~ (my_cloud_project)$ 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将启用[秘密管理器 API](https://console.developers.google.com/apis/api/secretmanager.googleapis.com/overview) (和[云函数 API](https://console.developers.google.com/apis/api/cloudfunctions.googleapis.com/overview) ，当我们在这里的时候):

```
$ gcloud services enable secretmanager.googleapis.com cloudfunctions.googleapis.com 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将使用`gcloud`命令行工具创建一个新的秘密:

```
$ echo -n "The chickens are in the hayloft." | \
    gcloud secrets create my-secret \
      --data-file=- \
      --replication-policy automatic 
```

Enter fullscreen mode Exit fullscreen mode

`--data-file=-`标志允许我们通过管道将前一个命令输出的秘密传递给`gcloud`命令，这就是`echo`的秘密(没有换行符)。

我们的秘密现在被安全地保存着。如果我们想验证这一点，我们可以在命令行上使用下面的命令来访问我们的秘密:

```
$ gcloud secrets versions access 1 --secret="my-secret"
The chickens are in the hayloft. 
```

Enter fullscreen mode Exit fullscreen mode

# 使用你的秘密

现在我们将编写我们的函数。这是我们将要部署的整个函数，它位于一个名为`main.py` :
的文件中

```
import os
from google.cloud import secretmanager

client = secretmanager.SecretManagerServiceClient()
secret_name = "my-secret"
project_id = "my-gcp-project"
request = {"name": f"projects/{project_id}/secrets/{secret_name}/versions/latest"}
response = client.access_secret_version(request)
secret_string = response.payload.data.decode("UTF-8")

def secret_hello(request):
    return secret_string 
```

Enter fullscreen mode Exit fullscreen mode

这个函数根据函数的环境确定秘密的资源名称，访问我们的秘密的最新版本，然后解码响应，最后返回原始字符串。

还要注意这个函数是如何在 Python 函数本身的之外*完成这项工作的。这将通过在云函数实例化时只访问一次秘密来加速我们的函数，而不是对每个请求都访问一次。*

我们还需要告诉我们的函数安装`google-cloud-secret-manager`库，这样我们就可以从 Python 访问这个秘密。在名为`requirements.txt`的文件中，添加一行:

```
google-cloud-secret-manager==2.0.0 
```

Enter fullscreen mode Exit fullscreen mode

此时，您的目录结构应该是这样的:

```
.
├── main.py
└── requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

# 应用权限

我们还需要给我们的函数访问这个秘密的权限:

```
$ gcloud secrets add-iam-policy-binding my-secret \
    --role roles/secretmanager.secretAccessor \
    --member serviceAccount:my-cloud-project@appspot.gserviceaccount.com 
```

Enter fullscreen mode Exit fullscreen mode

这里的`serviceAccount`是云功能的默认服务账号。

# 部署您的应用

最后一步是用`gcloud`工具:
部署我们的功能

```
$ gcloud functions deploy secret_hello \
    --runtime python38 \
    --trigger-http 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在部署后测试我们的功能了:

```
$ gcloud functions call secret_hello
executionId: 4856xc8an4fa
result: The chickens are in the hayloft. 
```

Enter fullscreen mode Exit fullscreen mode

# 下一步

你可以用云函数做更多的事情！点击以下链接，了解如何:

*   [编写响应事件](https://cloud.google.com/functions/docs/writing/background)的“后台”触发函数
*   [使用 Stackdriver 监控高级功能](https://cloud.google.com/functions/docs/monitoring/)
*   [参见附加提示&编写云函数的技巧](https://cloud.google.com/functions/docs/bestpractices/tips)

*所有代码谷歌 w/ Apache 2 许可证*