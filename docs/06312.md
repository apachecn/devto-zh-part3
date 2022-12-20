# 具有谷歌云功能的无服务器 Python 快速入门

> 原文：<https://dev.to/googlecloud/serverless-python-quickstart-with-google-cloud-functions-19bb>

这里有一个新的运行时:Google Cloud 团队通过发布除现有 Node.js 运行时之外的 Python 3.7 运行时，将 Python 的一切都带到了 Google Cloud 函数中。

云函数非常适合编写轻量级、自包含的微应用程序，这些微应用程序可以单独扩展(并且是部署完整 webapp 成本的一小部分)。它们也可以用作“触发器”:例如，一旦上传到谷歌云存储桶，就自动将图像调整为更小的缩略图。

现在您可以用 Python 编写云函数了，您可以任意使用 Python 语言的全部功能(以及来自 PyPI 的任何包)。

# 初始设置

云函数有一个简单的目录结构:

```
.
├── main.py
└── requirements.txt 
```

Enter fullscreen mode Exit fullscreen mode

在`main.py`中，我们将定义我们的函数。在`requirements.txt`中，我们将添加我们可能需要从 PyPI 安装的任何需求(或者如果没有任何需求，就将其留空)。

# 编写我们的函数

首先，让我们在我们的`main.py`文件中写一个简单的函数，它只说 hello:

```
def hello(request):
    return "Hello World!" 
```

Enter fullscreen mode Exit fullscreen mode

# 本地运行

为了快速测试我们的功能，我们可以在部署之前在本地运行它。

您需要安装 Python 来完成这一部分。如果您还不知道，请参见[“设置 Python 开发环境”](https://cloud.google.com/python/setup)。

一旦您在本地获得了 Python 环境，我们可以将下面的代码添加到我们的`main.py`文件的末尾:

```
if __name__ == '__main__':
    from flask import Flask, request
    app = Flask(__name__)
    app.route('/')(lambda: hello(request))
    app.run() 
```

Enter fullscreen mode Exit fullscreen mode

接下来，创建一个虚拟环境，激活它，并安装 Flask:

```
$ python -m venv env
$ source env/bin/activate
(env) $ pip install flask
Collecting flask
  ...
Successfully installed Jinja2-2.10 MarkupSafe-1.0 Werkzeug-0.14.1 click-6.7
flask-1.0.2 itsdangerous-0.24 
```

Enter fullscreen mode Exit fullscreen mode

运行如下:

```
$ python main.py 
```

Enter fullscreen mode Exit fullscreen mode

...并访问 [http://localhost:5000](http://localhost:5000) 在本地查看您的端点。

# 部署我们的功能

我们的功能只有部署了才有用！在部署我们的功能之前，您需要[安装并初始化`gcloud`命令行工具](https://cloud.google.com/sdk/docs/)。

一旦安装了`gcloud` SDK，我们就可以用一行代码来部署我们的功能:

```
$ gcloud beta functions deploy hello --trigger-http --runtime python37 --project YOUR_PROJECT_NAME 
```

Enter fullscreen mode Exit fullscreen mode

部署后，该命令将为您提供一个到实时功能的链接。

# 传入变量

你可能想要传递一些数据到你的函数中。一种方法是通过 URL 参数，比如`http://foo.bar/hello?name=Dustin`。让我们在例子中选择一个名为`name`的参数，并使用它来代替“世界”:

```
def hello(request):
    name = request.args.get('name', 'World')
    return f"Hello {name}!" 
```

Enter fullscreen mode Exit fullscreen mode

然后当我们重新部署我们的函数，并访问`https://YOUR_PROJECT_NAME.cloudfunctions.net/hello?name=dev.to`，它会说:

> 你好，戴夫·托！

# 添加依赖关系

云函数的一个好处是，您可以从 PyPI 安装您通常会在其他地方使用的相同的依赖项。

让我们在 [`emoji`](https://pypi.org/project/emoji/) 包中添加一个依赖项，这样我们也可以在消息的末尾添加一个可选的表情符号。

我们将把包的名称和版本添加到我们的`requirements.txt`文件:

```
emoji==0.5.1 
```

Enter fullscreen mode Exit fullscreen mode

这将在我们重新部署该功能时自动安装。

接下来，我们想要在我们的函数中导入并使用这个库:

```
import emoji

def hello(request):
    name = request.args.get('name', 'World')
    reaction = emoji.emojize(request.args.get('reaction', '!'))
    return f"Hello {name}  {reaction}" 
```

Enter fullscreen mode Exit fullscreen mode

然后我们可以访问`https://YOUR_PROJECT_NAME.cloudfunctions.net/hello?name=dev.to&reaction=:wave:`，它会说:

> 你好，戴夫·托👋

# 下一步

你可以用云函数做更多的事情！点击以下链接，了解如何:

*   [编写响应事件](https://cloud.google.com/functions/docs/writing/background)的“后台”触发函数
*   [使用 Stackdriver 监控高级功能](https://cloud.google.com/functions/docs/monitoring/)
*   [参见附加提示&编写云函数的技巧](https://cloud.google.com/functions/docs/bestpractices/tips)

*所有代码谷歌 w/ Apache 2 许可证*