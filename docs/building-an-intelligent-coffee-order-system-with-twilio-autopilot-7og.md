# 用 Twilio Autopilot 构建智能咖啡点餐系统

> 原文：<https://dev.to/twilio/building-an-intelligent-coffee-order-system-with-twilio-autopilot-7og>

如果你能每天节省一些与他人的交流，也许是尴尬的交流，并让机器人来处理，这不是很好吗？然而，通常这些机器人在与你互动时并不太智能。在这篇博文中，我们将在几分钟内学习如何构建一个更智能的短信机器人。

在之前的一篇博文中，我们谈到了我们如何喜欢在会议和其他活动中提供“Twilio 风格”的咖啡，允许与会者通过短信订购咖啡。如果你还没有读过这篇博文，一定要看看用 Twilio 可编程短信服务咖啡的[和 React](https://www.twilio.com/blog/2018/03/serving-coffee-with-sms-and-react.html) 。不幸的是，我们处理订单的方式相当简单。直到现在。

让我们看看如何使用 Twilio Autopilot 将这个现有项目升级为真正的智能。

如果你更喜欢通过观看视频来了解这是如何工作的，这里是我们在 YouTube 上的[教程](https://www.youtube.com/watch?v=gOOrvhbJ9No) :
[https://www.youtube.com/embed/gOOrvhbJ9No](https://www.youtube.com/embed/gOOrvhbJ9No)

## 设置

在我们开始之前，请确保您已经准备好了设置。如果你已经执行了我们之前的博客文章中的步骤，你就已经准备好了。如果你还没有，一定要看看“用 Twilio 可编程短信服务咖啡和反应”。或者遵循 [Barista Lite GitHub 项目](https://github.com/dkundel/barista-lite)的`README`中的设置说明。

## 打造您的个人咖啡助手

为了开始使用 Twilio Autopilot，我们必须创建一个新的“助手”。为此，在 Twilio 控制台的[自动驾驶部分创建一个新助手。您可以给助理起任何名字。我暂时把我的命名为“咖啡机器人”。](https://www.twilio.com/console/autopilot/list)

[![](img/da8a0458c8ad42fd05bd8650b447ee5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KpmrqaJA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/49hWq7ZmrekGOB9Z8U4V5ZeWudwozNDPaG3U-t1yAErFNN.width-500.png)

创建后，让我们确保将它连接到我们的电话号码，开始测试开箱即用的体验。点击左侧的“频道”并选择“可编程信息”。

[![](img/7de86ab6086c81323efd52bd2d668e85.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--aKxZBhoP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/9P5-R320tIvGzPkzz8F3P3ADZq49A3Nf0_ztI4uCgtUmpQ.width-500.png) [ ![](img/62bcb2316f431eaa1f6ef60fc87f1e37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eZV7GQjz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/UsACrs-1LdTAltmQjlpr2N2zWeefBKriL1dNc79lxAi00w.width-500.png)

您应该会在那里看到一个 URL。复制它并转到您在 [Twilio 控制台电话号码部分](https://www.twilio.com/console/phone-numbers)中的电话号码，并将“当有消息进来时”网页挂钩更新到您复制的 URL，并确保按下保存。

[![](img/532c5944072a3b798e216cca396357c9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8nxS4xpM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/iI_1-681QCN7ieoc8nwQxVBxAOy9z2Vl1isemiMsYswQVW.width-500.png)

一旦保存，拿起你的手机，发送任何消息到你的号码。你应该看到一个回答“这是你的新任务”。

此消息来自创建助理时创建的默认任务。但是什么是任务呢？

## 什么是任务、样本、字段、模型、...？？？

当你开始使用 Autopilot 时，如果你以前没有使用过任何自然语言处理，可能会有很多新的术语让你感到困惑。所以让我们试着把这些弄清楚一点。

*   任务:这些是您想要执行的不同工作单元。Autopilot 允许你在任务中执行不同的“动作”。这些可以是向用户传达信息的“说”，将信息转发给人的“传递”，ping 一个 webhook 以决定下一步做什么的“重定向”，收集一堆数据的“收集”，等等。
*   **示例:**为了让 Autopilot 理解何时触发哪个任务，我们需要向它提供示例语句，并将这些语句映射到现有任务。每个任务的样本越多，你的机器人就越能智能地将你的用户引导到正确的任务。
*   **字段:**有时您的样品不是完全静态的。例如，像“我想喝一杯浓咖啡”这样的样本有一些您想要提取的重要信息，这些信息可能因用户而异。假设咖啡的数量和类型实际上是这个示例中的字段。稍后，我们将了解如何使用它们以及不同的可用字段类型。
*   **模型:**每次你修改自动驾驶助手的这些部分，你都必须重新构建一个新的模型。你可以把一个模型想象成一棵巨大的“智能”决策树，它是你给助手的所有信息的结果。你给它的信息越多，你的模型就越复杂，越智能。

## 创建你的第一个任务

为了查看您现有的任务并创建新任务，请点击您助手的[“任务构建器”部分](https://www.twilio.com/console/autopilot/CoffeeBot/tasks)。您将看到一个已经存在的任务，名为“hello_world”。它还附有一些标签，表明它已经被配置为`Fallback`、`Initiation`和`OnFailure`任务。如果您想了解所有这些的含义或更改其中一个，请单击 Tasks 视图中的“Defaults”选项卡。

[![](img/ec7c784e30ae9fb1195adc037848f48a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n1RAo3QI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/bOy5avgMYP0lGYYvJilNyoz1GHQJA3shORznw1z2hbUhBF.width-500.png)

让我们从修改这个任务开始，而不是从头开始创建一个任务。单击该任务，您将看到一个代码编辑器弹出，其中包含以下 JSON:

```
{  "actions":  [  {  "say":  "This is your new Task"  }  ]  } 
```

在里面，你可以看到它显示了我们之前收到的确切文本。让我们把它修改成一个更友好的问候:

```
{  "actions":  [  {  "say":  "Hi there! Thanks for reaching out to your friendly Coffee Bot. Please let me know what you would like to order."  },  {  "listen":  true  }  ]  } 
```

正如你所看到的，我们改变了“say”键后面的文本，但也添加了第二个动作“listen ”,告诉 Autopilot 应该保持会话打开。修改 JSON 后，点击 save。

[![](img/f78c1b24e83ade9fecc644a0a516efd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--69V8xbF8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/cJ5p8Phj__TGermuE-aBvY5V2e_Zh3Tk6FJgm3GDdUqAMJ.width-500.png)

接下来，让我们添加一些示例来触发此任务。转到[“自然语言路由器”部分](https://www.twilio.com/console/autopilot/CoffeeBot/router)，点击文本字段旁边的扩展按钮。这样，我们可以通过在不同的行上添加多个样本来一次添加它们。随意添加任何你想要的例句或者复制下面的:

```
hey there
hi
what's up
hello 
```

选择旁边的`hello_world`任务，按“+”按钮添加它们:

[![](img/96e2a23417dfe4fff74e8d5b53fa7724.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EE_GX9lb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/Faf81oe0R0sCEeJXMYKozA1UL57RE_LdzZ_cI3_EmflWEz.width-500.png)

在做了所有这些改变之后，我们需要创建一个新的模型。转到[“构建模型”选项卡](https://www.twilio.com/console/autopilot/CoffeeBot/router/model-builds)并创建一个新的模型构建，名称为“v0.0.1”。

[![](img/aadb6217a6f77f52a77d8c7c6c3ab8c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---oHrhoPB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/hry2KwfbeZPRAtIzXb9DHzVx-ofPU-TNwJZkXasJdXBMIU.width-500.png)

一旦你的模型的状态变为“完成”,你的机器人就可以进行测试了。

[![](img/d14580e6f51f0fdede42e78499baea8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WoVxuQwN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/C-tFP-Quat8QSN9CVFNKXdGUZ8GNN8d3_lc1CQmyknzwmq.width-500.png)

对于这个文本，你想你的号码，你应该会看到一个更新的消息。

[![Screenshot of Autopilot welcome message](img/ecdd685f5075a5858e9353536a86c7c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rLqhLhsD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/Photo_20.02.19_18_38_20.width-500.jpg)

## 创建动态任务

[![](img/17c923144c890e6714db4ad6c7b4c213.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ZV2wnIl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/original_images/Ms1HxAWtbxB7kM9bNbClZvK_p7n_S8-FM7qzbAQxPTn8ifIuAT-4v5xiMIOPuhOLSEyLx6jJZ8zm5a)

好了，现在我们有了一个静态任务，让我们使用字段引入一些更动态的体验。如前所述，字段允许您在样本中添加占位符，这些占位符稍后将由 Autopilot 自动提取，以便我们可以使用它们。

在我们处理字段之前，我们需要创建一个新的任务，并在以后处理字段值。为此，让我们首先创建一个 Twilio 函数，它将记录一个字段的值并返回一个`say`动作。

转到 [Twilio 运行时](https://www.twilio.com/console/runtime/overview)的[函数部分](https://www.twilio.com/console/runtime/functions/manage)，创建一个新的“空白”函数。给它起一个类似“日志字段值”的名字，并添加一个类似`/log-value`的路径。将您的函数代码更改如下:

```
exports.handler = function (context, event, callback) {
  console.log(event.Field_Quantity_Value)
  const response = {
    actions: [
      {
        say: "Thank you for your order."
      }
    ]
  }
  callback(null, response);
}; 
```

然后回到你的 CoffeeBot 助手，在[任务构建器部分](https://www.twilio.com/console/autopilot/CoffeeBot/tasks)创建一个新任务。给它起一个类似`new_order`的名字，并添加下面的代码:

```
{  "actions":  [  {  "redirect":  "https://<your_runtime>.twil.io/log-value"  }  ]  } 
```

[![](img/684426e5c97aac6d392900ba53e6ffcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FiZFc-Hi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/fYpklGZsM-qIrl0CT9rngsiE8X_Js0_43lcErVBg5Pr6GO.width-500.png)

确保使用 Twilio 函数的链接更新 URL。之后，单击保存以确保您的更改不会丢失。

接下来，我们需要向该任务添加字段。单击我们任务旁边的“修改”链接，并在对话框中单击“查看字段”链接。在那里你可以添加新的字段。创建一个名为`Quantity`的新字段。Autopilot 为字段提供了各种预定义的数据类型。在我们的例子中，我们关心数量。所以请选择“Twilio”。数字"作为该字段的类型。

[![](img/f4afb8ce21d11a537afcfa671d4a526b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MSIK94XQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/y_w-gsw-R4vvZk0onIP9MSIi4GppSYQEkiOeM3rPkSzOQj.width-500.png)

然后再次关闭对话框，切换到*S*T2【样品】T3，因为我们必须为我们的任务添加新的样品。展开输入字段，并在其中输入以下值:

```
I would like to have {Quantity} coffee please.
{Quantity} coffee.
Could you make me {Quantity} coffee please?
Can I have {Quantity} coffee? 
```

`{Quantity}`告诉 Autopilot 这是字段“数量”的占位符。将样本与`new_order`任务相关联，并点击“+”按钮添加它们。

[![](img/a0d9ab4df67a7ba5056e87aa7d273a05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vFcTX3Yv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/-BU6Naz8LjEzWDvq0OT8qkIu_2CH7bI9XgkCWDFVG7VJQP.width-500.png)

创建样本后，再次进入`Build Models`部分并触发新的模型构建。一旦完成，回到你的 Twilio 函数，看看函数底部的日志，拿出你的手机。

给它发类似“请给我 10 杯咖啡”的短信，然后查看你的函数日志。您应该会看到日志“10”。

这是因为我们正在记录参数`Field_Quantity_Value`。Autopilot 会自动将每个捕获的字段作为`Field_<FIELD_NAME>_Value`传递给 webhook。你也可以看到它把`Ten`变成了`10`。那是因为我们告诉 Autopilot 这个字段是 number 类型的。它现在将把这两个数字都作为数字或单词来处理。试试发短信“我能要 15 杯咖啡吗？”

[![](img/dd528907a17263b965a53cd181467ea3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IPkzapbK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/H8UqFjqp_mdzWYcguT-LQY43lEoi_hp5hxkhkyJrUq1tY_.width-500.png)

## 添加自定义字段类型

虽然内置字段类型适用于各种用例，但有时您会希望拥有自己的字段类型。在我们的 CoffeeBot 案例中，这将是，例如，我们提供的不同类型的咖啡。

[![](img/cd66c1cacfcaa1c57a0bd6e2f205628e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I14ChgCS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/original_images/JYu4LjtQ3d9OOvdLkgAN0MeLvUTKI_Q7LVvxquUzrL_7Pyiex6UqoDXReMAA0BOdo9L_xYZmQBdcHp)

要创建一个新的自定义字段类型，进入你的机器人的自然语言路由器部分，点击[管理字段](https://www.twilio.com/console/autopilot/CoffeeBot/router/field-types)选项卡。按下“创建您的第一个字段类型”按钮，并给它起一个类似于`coffee_type`的名字。创建后，单击字段类型列表中的名称，然后按加号按钮添加新示例。这是您想要为该类型添加可能值的地方。在我们的例子中，这将是任何有效的咖啡类型。为了便于使用，您可以每行输入一个值。

```
espresso
americano
latte
mocha
tea
coffee
flat white 
```

[![](img/e624dfded0fde0f2edcb8afd185b4645.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5r1rXa7_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/6XJxGn-YtMAIQHueJkmLVQg7shhrlxZ9VJ94O2GGI9_6l-.width-500.png)

请注意，您在此处提供的值不是唯一有效的值。Autopilot 将学习并尝试将其他单词与该字段类型相匹配。

在您创建它们之后，让我们添加`CoffeeType`作为我们的`new_order`任务的有效字段。返回到您的`new_order`任务的[视图字段](https://www.twilio.com/console/autopilot/CoffeeBot/tasks/tasks/new_order/fields)部分，添加一个名为`CoffeeType`和数据类型为`coffee_type`的新字段。

[![](img/c2617f597185a9b090906eb4d17fdae0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--INDGSm6f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/UW5CWm2oHG0Ye4yhEbGM2Gzt_g0sMqLnXLRjnAZyNhHyIp.width-500.png)

现在，在我们构建新模型之前，我们需要更新我们的示例以使用新字段。继续删除`new_order`的旧样本，并使用以下值创建新样本:

```
I would like to have {Quantity} {CoffeeType} please.
{Quantity} {CoffeeType}.
{CoffeeType}
Could you make me {Quantity} {CoffeeType} please?
Can I have {Quantity} {CoffeeType}? 
```

保存后，进入[模型构建](https://www.twilio.com/console/autopilot/CoffeeBot/router/model-builds)并通过在您的构建名称中增加版本来构建一个新模型。

您的模型现在将能够理解数量和不同的咖啡类型。

## 连接我们的订购系统

为了实际上能够创建新订单，我们现在需要将我们正在触发的操作更改为我们原始的 Twilio 函数 URL，并更新我们的 Twilio 函数。

从之前的博文中找到你的“咖啡师创建订单”Twilio 函数，并相应地更新它:

```
exports.handler = function (context, event, callback) {
  const ORDER_LIST = 'orders';
  const SERVICE_SID = context.SYNC_SERVICE_SID || 'enter Sync Service Sid';

  const orderType = event.Field_CoffeeType_Value;
  const orderQuantity = event.Field_Quantity_Value || 1;
  const order = `${orderQuantity}x ${orderType}`;

  const orderData = {
    order: order,
    phoneNumber: event.UserIdentifier,
    status: 'open'
  };

  // Create a sync list item for the order
  const twilioClient = context.getTwilioClient();
  twilioClient.sync
    .services(SERVICE_SID)
    .syncLists(ORDER_LIST)
    .syncListItems.create({ data: orderData })
    .then(x => {
      callback(null, {
        actions: [
          {
            say: `Thank you for ordering ${order}`
          }
        ]
      });
    })
    .catch(err => callback(err));
}; 
```

这将读取正确的字段，并且如果没有检测到数量，则退回到数量 1。此外，我们在这里使用`event.UserIdentifier`来获取电话号码，因为这将是短信和语音机器人的电话号码。注意，举例来说，这不适用于 Alexa 或 Google Home。

保存您的函数更改，并将 URL 复制到您的函数。返回到您的自动驾驶任务，修改`new_order`任务，并更新重定向到您的函数 URL 的 URL。

确保再次重建模型以捕捉所有更改。

如果你还没有打开你的终端，现在就打开它，运行:
来启动你的 React 界面

```
npm start 
```

您应该会在浏览器中看到以下屏幕:

[![](img/5723326b9f95cc5f2f98c2e822be9f8c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jATqEcGB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/2JO_7mES5rGq68K36k1R3zzMbntaURgFOGIeAnE_7K7ST-.width-500.png)

让我们通过发短信来测试一切是否正常，比如“我能要两杯拿铁吗？”。请注意，我们将它拼写成小写，并且改变了例句中的句子。

[![](img/82d9f5e08610ed81e4573af33955ef04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DnQJgRXq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/diU-_pCiOEGwVmo-TlPFQwCrQpwi1ql3JmPn5uzjR3KNAD.width-500.png)

你应该会得到一个“2 杯拿铁”订单的确认，它应该会出现在浏览器 UI 中。点击“完成订单”或“取消订单”,您将通过短信收到相应的确认信息。

[![](img/1c2c88d44fd3fe0c729fe34270189a04.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--80YHQfSv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-doimg/Sg9I7t6-Syf7KyG7gsYgDAFNwE1E5iGz2oLSaas0RrUxkX.width-500.jpg)

[![](img/28c301831471039186785f04e4e55469.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WT5X7IU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/original_images/huVgRNib8wjC1mQEyROrziT0FbnFep5FkPh6MVSkEGzql7ZpNjgqMXE_Ai78qxIH9bYfcbJeKhbggU)

## 接下来是什么？

[![](img/9354c0f02a7b7bcabcfa1563ad84ac98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7NO5Ko2H--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3.amazonaws.com/com.twilio.prod.twilio-docs/original_images/hyNQhFfiLX-90pf78CoPKD8NYdP78qRNR3NEuaB3ICj_hghwgkQ3XUpBbe9fqNrYv28vSa9RMbHGRU)

这只是你能用 Twilio Autopilot 做的事情的开始。如果你想了解更多，我建议你查看一下[收集动作](https://www.twilio.com/docs/autopilot/actions/collect)，它可以让你轻松地填写表格。例如，你可以用它来询问顾客额外的信息，比如他们的名字，或者他们是否想在他们的咖啡中加入豆奶。或者看看你如何使用[切换](https://www.twilio.com/docs/autopilot/actions/hand-off)功能来连接到 [Twilio Flex](https://www.twilio.com/flex) 代理或直接连接到你的咖啡师的电话，以解决机器人无法回答的任何问题。

例如，如果你想将你的机器人导入/导出到另一个账户，你应该检查一下[自动驾驶命令行界面](https://github.com/twilio/autopilot-cli)，它将帮助你构建你的机器人。

如果你有任何问题，或者如果你想让我看看你用自动驾驶仪做的很酷的东西，或者只是一般来说，请随时联系我:

*   [dkundel@twilio.com](//mailto:dkundel@twilio.com)

*   GitHub: [dkundel](https://github.com/dkundel)
*   [dkundel.com](https://dkundel.com/)