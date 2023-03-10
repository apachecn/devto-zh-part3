# 带有 twilio 的动态黑名单

> 原文：<https://dev.to/cohan/dynamic-blocklist-with-twilio-1h91>

对于那些你希望可以在 Twilio 中动态阻止来电的时刻..我们都有，对吧？

## 期望的最终结果

我的网站上有一个列出电话号码的文件。名单上的任何号码都不能给我打电话。

## 你将需要

*   包含电话号码数组的 json 文件
*   一个 Twilio 号码，接收来自您希望阻止的号码的呼叫

首先，我们要跳到一个完全不同的网站(如果来源消失了，我会写在这里，如果是这样的话，请在[联系表单](https://cohan.io/contact)上通知我)

在 Twilio 的帮助网站上阅读这篇文章。如果锚不起作用，你要找的部分在“拒绝来自特定电话号码的呼叫”下面。

当你到达“代码”部分的时候回到这里。

…

## 我提到的那个 JSON 文件

很简单，把它上传到公开的地方，它应该是这样的(但是当然是实数)

```
[
    "+441234567891",
    "+441234567893",
    "+441234567892"
] 
```

Enter fullscreen mode Exit fullscreen mode

记住不要在最后一个数字上加逗号。JSON 不喜欢那样。

## 现在为一些代码

准备好了吗？太好了。现在在 Twilio 中，在 ***运行时- >功能*** 部分转到 ***配置侧栏上的*** ，在 ***依赖关系*** 下添加一个新的 NPM 模块`request`。不应该要求版本，但是如果你想知道的话，在我写这篇文章的时候是 2.88.0。

注意:对我来说，表单输入隐藏在保存按钮下面。有点奇怪..点按您可以看到的最后一个版本，然后按 tab 键进入该版本。

一旦你点击保存按钮，它应该弹出一个关于保存，然后部署，然后部署的消息。

回到 ***管理- >你的功能*** 。在下面插入这段代码，进行三处替换

*   `https://cohan.io/blocked-numbers.json`:替换为您阻止的 numbers 文件的 URL
*   `https://ic4.io/twilio-voicemail`:之前处理你的入站流的 URL。

就我个人而言，我用一个 Twimlet 把每个人都转到语音信箱。

```
exports.handler = function(context, event, callback) {

    var request = require('request');

    request('https://cohan.io/blocked-numbers.json', function (error, response, body) {

        let twiml = new Twilio.twiml.VoiceResponse();
        let blocked = true;

        if (error) {
            twiml.redirect("https://ic4.io/twilio-voicemail");
        }
        else {
            let blocklist = JSON.parse(body);

            if (blocklist.length > 0) {
                if (blocklist.indexOf(event.From) === -1) {
                    blocked = false;
                }
            }
            if (blocked) {
                twiml.reject();
            }
            else {
                twiml.redirect("https://ic4.io/twilio-voicemail");
            }
            callback(null, twiml);
        }

    });

}; 
```

Enter fullscreen mode Exit fullscreen mode

在此过程中，当我们收到新的呼入电话时，我们:

*   请求获取我们的黑名单
*   如果失败，我们将假设我们没有阻止列表，照常进行
*   如果成功，我们将 JSON 解析成`blocklist`
*   然后我们会对照名单检查现在打给我们的电话号码
*   如果没有列出，请照常进行
*   如果在阻止列表中，则拒绝该呼叫

出于好奇的目的；当你拒绝一个电话时，来电者会收到一条自动信息，说这个号码不在使用中，然后会立即挂断电话。

# 使其如此

要将您新创建的功能应用于您的电话号码，请查看文章[配置电话号码以接收语音呼叫](https://support.twilio.com/hc/en-us/articles/223135027-Configuring-Phone-Numbers-to-Receive-Voice-Calls)

# 遁

这很简单…对吗？如果您需要帮助，请访问论坛:)