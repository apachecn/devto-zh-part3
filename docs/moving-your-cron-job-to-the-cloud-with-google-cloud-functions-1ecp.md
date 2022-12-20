# 使用 Google Cloud 功能将 cron 作业迁移到云中

> 原文：<https://dev.to/googlecloud/moving-your-cron-job-to-the-cloud-with-google-cloud-functions-1ecp>

一个 [cron](https://en.wikipedia.org/wiki/Cron) 作业是一种以指定的时间间隔在服务器上运行程序的方式。这通常是一个小脚本，执行一些可重复的任务，比如收集指标、检查服务的健康状况、拍摄一些数据集的快照等。

cron 实用程序有一个高度可定制的格式，称为 crontab 文件，它允许我们以任何想要的时间间隔运行任何命令。您可以让脚本一分钟运行一次，也可以一年运行一次。

最常见的情况是，cron 作业接受一些输入，进行一些处理，然后生成一些输出。输入可以是网页、另一个应用程序、数据库等。输出可以是任何内容，从向数据库的表中添加一行，到将文件放入存储器，到发送消息。

# 使用 cron 作业

Cron 作业有很多用途:本质上，你想以可重复的方式发生的任何事情都可以成为 cron 作业。

我喜欢使用 cron jobs 的一个原因是关注我不经常访问的网站。比如我很想看[黑客新闻](https://news.ycombinator.com)上贴的关于 Serverless 的文章，但是我没有时间每天去查，看每篇帖子的标题，看是不是关于 Python 的。

相反，我可以编写一个 cron 作业来完成这项工作。步骤大致如下:

*   每天一次，从 API 获取热门故事；
*   迭代每个故事；
*   如果他们的标题与“无服务器”匹配:
    *   发送带有链接的电子邮件

脚本如下:

```
#!/usr/bin/python 
import requests
from utils import send_email

api_url = 'https://hacker-news.firebaseio.com/v0/'
top_stories_url = api_url + 'topstories.json'
item_url = api_url + 'item/{}.json'

def scan_hacker_news()
    # Make a request to the API
    top_stories = requests.get(top_stories_url).json()
    serverless_stories = []

    # Iterate over every story
    for story_id in top_stories:
        story = requests.get(item_url.format(story_id)).json()
        if 'serverless' in story['title'].lower():
            serverless_stories.append(story)

    # If there are any, send an email
    if serverless_stories:
        send_email(serverless_stories) 
```

Enter fullscreen mode Exit fullscreen mode

它向 API 发出一个请求，遍历每个故事，过滤掉标题中有“无服务器”的故事，然后给我发一封电子邮件(我们将这个功能留给读者练习)。

如果我想在我的 Linux 机器上将它设置为一个 cron 作业，我会将它保存到一个文件(`send_me_pythons.py`)，使它可执行(`chmod 755 send_me_pythons.py`)，并将其放入我的本地 bin ( `/usr/local/bin/send_me_pythons.py`)。

然后，我将编辑我的 crontab ( `/etc/crontab`)并添加下面一行:

```
0 0 * * * /usr/local/bin/send_me_pythons.py 
```

Enter fullscreen mode Exit fullscreen mode

这将在每天午夜运行一次脚本。我喜欢在设置之前使用[https://crontab.guru/](https://crontab.guru/)来确保我的 crontab 是正确的。这里是每个领域的图表:

```
# 0 0 * * * /usr/local/bin/send_me_pythons.py
# │ │ │ │ │ └── run this script
# │ │ │ │ └──── every year
# │ │ │ └────── every month
# │ │ └──────── every day
# │ └────────── at hour zero
# └──────────── at minute zero 
```

Enter fullscreen mode Exit fullscreen mode

一旦设置了 crontab，我的机器将获取新的 cron 作业，并按照我指定的频率运行它。

# 将我们的脚本移动到云端

这很好，但它有一个巨大的缺点:现在我必须让我的服务器保持运行，以确保电子邮件被发送，否则我会错过有价值的无服务器内容。事实上，除了每天运行一次这个小脚本之外，我应该将这个服务器用于其他事情，或者我需要保持整个服务器正常运行，就为了这个小脚本，这就更加复杂了！不理想。

相反，让我们把这个功能带到云上。首先，我们将把它变成一个[谷歌云功能](https://cloud.google.com/functions/)。我们将把现有的整个脚本包装在一个 Python 函数中，并把它放在一个名为`main.py` :
的文件中

```
import requests
from utils import send_email

api_url = 'https://hacker-news.firebaseio.com/v0/'
top_stories_url = api_url + 'topstories.json'
item_url = api_url + 'item/{}.json'

def send_pythons(request):
    # Make a request to the API
    top_stories = requests.get(top_stories_url).json()
    serverless_stories = []

    # Iterate over every story
    for story_id in top_stories:
        story = requests.get(item_url.format(story_id)).json()
        if 'serverless' in story['title'].lower():
            serverless_stories.append(story)

    # If there are any, send an email
    if serverless_stories:
        send_email(serverless_stories) 
```

Enter fullscreen mode Exit fullscreen mode

**注意:**只有每次调用函数时需要发生的行，实际上需要在`send_pythons`函数中。导入语句只需要在函数加载时执行一次，并且可以放在函数之外。

接下来，我们将定义我们的依赖关系。我们正在使用`requests`，所以我们需要把它放在我们的`requirements.txt` :

```
requests==2.20.0 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以使用 [`gcloud`](https://cloud.google.com/sdk/gcloud/) 命令行工具进行部署:

```
$ gcloud beta functions deploy test --runtime python37 --trigger-http 
```

Enter fullscreen mode Exit fullscreen mode

这将为我们提供一个端点，类似于:

```
https://us-central1-[PROJECT_ID].cloudfunctions.net/send_pythons 
```

Enter fullscreen mode Exit fullscreen mode

向该端点发出 HTTP `GET`请求将导致我们的函数运行。

# 在云端调度我们的脚本

现在我们已经把脚本作为一个函数放在了云中，我们需要做的就是调度它。我们可以使用`gcloud`命令行工具:
创建一个新的 Google Cloud Scheduler 作业

```
$ gcloud beta scheduler jobs create http send_pythons_job \
    --schedule="0 0 * * *" \
    --uri=https://us-central1-[PROJECT_ID].cloudfunctions.net/send_pythons 
```

Enter fullscreen mode Exit fullscreen mode

这为我们的作业指定了一个名称`send_pythons_job`，它对于每个项目都是唯一的。它还指定了我们上面设置的 crontab 调度，并将作业指向我们创建的 HTTP 函数。

我们可以列出我们的工作:

```
$ gcloud beta scheduler jobs list --project=$PROJECT
ID                LOCATION     SCHEDULE (TZ)        TARGET_TYPE  STATE
send_pythons_job  us-central1  0 0 * * * (Etc/UTC)  HTTP         ENABLED 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想不按计划运行我们的作业，我们可以从命令行执行:

```
$ gcloud beta scheduler jobs run send_pythons_job 
```

Enter fullscreen mode Exit fullscreen mode

# 下一步

使用云功能+云调度程序，您可以做更多事情！点击以下链接，了解如何:

*   [使用云调度程序调度更复杂的任务](https://cloud.google.com/appengine/docs/standard/python/config/cron)
*   [使用云调度程序和发布/订阅来触发云功能](https://cloud.google.com/scheduler/docs/tut-pub-sub)
*   使用 [Mailjet](https://cloud.google.com/appengine/docs/standard/python3/sending-emails-with-mailjet) 或 [SendGrid](https://cloud.google.com/appengine/docs/standard/python3/sending-emails-with-sendgrid) 从应用引擎发送电子邮件

*所有代码谷歌 w/ Apache 2 许可证*