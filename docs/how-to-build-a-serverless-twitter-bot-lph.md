# 如何构建一个无服务器的 Twitter 机器人

> 原文：<https://dev.to/lorenzotenti/how-to-build-a-serverless-twitter-bot-lph>

在这篇文章中，我将向你展示如何使用[无服务器框架](https://github.com/serverless/serverless)构建一个 Twitter 机器人。这个框架代表了“构建无服务器应用程序的简单而开放的方式”，如果你不知道我在说什么，你绝对应该去看看。

我们将建立一个机器人，它将定期转发关于无服务器计算的最相关的推文，并将跟踪推文的作者。如果你不怕剧透的话，看看这里。

对于本教程，我们在 Python 3.6 运行时使用 AWS Lambda，但是您可以很容易地切换到另一种提供者/编程语言。

我们将使用 [Twython](https://twython.readthedocs.io) 与 Twitter API 进行交互。

# 设置 Twitter 账户和应用

首先，你需要为机器人创建一个 Twitter 账户，然后去[developer.twitter.com](https://developer.twitter.com/)申请一个开发者账户来访问 Twitter API。

之后，您可以创建一个 Twitter 应用程序，并获得所需的密钥和令牌。Twitter 界面易于使用，过程非常简单。

# 安装无服务器并配置环境

安装无服务器框架最简单的方法是遵循他们的安装指南，这里[这里](https://serverless.com/framework/docs/providers/aws/guide/installation/)，然后设置 AWS 凭证，如图所示[这里](https://serverless.com/framework/docs/providers/aws/guide/credentials/)。

当框架被安装和配置后，一个新的服务可以通过
很容易地创建

```
$ serverless create \
  --template aws-python3 \
  --name serverlessbot \
  --path serverlessbot 
```

然后，`cd`进入新创建的目录，使用`virtualenv` :
创建一个 Python 虚拟环境

```
$ cd serverlessbot
$ virtualenv venv --python=python3 
```

您需要使用
激活虚拟环境

```
$ source venv/bin/activate 
```

现在您可以安装`twython`库，然后将环境的包版本保存到一个`requirements.txt`文件中。

```
$ pip install twython
$ pip freeze > requirements.txt 
```

在这一点上，我们应该准备好开始编码机器人。然而，手动配置 Lambda 来使用外部库可能会很棘手，因此我们将使用`serverless-python-requirements`插件来简化它。

首先，用
初始化`npm`

```
$ npm init 
```

接受所有的默认设置(按几次回车键)
然后用
安装插件

```
$ npm install --save serverless-python-requirements 
```

最后，在您的`serverless.yml`文件的末尾添加以下内容:

```
plugins:
  - serverless-python-requirements

custom:
  pythonRequirements:
    dockerizePip: non-linux 
```

你可以在这里找到更多关于这个插件的信息。

# 编程机器人

现在我们终于可以开始制造机器人了。

我们必须将`serverless.yml`文件中的`functions`部分修改为

```
functions:
  retweet:
    handler: handler.retweet
    events:
      - schedule: rate(2 hours) 
```

这将每 2 小时调用`handler.py`的方法`retweet`。

然后，我们可以用以下内容替换`handler.py`文件:

```
import json, config
from twython import Twython, TwythonError

def retweet(event, context):

    twitter = Twython(config.APP_KEY, config.APP_SECRET, config.OAUTH_TOKEN, config.OAUTH_TOKEN_SECRET)

    search_results = twitter.search(q='serverless', result_type='mixed', lang='en')
    message = ""

    for tweet in search_results['statuses']:
        try:
            twitter.retweet(id=tweet['id'])
            message = f"Retweeted \"{tweet['text']}\" by {tweet['user']['name']}"
            twitter.create_friendship(id=tweet['user']['id'])
            break
        except TwythonError:
            pass

    body = {
        "message": message,
        "input": event
    }

    response = {
        "statusCode": 200,
        "body": json.dumps(body)
    }

    return response 
```

代码非常简单明了。基本上，我们搜索提到`serverless`的英文推文，转发它们，并跟踪推文的作者。

`return response`仅用于记录和测试。

如您所见，Twitter 密钥和令牌是使用`config`读取的。你所需要做的就是在同一个目录下创建一个`config.py`文件，并用你从 Twitter 应用页面得到的信息填充它:

```
APP_KEY="xxxyyyzz"
APP_SECRET="xxxyyyzz"
OAUTH_TOKEN="xxxyyyzz-xxxyyyzz"
OAUTH_TOKEN_SECRET="xxxyyyzz" 
```

# 部署 bot

此时，我们将使用:
部署该函数

```
$ serverless deploy -v 
```

无服务器框架现在会处理部署 Lambda 函数所需的一切。当这个过程完成时，您将能够在 AWS 上的 Lambda 管理控制台中看到它。如果您注意到，将有一个 CloudWatch 事件配置为每 2 小时触发一次该功能。

如果不想等 2 个小时，可以直接用
调用该功能

```
$ serverless invoke -f retweet 
```

如果您想从 AWS 帐户中删除该功能，只需使用:

```
$ serverless remove 
```

就是这样。你现在有了自己的 Twitter 机器人，能够转发和关注用户。阅读 Twython 文档，您可以了解如何添加更多功能。

# 结束语

本文中使用的完整代码可以在 GitHub 上获得:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[lorenzotenti](https://github.com/lorenzotenti)/[server less bot](https://github.com/lorenzotenti/serverlessbot)

<article class="markdown-body entry-content container-lg" itemprop="text">

# 无服务器机器人

这是一个 Twitter 机器人，每隔一段时间就会转发关于无服务器计算的最相关的推文。

在这里找到它[。](https://twitter.com/_serverlessbot_)

关于如何构建这个机器人的教程可以在这里找到。

* * *

它需要一个带有 Twitter API 键的 config.py 文件才能工作:

```
APP_KEY="YOUR_APP_KEY"
APP_SECRET="YOUR_APP_SECRET"
OAUTH_TOKEN="YOUR_OAUTH_TOKEN"
OAUTH_TOKEN_SECRET="YOUR_OAUTH_TOKEN_SECRET" 
```

</article>

[View on GitHub](https://github.com/lorenzotenti/serverlessbot)

本文构建的 *_serverlessbot_* 可以在[这里](https://twitter.com/_serverlessbot_)找到。

我希望你喜欢这个教程。如果没有，请在评论中告诉我原因。