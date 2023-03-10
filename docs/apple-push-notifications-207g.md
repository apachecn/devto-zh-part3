# 苹果推送通知

> 原文：<https://dev.to/adityavarma1234/apple-push-notifications-207g>

为了向任何使用 apns 的苹果设备发送推送通知，我不得不花大量时间查看 python 中所有可用的替代方案。然后我找了一个开源库 [pyapns2](https://github.com/Pr0Ger/PyAPNs2) 。我在探索的时候几乎拒绝了这个库，因为它在文档中没有说明它支持批处理或基于令牌的认证。
当我搜索源代码时，我惊讶地发现它对两者都有支持，但却没有文档记录。

所以我更新了文档并提出了一个[拉请求](https://github.com/Pr0Ger/PyAPNs2/pull/75)。

对于任何面临类似问题的人来说，下面是如何使用该库的示例代码。

```
from apns2.client import APNsClient
from apns2.payload import Payload

token_hex = 'b5bb9d8014a0f9b1d61e21e796d78dccdf1352f23cd32812f4850b87'
payload = Payload(alert="Hello World!", sound="default", badge=1)
topic = 'com.example.App'
client = APNsClient('key.pem', use_sandbox=False, use_alternative_port=False)
client.send_notification(token_hex, payload, topic)

# To send multiple notifications in a batch Notification = collections.namedtuple('Notification', ['token', 'payload'])
notifications = [Notification(payload=payload, token=token_hex)]
client.send_notification_batch(notifications=notifications, topic=topic)

# To use token based authentication from apsn2.credentials import TokenCredentials

auth_key_path = 'path/to/auth_key'
auth_key_id = 'app_auth_key_id'
team_id = 'app_team_id'
token_credentials = TokensCredentials(auth_key_path=auth_key_path, auth_key_id=auth_key_id, team_id=team_id)
client = APNsClient(credentials=token_credentials, use_sanbox=False)
client.send_notification_batch(notifications=notifications, topic=topic) 
```

要了解基于令牌的身份验证，您可以查看这个精彩的资源[这里](http://gobiko.com/blog/token-based-authentication-http2-example-apns/)

**基准:**

当我使用`send_notification`表示 2000 的样本令牌大小时，我用了大约`10 minutes`来发送所有的通知。
当我使用`send_notification_batch`获得相同大小的样本令牌时，发送所有通知只需要不到`5 seconds`的时间。