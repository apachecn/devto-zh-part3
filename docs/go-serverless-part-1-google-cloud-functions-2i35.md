# 走向无服务器:第 1 部分谷歌云功能

> 原文：<https://dev.to/niklasmerz/go-serverless-part-1-google-cloud-functions-2i35>

# 使用 Golang 运行无服务器功能

Go 是云计算最流行的语言，而无服务器对于各种工作负载也越来越流行。让我们看看如何在不同的云提供商上运行一个简单的 Go 功能。

我们将从[谷歌云功能](https://cloud.google.com/functions/docs/writing/http#writing_http_helloworld-go)和 Firebase 开始。AWS Lambda 是当今最流行的无服务器平台。一些提供商以更好/更简单的工具和不同的价格转售 AWS Lambda。我们将在这个系列中看看 [Zeit Now](https://zeit.co/now) 和可能的“裸机” [AWS Lambda](https://aws.amazon.com/blogs/compute/announcing-go-support-for-aws-lambda/) 。

为了测试不同的提供者，我们将使用我的小项目 [Gistdirect](https://github.com/NiklasMerz/gistdirect) ，这是一个简单的 HTTP 函数，它重定向到保存在 Github Gist 中的 URL。这个项目易于构建和部署。Gistdirect 函数等待用一个路径调用，以重定向到给定的 URL。

# 围棋之美(在函数中)

我喜欢使用 Go for serverless，因为基本上所有平台都期望标准的 Go 处理函数，就像这样:`func Handler(w http.ResponseWriter, r *http.Request)`。这意味着你可以为所有类型的处理程序构建可重用的函数，比如标准的`http`包或路由器，比如`mux`。我不喜欢构建只能在特定(云)环境下工作的东西。得益于一个简单的 [`main`包](https://github.com/NiklasMerz/gistdirect/blob/master/main/main.go)，Gistdirect 也可以独立运行。

# 谷歌云功能

[自 2019 年 1 月起](https://cloud.google.com/blog/products/application-development/cloud-functions-go-1-11-is-now-a-supported-language)谷歌云功能支持 Go，结合 Firebase 托管，我们可以很容易地让 Gistdirect 在自己的域上运行。

显然，你需要一个谷歌账户，并注册谷歌云(GCP)。在云控制台中，我们创建了一个新项目。创建后，我们切换到云功能菜单项，并激活 API。之后，我们可以点击一个按钮来创建一个新的函数，设置运行时为 Go，设置环境变量为 Gist URL，并粘贴或上传来自 [function.go](https://github.com/NiklasMerz/gistdirect/blob/master/function.go) 的代码。但是我们将使用更加方便频繁更新和测试的`gcloud` CLI 工具:

*   设置`gcloud` CLI: [快速启动](https://cloud.google.com/sdk/docs/quickstarts)
*   选择一个项目:`gcloud config set project NAME`
*   从 Github 获取 Gistdirect 项目:`git clone https://github.com/niklasmerz/gistdirect && cd gistdirect`
*   看一下`function.go`:函数`Handler(..)`包含重定向代码，应该作为入口点调用。
*   使用 Gistdirect 和入口函数`Handler`所需的环境变量为运行时 Go 1.11 部署函数 CLI 命令:`gcloud functions deploy gistdirect --entry-point Handler --trigger-http --runtime go111 --set-env-vars "GIST_URL=https://gist.github.com/NiklasMerz/a9b5905f742b5863197a0af0465a39f6/raw/"`

如果一切正常，我们会得到这样的结果:

```
deploying function (may take a while - up to 2 minutes)...done.                                                                             
availableMemoryMb: 256
entryPoint: Handler
environmentVariables:
  GIST_URL: https://gist.github.com/NiklasMerz/a9b5905f742b5863197a0af0465a39f6/raw/
httpsTrigger:
  url: https://us-central1-PROJECT.cloudfunctions.net/gistdirect
labels:
  deployment-tool: cli-gcloud
name: projects/serverless-blog-236916/locations/us-central1/functions/gistdirect
runtime: go111 
```

现在，我们可以在浏览器中点击`https://us-central1-PROJECT.cloudfunctions.net/gistdirect/repo`，我们将被重定向到 Gistdirect repo，因为我们设置为环境变量的测试[主机文件](https://gist.githubusercontent.com/NiklasMerz/a9b5905f742b5863197a0af0465a39f6/raw/)有这个重定向规则。

就这样，我们的无服务器功能在 GCP 上运行！

## 奖励获得更好的网址

gistdirect 的要点是从短 URL 重定向到长 URL。这就是为什么我们要把自己的域指向函数。我们可以用 Firebase 做到这一点。

*   转到 [Firebase 控制台](https://firebase.google.com/)
*   转到菜单中的“托管”
*   单击“开始”并通过 NPM 安装 Firebase CLI 工具
*   在我们继续设置本地项目之前，我们可以将自定义域或 sudomain 添加到 firebase 中。我们按照说明验证并添加域。我第一次得到正确的 DNS 记录时遇到了一些麻烦，但是它应该很简单。
*   `cd`进入 Gistdirect 项目目录
*   仅使用`firebase login`而不使用 `firebase init`的
***   我们可以用`firebase use PROJECTID`切换到我们的项目*   看一看`firebase.json`**

```
{
  "hosting": {
    "public": "public",
    "ignore": [
      "firebase.json",
      "**/.*",
      " **/node_modules/**"
    ],
    "rewrites": [
        {
            "source": "*",
            "function": "gistdirect"
        }
    ]
  }
} 
```

*   我们在这里定义，我们对 Firebase 域([https://PROJECTID.firebaseapp.com/](https://PROJECTID.firebaseapp.com/))或自定义域的所有请求都应该被重定向到我们的云功能。我们只需要设置我们的函数名，并调用`firebase deploy`来设置它。因为 Firebase 现在不支持 Go 功能，所以我们需要走一条更长的路，用 GCP 设置我们的功能。
*   你可以在这里找到更多信息[。](https://firebase.google.com/docs/functions/)

**注意**:该功能和 Firebase 项目必须在同一地区。我的项目设置为`us-central1`。但是你可以试试其他地区。

**现在我们已经准备好对我们的域使用无服务器功能****