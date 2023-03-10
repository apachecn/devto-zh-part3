# 漂亮的块状松弛消息

> 原文：<https://dev.to/nathanheffley/beautiful-blocky-slack-messages-38aa>

最近 Slack 发布了一种新的消息格式，应用程序可以利用这种格式，称为 [Block Kit](https://api.slack.com/block-kit) 。这种新格式允许应用程序和其他软件通过 API 调用或 webhooks 发送非常丰富的消息。

<figure>

[![A Slack message styled to look like a search result displaying three hotels](img/81fc3572408cf0c9ffa6694e4135554f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Dkx-bAg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q0clerhsgqztc5feqj4c.png)

<figcaption>An example of hotel search results as a message, one of Slack's official Block Kit example templates.</figcaption>

</figure>

如果你对发送这样的消息感兴趣，你首先必须设置一个 Slack 应用程序。你需要有一个可用于开发应用程序的帐户和工作空间。一旦你做好准备，你需要访问[https://api.slack.com/apps](https://api.slack.com/apps)并点击“创建新应用”按钮。

您可以随意命名它，并选择您将用于测试的工作区。开始发送信息的最快方法是设置一个网页挂钩，所以点击侧边栏菜单中的“传入网页挂钩”并切换开关来激活传入网页挂钩。

现在，在页面的底部，您应该看到一个按钮，用于向您的工作区添加一个新的 webhook。点击它会提示你选择一个频道并授权应用程序。既然你应该能够相信你不会滥用你自己的应用程序，那就继续下去，选择你想发送测试消息的任何通道，并授权给你自己。

您将被重定向回上一页，但现在您将看到您刚刚选择的频道的网络链接。复制该 URL，因为我们现在要用它来发送消息！

为了确保您的 webhook 正常工作，您可以使用下面的 curl 命令向自己发送一条简单的消息，只需确保用您刚刚复制的 url 替换 webhook URL。

```
curl -X POST -H 'Content-type: application/json' --data '{"text":"Hello, World!"}' https://hooks.slack.com/services/YOUR/WEBHOOK/HERE 
```

Enter fullscreen mode Exit fullscreen mode

现在，发出一个封锁消息，事情开始变得有点疯狂。为了简单起见，我们将使用 curl 命令发送一个完整的 block 消息，但是要知道这是一个非常简单的消息，我不允许使用 curl 作为发布 Slack 消息的主要形式。

```
curl -X POST -H 'Content-type: application/json' --data '{
    "blocks": [
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": "*<fakeLink.toUserProfiles.com|Iris / Zelda 1-1>*\nTuesday, January 21 4:00-4:30pm\nBuilding 2 - Havarti Cheese (3)\n2 guests"
            },
            "accessory": {
                "type": "image",
                "image_url": "https://api.slack.com/img/blocks/bkb_template_images/notifications.png",
                "alt_text": "calendar thumbnail"
            }
        }
    ]
}' https://hooks.slack.com/services/YOUR/WEBHOOK/HERE 
```

Enter fullscreen mode Exit fullscreen mode

如果你把它输入到你的终端(你不会从网上直接复制并粘贴随机的多行命令到你的终端，对吗？)您将看到 Slack 的另一个示例模板的简化版本。在这种情况下，这是一个简单的会议提醒消息，带有一些信息和一个漂亮的小图形，让您知道这是一个日历事件。

至于这些模块实际上是如何工作的，让我们来看看这些位是什么。

数据选项中的整个 JSON 对象是一个消息有效负载。在块之前，您必须包含像第一个消息示例中的`text`键这样的东西。现在有了块，你就可以有一个`blocks`键，它将是你想要包含在你的消息中的块的数组。因为它是一个数组，所以您可以在一条消息中发送任意多的块。这就是为什么我在这篇文章开始的时候有非常丰富的图片。

在每个块对象中，你至少需要有一个`type`属性。您可以拥有的最一般的类型是`section`，这意味着它是块的一般类型。您还可以拥有`image`、`context`、`actions`和`divider`类型的块，它们都有稍微不同的用例。

在我们的示例块中，我们在`text`属性中包含了一些文本。文本值本身就是一个对象，可以是`mrkdwn`或`plain_text`。在这种情况下，我们使用 Markdown，因为会议的标题是一个粗体链接。文本的`text`属性是实际文本所在的位置。

最后，这个部分有一个特殊的额外部分，叫做附件。附件可以是图像、按钮、日期选择器或整个菜单。在本例中，我们添加了一个图像，特别是一个日历图标的图像。

[![A Slack message styled to look like a calendar event reminder](img/abc5e85eca58e23f5e637d19e0bd6f0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yCO7YGIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/at5ollgs7dz9cwiia9qs.png)

要更深入地了解这些丰富的消息布局，请查看官方 Slack 文档关于[创建丰富的消息布局](https://api.slack.com/messaging/composing/layouts)！

Block Kit 相当新，我还没有看到我想要的对它的支持。因为我目前选择的框架是 Laravel，所以我试图找出是否有一种简单的方法来发送块消息。虽然有一个官方的软件包可以向 Slack 发送通知，但他们决定不支持 Block Kit😞

我已经创建了一个包，基本上扩展了官方的包，但是包含了对 Block Kit 的支持。如果您使用 Laravel 并且对利用这个丰富的通知系统感兴趣，请查看我在 GitHub 上的包！

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)

### 官方“laravel/slack-notification-channel”包的扩展，增加了对 Slack 块的支持。

<article class="markdown-body entry-content container-lg" itemprop="text">

# Laravel 松弛块

这个包是官方`laravel/slack-notification-channel`包的扩展。

## 使用

你应该要求这个包，而不是要求官方包。

```
composer require nathanheffley/laravel-slack-blocks 
```

因为这个包是建立在官方包的基础上的，所以你将拥有在[官方文档](https://laravel.com/docs/5.8/notifications#slack-notifications)中找到的所有功能。

您可以遵循这些说明，只需稍微调整一下，要求从`NathanHeffley\LaravelSlackBlocks`而不是`Illuminate\Notifications`开始的类。

这些扩展类支持基本 Illuminate Notifications 类中支持的所有内容。

如果要将块添加到时差消息中，需要将块添加到附件中。

```
use NathanHeffley\LaravelSlackBlocks\Messages\SlackMessage;

// ...

public function toSlack($notifiable)
{
    return (new SlackMessage)
        ->attachment(function ($attachment) {
            $attachment->block(function ($block) {
                $block
                    ->type('section')
                    ->text([
                        'type' => 'mrkdwn',
                        'text' => '*Hello World!*',
                    ]);
            });
        });
} 
```

要查看可以添加到块中的所有可能的字段，请查看…

</article>

[View on GitHub](https://github.com/nathanheffley/laravel-slack-blocks)