# 使用 Python 发送松弛消息

> 原文：<https://dev.to/mmphego/send-slack-messages-with-python-17pc>

*更新于 2019 年 5 月 31 日
*最初发布于 2019 年 5 月 26 日 blog.mphomphego.co.za。*

* * *

我们一整天都使用并喜欢 Slack T1 来传递团队信息。我需要在办公室和家里将 slack 与我的一些物联网设备集成，主要是因为它的 API 与 WhatsApp 和 Telegram(我从来不用)相比过于简单。

原因很简单，我想让我的设备不接收信息。例如，我在办公室的花盆里和家里的菜园里种了土壤湿度传感器，我想让我的植物在需要我浇水的时候放松一下。

**注意**，这是一个正在进行的副业项目，如果你有兴趣去[这里](http://bit.ly/2WyDjIN)，我将很快发表一篇详细的博文。

我的解决方案是用一个简单的 Python 脚本，在特定/随机时间或者当`xValue`超过某个`threshold`时，在适当的 Slack 通道中给我发送一条消息。

Slack 提供了`Incoming-Webhooks`并且他们有一份有据可查的[入网钩指南](https://api.slack.com/incoming-webhooks)。然而，指南只告诉你他们期望你做什么，而没有真正解释你实际需要做什么。

我需要做的第一件事是从 Slack 中找出它将用来发布消息的正确 URL。

*   前往[https://api.slack.com/apps?new_app](https://api.slack.com/apps?new_app)并登录，然后按照这里[的指示](https://api.slack.com/incoming-webhooks)
*   选择要向其发送消息的通道，然后添加传入的 WebHooks 集成。
*   请注意，您可以找到`Display Name`并更改将出现在频道中的内容。Slack 为您提供了您将发布消息的 URL。类似于此:`https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX`

## 过账到松弛渠道

从 Slack 给你的网址中，提取出 Slack 给你的网址上标注为`/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX`的`app_id`、`secret_id`和`token`。下面是简化的 Python 代码片段

```
import requests

class SlackBot:
    def __init__(self, app_id, secret_id, token):
        """
        Get an "incoming-webhook" URL from your slack account.
        @see https://api.slack.com/incoming-webhooks
        eg: https://hooks.slack.com/services/<app_id>/<secret_id>/<token>
        """
        self._url = "https://hooks.slack.com/services/%s/%s/%s" % (
            app_id,
            secret_id,
            token,
        )

    def slack_it(self, msg):
        """ Send a message to a predefined slack channel."""
        headers = {"content-type": "application/json"}
        data = '{"text":"%s"}' % msg
        resp = requests.post(self._url, data=data, headers=headers)
        return "Message Sent" if resp.status_code == 200 else "Failed to send message"

slack = SlackBot(app_id, secret_id, token)
slack.slack_it("Hello") 
```

Enter fullscreen mode Exit fullscreen mode

[![slack test](img/0fbf53be5a96b0923c01188ace2973cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gIKlL6cH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/mmphego/uPython-moisture-sensor/master/assets/slack.png)

## 包装完毕

现在您已经有了一个简化的 Slack message bot，它可以扩展并以各种方式使用。

如果你想进一步挖掘，有一个名为 [`slackclient`](https://pypi.org/project/slackclient/) 的“臃肿”Python 包，它是一个开发工具包，用于与 Python 3.6 及更高版本上的 Slack Web API 和实时消息(RTM) API 接口。