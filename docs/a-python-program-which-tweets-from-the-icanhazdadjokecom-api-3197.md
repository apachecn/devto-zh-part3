# 一个在推特上发布从 icanhazdadjoke.com 获取的数据的 Python 程序

> 原文：<https://dev.to/highcenburg/a-python-program-which-tweets-from-the-icanhazdadjokecom-api-3197>

*封面照片由[https://icons8.com/](https://icons8.com/)*
[https://w.soundcloud.com/player/?url=https://soundcloud.com/mrmindful/mindful-vibes-episode-10-jazz-hop-mix&auto_play=false&color=%23000000&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true](https://w.soundcloud.com/player/?url=https://soundcloud.com/mrmindful/mindful-vibes-episode-10-jazz-hop-mix&auto_play=false&color=%23000000&hide_related=false&show_comments=true&show_user=true&show_reposts=false&show_teaser=true)

这个程序的目的是让我通过 twitter 在我的 facebook 账户上发布的笑话自动化。虽然我还没有弄清楚 AWS Lambda，但我相信我会很快到达那里。

这个程序是通过 IDLE 运行的，根据[维基百科](https://g.co/kgs/iQ9hDM)的说法，这个程序从 1.5.2b1 开始就和 Mac OS X Python 捆绑在一起了

我们从“[Twitter API](https://projects.raspberrypi.org/en/projects/getting-started-with-the-twitter-api)入门”开始学习，然后在整篇文章中我们继续调用[icanhazdadjockole API](https://icanhazdadjoke.com)。

在这个项目中，我学到了 4 件事:

*   如何应用和创建该应用程序中使用的 Twitter API 键

*   如何使用 Twython 通过 Python 发送推文

*   如何调用 API &

*   如何在搜索引擎上搜索正确的问题以获得正确的答案

#### 首先，我们借助[这个](https://projects.raspberrypi.org/en/projects/getting-started-with-the-twitter-api/3)申请一个 Twitter 开发者账号。完成申请后，我们将收到 Twitter 的电子邮件确认，以确认我们的电子邮件地址。然后我们等待我们的申请被批准。我们可以在 developer.twitter.com 的[查看状态。](https://developer.twitter.com)

#### 获得开发者账户批准后，我们登录到 developer.twitter.com注册我们的应用程序以获得我们的 API 密钥。

[![](img/d569692f8253dacaa563d7dec1c3b378.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V11jpRgJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/50a39k8s2a11qz2l82zt.png)

[![](img/8297b0513a1eb7fcce3712c7cdd18481.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0dyBVaiZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8utkkl7igx2i16j7oo4v.png)

[![](img/3d4fc114b5fb594916b117a82dbed8c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ltXmh91---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fjiyzahgleta6a9xifu2.png)

[![](img/e42a2d891f5e9d3b0b3ce17af7ef29c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rU9xgEKd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3wtexpx58ro4s0np4o7p.png)

[![](img/262959e39120600f4d8e5503e4ad5877.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N1ZzjNvI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gywdbkispkhw02925slu.png)

##### 查看开发者条款，点击创建。

[![](img/26beb1719759ed03e4da619c1f8bfbec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kUKqIeO0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4kltiwvxhgfhqbng4u2m.png)

#### 点击密钥和令牌查看您的密钥和访问令牌，然后点击访问令牌&下的创建访问令牌密码。

[![](img/1ed6d1c75b3fcb2352ea3bb71b0cb916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H8Ija0RF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/z0inajk61abjlnm6g22x.png)

[![](img/953fc2c92201a2bbb988979587350693.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x7UXDqHb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q8f5xd17798lnuk7qwgn.png)

### **编写程序并发送我们的第一条推文**

#### 在空闲时创建一个 auth.py 文件，将你的密钥粘贴到里面，然后点击保存

```
consumer_key = 'ENTER_YOUR_CONSUMER_KEY'
consumer_secret = 'ENTER_YOUR_CONSUMER_SECRET'
access_token = 'ENTER_YOUR_ACCESS_TOKEN'
access_token_secret = 'ENTER_YOUR_ACCESS_TOKEN'
```

#### 创建另一个文件，从 twython 和 requests 模块导入 Twython

```
from twython import Twython
import requests
```

#### 也从 auth.py 导入变量

```
from auth import (
    consumer_key,
    consumer_secret,
    access_token,
    access_token_secret
)
```

将此另存为 twitter.py

#### 使用 Twython 创建与 Twitter API 的连接

```
twitter = Twython(
    consumer_key,
    consumer_secret,
    access_token,
    access_token_secret
)
```

#### 我们将使用 Twitter API 的

```
update_status()
```

函数来发送一条推文

```
message = "Hello world!"
twitter.update_status(status=message)
print('Tweeted: ' % message)
```

#### 现在我们通过使用 F5 或者从 tweet.py - >运行- >运行模块来运行模块。

从手机上查看:
[![](img/038dc844175a5fbf53b677be410de13a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sBq1DDeH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59xvt797h0kc2jmi5u0m.jpg)

从 CLI 查看:
[![](img/547f543e228eaee6c2c8aca05723107d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MtXqgAB5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3sywi3aacy03uejeuknx.png)

从浏览器查看:
[![](img/afb8d50b7f9dbdfeaa186daa2c6e23c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XltKZAex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zlk04q3jla5z79y0ph57.png)

### **调用 icanhazdaddjoke API**

幸运的是， **GET** 方法可以在没有认证的情况下调用*icanhazdaddjoke*。如果你想了解更多，请阅读他们的文档[这里](https://icanhazdadjoke.com/api)

```
url = 'https://icanhazdadjoke.com/'
headers = {'Accept': 'application/json'}
joke_msg = requests.get(url, headers=headers).json().get('joke')
print(joke_msg)
```

回应是:🙌🙌🙌
[![](img/cf640c5f3d82b3f02adcf4df1ff9fe0b.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NUAqeKkX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oxa9xnkvlpdostt15pw7.png)

### 现在，我们来玩转代码。

#### 设计代码结构

我们希望在我们的 twitter 账户上发布*icanhazdaddjoke*的输出，所以我们使用 **url** 变量来调用他们的 API。我从输出开始，回到调用 API。

**我们以这段代码结束:**

```
url = 'https://icanhazdadjoke.com/'
headers = {'Accept': 'application/json'}
joke_msg = requests.get(url, headers=headers).json().get('joke')
twitter.update_status(status=joke_msg)
print("Tweeted: " + joke_msg)
```

输出是:😱😱😱

CLI:
[![](img/2041c3a68ef830c6fb85564ed58bf3b1.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--rCih7Tqj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yj1bp6f5yzx0oms8zghz.png)

浏览器:
[![](img/7558ba7a7af5ad16fda7372eb04c82db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aCdsUbnd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qnb0cxt3ghrq25zhxxnw.png)

移动:
[![](img/75ba0762517ce43ee0dfcd0cdb673558.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mvf45fnL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9abw67tyoquum7e2saw2.jpg)

🙌😱🙌😱🙌

**下一步将是开发 AWS Lambda，让每天两次的推文自动化，然后忘掉它。**