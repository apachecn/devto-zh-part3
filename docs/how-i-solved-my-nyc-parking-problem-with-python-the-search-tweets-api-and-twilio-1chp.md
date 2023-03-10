# 我如何用 Python、搜索 Tweets API 和 Twilio 解决我在纽约的停车问题

> 原文：<https://dev.to/twitterdev/how-i-solved-my-nyc-parking-problem-with-python-the-search-tweets-api-and-twilio-1chp>

[![car](img/588eb853b3e5c17fcbb29e8fa58402fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xxMTDRgX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1489824904134-891ab64532f1%3Fixlib%3Drb-1.2.1%26ixid%3DeyJhcHBfaWQiOjEyMDd9%26auto%3Dformat%26fit%3Dcrop%26w%3D1489%26q%3D80)

*这个教程最初是[发布到 developer.twitter.com](https://developer.twitter.com/en/docs/tutorials/nyc-parking)的。*

由于我在纽约市有一辆车，所以我的车要遵守纽约市的街道停车条例。这意味着大多数晚上我都需要在我家附近的清晨街道清扫之前移动我的车。我已经养成了每晚睡觉前移动汽车的习惯。我有时有点太擅长这个了，我经常在不需要的时候移动我的车。由于在节假日或天气恶劣的日子里，街道另一侧的停车经常被取消，因此有一个 Twitter 句柄 [@NYCASP](https://twitter.com/NYCASP) ，它每天都发布帖子，无论何时出现紧急情况。

为了解决我的问题，我使用了 Twitter 数据和 Twilio。现在，每当我不需要移动我的车时，我都会收到一条短信。我使用 Python 的搜索 Tweets 包装器从前面提到的 Twitter 句柄和 Twilio 获取数据，这样当我不需要移动我的汽车时，我就可以收到一条短信。我创建了一个脚本，它会查看 Tweet 中是否出现暂停和明天这两个词，如果满足这些条件，它会给我发一条短信。本教程将带您了解我是如何创建这个解决方案的。

## 获取我们的需求集

我写的代码是用 Python 3.6 写的。我也推荐使用这个版本。为了使用 pip，[您需要首先安装 pip](https://pip.pypa.io/en/stable/installing/)。在本教程中，我们将使用[原子](https://atom.io/)。你需要确保你已经安装了[命令行工具](https://flight-manual.atom.io/getting-started/sections/installing-atom/)。

我们还需要在命令行中运行以下命令，以确保我们设置了正确的依赖项:

```
pip install pandas
pip install twilio
pip install searchtweets 
```

现在，让我们在命令行上为这个项目创建一个新目录，并将其更改为。

```
mkdir parking
cd parking 
```

## 设置 Jupyter 笔记本

我用 Jupyter 笔记本写代码，所以我可以用交互的方式处理数据。这对我在这个项目中很重要，所以我可以看到我正在搜索的推文。您可以使用 Python 的 Anaconda 发行版，它预装了 Jupyter，或者使用以下语法:

```
pip install jupyter 
```

一旦你安装了 Jupyter，你就可以通过在命令行中输入以下命令来运行你的笔记本:

```
jupyter notebook 
```

如果您需要随时停止使用 Jupyter 笔记本，您可以按`ctrl+c`键。

如果设置正确，您的浏览器中应该会出现一个目录列表。在右上角点击显示`new`的地方。在那里，你可以点击下拉选项中的`Python 3`来启动笔记本。一旦启动，点击显示`untitled`的地方，将笔记本的名字改为 parking。

## 设置与 Twilio 连接

您将需要创建一个脚本，以便我们可以连接到 [Twilio](https://www.twilio.com/) 。要做到这一点，你需要一个 Twilio 账户，并查看关于主题的[入门文档。创建一个脚本来连接 Twilio。让我们打开我们正在工作的目录。](https://www.twilio.com/docs/sms/tutorials/how-to-send-sms-messages-python)

让我们在命令行中输入:

```
atom . 
```

我们还需要为 Twilio 帐户设置环境变量。一旦你创建了一个 Twilio 账户，你就可以在你的控制台中找到这些:

```
export 'TWILIO_ACCOUNT_SID'='xxxxxxxxxxxxxxxxxxx'
export 'TWILIO_AUTH_TOKEN'='xxxxxxxxxxxxxxxxxxxxxxx'
export 'TWILIO_PHONE_NUMBER'='xxxxxxxxxxx'
export 'CELL_PHONE_NUMBER'='xxxxxxxxxxx' 
```

当我们添加电话号码作为环境变量时，我们需要将我们的电话号码与国家代码、区号和号码放在一个字符串中。例如，如果我在美国，电话号码是(555) 555-5555，则电话号码变量的字符串将是“1555555555”。

从这里我们可以添加一个名为`twilio_connect_demo.py`的文件:

我们将首先添加我们需要的两个导入语句:

```
import os
from twilio.rest import Client 
```

现在我们可以编写一个函数来帮助我们连接到 Twilio API。

```
def twilio_connect():
    account_sid = os.environ.get('TWILIO_ACCOUNT_SID')
    auth_token = os.environ.get('TWILIO_AUTH_TOKEN')
    client = Client(account_sid, auth_token)
    return client 
```

现在我们可以编写另一个函数来发送文本消息:

```
def send_message(client):
    return client.messages.create(from_=os.environ.get('TWILIO_PHONE_NUMBER'),
                       to=os.environ.get('CELL_PHONE_NUMBER'),
                       body="You don't have to move your car tonight. Enjoy your night!") 
```

现在我们有了发送文本消息的脚本，这在本教程的后面会有帮助。

## 进口我们需要的东西

在笔记本的第一个单元格中，我们需要导入将要使用的库。我们将使用`datetime`，它是 Python 标准库的一部分，允许我们处理日期来运行我们的搜索。我们还需要使用 pandas 来获取我们的数据，并将其转换为数据帧。为了使用搜索 Tweets API，我们将导入[搜索 tweets Python 包装器](https://github.com/twitterdev/search-tweets-python)。我们需要这个库中的`ResultStream`、`gen_rule_playload`和`load_credentials`函数。我们还将从刚刚编写的脚本中导入两个函数，以便在代码中稍后发送文本消息。

```
import datetime
import pandas as pd
from searchtweets import ResultStream, gen_rule_payload, load_credentials

from twilio_connect_demo import twilio_connect, send_message 
```

要运行这段代码，我们可以按 play 按钮或使用键盘快捷键`shift-enter`。

## 连接到 Twitter API

您将需要创建一个 Twitter 应用程序，允许您连接到 API。在此之前，您需要一个经过批准的开发者帐户。完成初始设置后，您需要创建一个[开发者账户](https://developer.twitter.com/en/docs/basics/developer-portal/overview)。你可以在这里申请开发者账号[。](https://developer.twitter.com/en/apply-for-access)

一旦您的帐户获得批准，您将需要[创建一个应用程序](https://developer.twitter.com/en/apps)。然后，为[搜索 Tweets: 30 天环境](https://developer.twitter.com/en/account/environments)设置应用程序，并设置开发环境标签名称。

完成初始设置后，您需要从 Twitter 应用程序中找到您的消费者 API 密钥和 API 密钥。[有关设置钥匙定位的更多信息，请查看我们关于主题](https://developer.twitter.com/en/docs/basics/apps/overview)的文档。

在 Atom 中，我们需要添加一个新文件，其中包含我们的应用程序密钥和秘密。让我们创建一个名为`secret.yaml`的文件，它包含以下内容:

```
search_tweets_api:
  account_type: premium
  endpoint: https://api.twitter.com/1.1/tweets/search/30day/env_name.json
  consumer_key: xxxxxxxxxxxxxxxxxxx
  consumer_secret: xxxxxxxxxxxxxxxxxxx 
```

端点中的`env_name`是您在 developer.twitter.com 上创建的开发环境的名称。您需要将其更改为您自己的开发环境的名称。

在把这个文件推送到 GitHub 之前，你需要确保你已经把它添加到了你的`.gitignore`中。关于使用 gitignore 文件[的更多信息，请查看这个页面](https://help.github.com/en/articles/ignoring-files)。你可能还想[看看我们关于保护代币安全的指南](https://developer.twitter.com/en/docs/basics/authentication/guides/securing-keys-and-tokens)。

## 处理 Twitter 数据

回到我们运行 Jupyter 笔记本的浏览器，我们可以创建一个名为 search_args 的变量，在这里我们可以加载我们的凭证以连接到 Twitter API。

```
search_args = load_credentials(filename="secret.yaml",
                              yaml_key="search_tweets_api",
                              env_overwrite=False) 
```

因为我们希望我们正在搜索的日期是动态的，这意味着我们不必输入日期，我们将需要创建一些变量。让我们首先创建一个名为`today`的程序，它从我们之前导入的`datetime`库中获取今天的日期。

```
today = datetime.date.today()
print(today) 
```

当我们运行这行代码时，我们应该得到今天的日期。为了获得从今天起 30 天后的开始日期，我们将创建一个名为 start date 的变量。这将使用 datetime 库中内置的 timedelta 方法获取今天的日期并减去 30 天。

```
start_date = today + datetime.timedelta(-30)
print(start_date) 
```

运行此命令后，我们应该会得到从今天起 30 天后的开始日期。现在我们有了可以传递到从搜索 tweets python 包装器导入的`gen_rule_payload`中的日期，这允许我们创建一个规则，允许我们从 [@NYCASP twitter 句柄](https://twitter.com/NYCASP)中提取过去 30 天的数据。

```
rule = gen_rule_payload("from:NYCASP",
                        from_date=str(start_date),
                        to_date=str(today),
                        results_per_call=500,
                       )
print(rule) 
```

运行完这行代码后，您应该会得到这样一条规则，即您也可以粘贴到 REST 客户端(如 [Postman](https://www.getpostman.com/) 或)的主体中来提前查看数据。

```
{"query": "from:NYCASP", "maxResults": 500, "toDate": "201811060000", "fromDate": "201810070000"} 
```

现在让我们创建一个名为`rs`的变量，它是 ResultStream 的缩写，因为我们要创建一个 Tweets 的结果流。为此，我们可以将刚刚创建的规则以及我们的凭证和一些其他参数传递到这个变量中，如下所示:

```
rs = ResultStream(rule_payload=rule,
                 max_results=500,
                 max_pages=1,
                 **search_args)

print(rs) 
```

一旦我们打印了名为 rs 的变量，就可以看到我们传递给它的信息。这应该是这样的:

```
ResultStream: 
    {
    "username": null,
    "endpoint": "https://api.twitter.com/1.1/tweets/search/30day/env_name.json",
    "rule_payload": {
        "query": "from:NYCASP",
        "maxResults": 500,
        "toDate": "201811060000",
        "fromDate": "201810070000"
    },
    "tweetify": true,
    "max_results": 500
} 
```

我们可以使用属性`.stream()`将结果流传递给一个名为`tweets`的变量。

```
tweets = rs.stream() 
```

从这里，我们可以将结果流转换成一个列表，这样我们就可以以一种更健壮的方式处理这些数据。这是我们将开始看到我们正在处理的推文的时间点。让我们使用一个列表理解来打印前 5 行，只是为了确保我们在正确的轨道上。

```
list_tweets = list(tweets)
[print(tweet.all_text, end='\n\n') for tweet in list_tweets[0:5]]; 
```

当我们运行这段代码时，我们会得到类似这样的结果:

```
#NYCASP rules will be suspended tomorrow, Wednesday, November 7 for Diwali. Parking meters will be in effect.

#NYCASP rules are suspended today, November 6 for Election Day. Parking meters are in effect.

#NYCASP rules will be suspended tomorrow, Tuesday, November 6 for Election Day. Parking meters will be in effect.

#NYCASP rules are in effect today, November 5.

#NYCASP rules will be in effect tomorrow, Monday, November 5. 
```

现在我们知道我们正在正确地接收数据，我们可以创建两个列表，一个用于日期，一个用于 Tweet 文本。我们将启动两个空列表，并使用一个 for 循环来遍历数据。

```
tweet_text = []
tweet_date = []

for tweet in list_tweets:
    tweet_text.append(tweet['text'])
    tweet_date.append(tweet['created_at']) 
```

现在我们可以使用 pandas 创建一个由这两列组成的数据帧。

```
df = pd.DataFrame({'tweet':tweet_text, 'date':tweet_date}) 
```

我们可以使用 head 属性来查看数据帧的前 5 行。

```
df.head() 
```

我们会看到这样的东西:
[![df image](img/8d31a2ef71c8ab9bc176f49adc297de7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mFbCbIzb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z85yxao9ry19jtui0rs9.png)

## 发送消息

现在我们已经有了正确的 Twitter 数据。如果满足适当的条件，我们可以设置发送消息。我们首先需要创建一个名为`client`的变量，它允许我们连接到 Twilio。

```
client = twilio_connect() 
```

在这里，我们可以设置逻辑，如果在最后一条推文中出现暂停和明天这两个词，就向我们发送一条短信。

```
if 'suspended' in df['tweet'].values[0]:
    if 'tomorrow' in df['tweet'].values[0]:
        send_message(client=client)
        print('text sent')
    else:
        print('suspended but not tomorrow, no text sent')
else: 
    print('not suspended, no text sent') 
```

因此，我们应该得到相应的打印信息，这取决于明天是否暂停街道停车。这允许我们从命令行进行调试。

如果“暂停”和“明天”这两个词出现在最后一条推文中，我们会收到一条类似这样的短信:
[![text](img/aae7b8818b254b185385d21728e989a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AjF8FbgM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/14bk7cf1g2xxuc9xitpp.png)

要下载这个脚本作为一个`.py`文件，点击右上角的文件并选择下载为。系统会提示您选择格式，请务必选择`Python`。

## 部署

目前的情况是，除非您将它部署到服务器上，否则这是您唯一一次收到此文本消息。如果你想在不需要移动汽车的时候收到短信，你可以设置这个脚本，让它每天在一个服务器上运行一个 cron 任务。我将它设置为每天下午 7:30 运行这个脚本。

## 下一步

完整的代码可以在[这里](https://github.com/twitterdev/parking)找到。我在伦敦办公室的同事提到，这段代码可以很容易地通过[这个句柄](https://twitter.com/Oxford_Tube)来运行，让她看到牛津地铁什么时候有延迟。在和其他人谈论这个项目时，他们提到如果这个项目告诉你什么时候需要移动你的车，而不是你什么时候不需要移动它，它可能会更有意义。您可以很容易地对代码进行一些修改来实现这一点。就目前的情况来看，这段代码可以作为其他类似想法的模板。

如果这激发了你做任何事情，请在论坛上让我们知道，或者发微博给我们。