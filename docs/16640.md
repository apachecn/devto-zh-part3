# 如何使用 Twilio 转移到长会议号码

> 原文：<https://dev.to/seankilleen/how-to-forward-to-a-long-conference-number-using-twilio-2o4n>

我有一个客户，我们使用缩放会议，需要拨号，然后额外的数字。我们一直在利用这些会议。

客户的会议电话允许将暂停保存在地址簿中，但没有足够的空间来输入电话会议的所有号码。

我真的想有一个快速拨号按钮，我们可以用它来节省输入和记忆这些号码的时间。

没问题！我们有一个解决方案。

## 解决方案:输入 Twilio&TwiML bin

语音&短信平台 Twilio 为这类解决方案提供了强大且经济高效的支持。

在本文中，我们将一步一步地介绍如何使用 Twilio 为会议电话设置转发。

## 概述:工作原理

不是免费的，但也不贵一吨。

*   我们每年为 Twilio 电话号码支付 1 美元，(这是我们在会议电话中节省的费用)
*   我们将电话号码与执行我们期望的步骤的 TwiML (Twilio 标记语言)联系起来。
*   我们根据使用那条线路的分钟数支付很少的月费(很少，因为我们用它来站着说话)

## 如何设置会议线路转发的 TwiML bin

下面我们将一步一步地介绍这个过程。

你可以通过[https://www.twilio.com/try-twilio](https://www.twilio.com/try-twilio)使用免费账户加入 Twilio，但你可能需要注册一个付费账户，添加支付信息等。

### 购买 Twilio 号码

*   从 [Twilio 控制台](https://www.twilio.com/console)中，您可以选择`All Products & Services`，然后选择`Phone Numbers`。您也可以直接前往 https://www.twilio.com/console/phone-numbers/incoming 的。
*   点击`+`图标添加电话号码。
*   搜索具有语音功能的电话号码，并选择您喜欢的号码。
*   完成结帐过程以购买号码。

### 创建二进制文件

*   从 twilio 控制台，选择`All Products & Services`，然后选择`Runtime`，然后选择`TwiML Bins`。您也可以通过[https://www.twilio.com/console/runtime/twiml-bins](https://www.twilio.com/console/runtime/twiml-bins)直接访问 TwiML bins 页面。
*   点击`+`图标添加一个 TwiML bin。
*   给你的 TwiML bin 起一个友好的名字，例如`Conference Line Forward`,并查看最后一节来添加 TwiML。

#### 添加和检查 TwiML

沿着下面几行添加一些 TwiML。

```
<?xml version="1.0" encoding="UTF-8"?>
<Response>
    <Say voice="man">Now Forwarding to the stand-up conference line.</Say>
  <Dial>
    <Number sendDigits="wwww111111111#ww#">+1-222-222-2222</Number>
  </Dial>
</Response> 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它分解一下。你可以把它想象成 Twilio 在通话过程中将要采取的任何行动的脚本。

*   `111111111`是我们编辑的缩放会议 ID，
*   `222-222-2222`是我们编辑过的主变焦拨入号码
*   首先，我们用普通人的声音说一句话。(我选择这个短语是为了让打电话的人知道发生了什么。我选择了一个通用的男人的声音，因为有一天我在摆弄语音功能，但那里有很多选项。
*   接下来，我们拨号。
*   在我们说出短语并拨出号码后，我们发送一些数字&等等。在这种情况下，`w`代表一定的等待时间。我稍微改动了一下，以使它正确。
*   关于`#ww#`:我们拨完会议号码后需要输入`#`，等待它询问我们的参与者 ID，然后点击`#`。

你也可以参考 TwiML 文档——你可以用它做很多事情。

当您完成 TwiML bin 时，一定要保存它。

#### 将 TwiML bin 与电话号码关联

*   返回到[电话号码屏幕](https://www.twilio.com/console/phone-numbers/incoming)
*   点击您之前购买的电话号码
*   选择接受语音呼叫
*   在`a call comes in`下，选择`TwiML`并选择您之前创建的 TwiML bin。
*   保存电话号码设置。

### 试一试

拨打您的号码，听听它的行动！

## 你怎么看？

你遇到什么困难了吗？你用不同的方法解决这个问题了吗？请在评论中发表意见！