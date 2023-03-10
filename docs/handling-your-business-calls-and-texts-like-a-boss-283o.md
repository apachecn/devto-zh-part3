# 像一个人一样处理您的业务电话和短信...老板！

> 原文：<https://dev.to/twilio/handling-your-business-calls-and-texts-like-a-boss-283o>

我害怕改变我的电话号码。是因为我收到了垃圾邮件，还是因为有人认为每天晚上给我打电话是个好主意...休息...关于...我的...生活。我宁愿扔掉我所有的带有一次性电话号码的名片，也不愿扔掉我拥有了 15 年的真实电话号码。

为了确保我们永远不必处理我们的真实号码，今天我们将学习如何提供新的电话号码，您可以在不离开 [Twilio 控制台](https://www.twilio.com/console)的情况下将这些电话号码分发给其他人或放在您的名片上。

## 我们的工具

*   Twilio 帐户-您可以免费获得一个帐户[点击这里](https://www.twilio.com/try-twilio)
*   (可选)一个相框——因为当你完成这个后，你会想把你的照片挂起来，看起来像一个老板。

## 处理短信

我们需要做的第一件事是去[电话号码页面](https://www.twilio.com/console/phone-numbers/incoming)购买一个电话号码。

这些号码的美妙之处在于，我们可以像获得一个号码一样轻松地释放它们，这与我们永远不必“释放”我们持有多年的心爱电话号码的想法很好地联系在一起。

我选择了一个英国手机号码，然后前往设置页面。在该页面的“消息”下，选择“TwiML”并按“+”按钮。

[![](img/3c938f8ce00acd343336b2a7fedf4f19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gXOMBqP5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/mBHNjSz6ST-nJ62Y3hdTgM-uOCT1lmlaoSnsSSOBmWyVh7.width-500.png)

在“+”按钮刚刚打开的模态窗口中，输入 TwiML bin 的名称。我把我的叫做“名片”。添加以下代码来处理传入的 SMS 消息。

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Message to="YOUR_PHONE_NUMBER">
        {{From}}: {{Body}}
    </Message>
</Response> 
```

用您的实际手机号码替换值`YOUR_PHONE_NUMBER`,这样您的 Twilio 手机号码收到的任何信息都会被重定向到您这里，然后点击保存。您已经完成了设置，可以接听电话了。

## 处理短信

我们可以做和上面打电话一样的事情，然后就完事了，但是我喜欢对直接打电话给我的人好一点。

前往[运行时](https://www.twilio.com/console/runtime/overview)并创建一个新的 [Twilio 函数](https://www.twilio.com/docs/runtime/functions)。在[管理](https://www.twilio.com/console/runtime/functions/manage)页面点击“+”，选择“空白”，点击“创建”。给它起个名字——我把我的叫做“个人语音邮件”——并把路径设置为“/个人语音邮件”。

[![](img/955bd2d1818c09c6df12a84c9bd7a9aa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---xAM8ESR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/Xy1kRjVbRCMg0KlXW05vAbu9ZkNs4MVG2Ecwv62ZTAX_B6.width-500.png)

我们想用这个函数做几件事:

*   能够用我们自己的号码接听打给我们 Twilio 号码的电话
*   阻止某些号码

选择“Event”下的“Incoming Voice Calls”并添加以下代码:

```
exports.handler = function (context, event, callback) {

        /***** configuration *****/

        const phoneNumber = 'YOUR_PHONE_NUMBER';
        const timeout = event.Timeout || 12;

        const secureRecordingLinks = false;

        const voiceOpts = {
                'voice': 'alice',
                'language': 'en-US'
        };

        const reject = [
                // To block a caller, add the E164 formatted number here
        ];

        let rejectMessage = "You are calling from a restricted number. Goodbye.";

        /***** end configuration *****/

        let thisFunction = 'https://' + context.DOMAIN_NAME + '/personal-voicemail';

        function shouldReject() {
                return reject.length && event.From && reject.includes(event.From);
        }

        function rejectInbound() {
                let twiml = new Twilio.twiml.VoiceResponse();
                if (rejectMessage) {
                        twiml.say(rejectMessage, voiceOpts);
                }
                twiml.hangup();
                return twiml;
        }

        function handleInbound() {
                const dialParams = {
                        'action': thisFunction
                };

                if (event.CallerId) {
                        dialParams.callerId = event.CallerId;
                }

                if (timeout) {
                        dialParams.timeout = timeout;
                }

                const twiml = new Twilio.twiml.VoiceResponse();
                twiml.dial(dialParams, phoneNumber);

                return twiml;
        }

        function redirect() {
                const twiml = new Twilio.twiml.VoiceResponse();
                twiml.redirect(thisFunction);
                return twiml;
        }

        switch (true) {
                case event.CallStatus === 'queued':
                        callback(null, redirect());
                        break;
                case event.CallStatus === 'ringing':
                        callback(null, shouldReject() ? rejectInbound() : handleInbound());
                        break;
                default:
                        callback();
        }
}; 
```

上面的代码检查呼叫您的 Twilio 号码的号码不在黑名单中，在这种情况下，会将呼叫转移到您的真实电话号码。

确保用您的实际手机号码替换`YOUR_PHONE_NUMBER`值。如果您有任何想要添加到黑名单中的号码，请以逗号分隔，如下所示:

```
const reject = [
                "+123456789","+15555555","+44123456789"
        ]; 
```

返回到[电话号码页面](https://www.twilio.com/console/phone-numbers/incoming)。在“语音&传真”下，为“当有来电时”选择“功能”，然后选择您的新功能并点击保存。

[![](img/fff45f3e9b25ba9235d0eab94158062b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mx01qULu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/93XjFtQkKcsepYj-WLkaHTBRmW0QrGf-sVe3eSBboozvZ5.width-500.png)

当有人打电话或发短信给你的 Twilio 电话号码时，他们应该会被重定向到你的真实电话号码。然后，您可以像往常一样选择接听或直接转到语音信箱。

## Boss 模式:解锁！

既然我们的号码不会被垃圾邮件或滥用，我们可以放心地知道，如果有人得到我们的 Twilio 号码并决定滥用它，我们可以随时释放它并使用我们已经编写的代码获得另一个号码。

此外，我们可以有几个号码使用相同的代码，这样我们就可以在不同的场合放弃他们。

我很想听听你使用 Twilio 让生活变得更轻松的巧妙方法。请在 [@marcos_placona](https://twitter.com/marcos_placona?lang=en) 联系我或在下面留言。