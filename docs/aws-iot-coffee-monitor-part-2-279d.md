# AWS 物联网咖啡监控器–第 2 部分

> 原文：<https://dev.to/rehanvdm/aws-iot-coffee-monitor-part-2-279d>

这篇文章最初发表在我的个人博客上。

这是构建 AWS 物联网咖啡监控器的第 2 部分。第 1 部分可以在[这里](https://dev.to/rehanvdm/aws-iot-coffee-monitor-part-1-33ic)找到

在第 1 部分中，我们讨论了如何将物联网数据放入云中。我们连接了我们的设备 ESP32，并成功地将我们一杯咖啡的温度发送到云端。现在，我们将数据存储在 DynamoDB 中，决定我们的咖啡处于什么状态，如果需要，发送电报推送通知，告诉我们抓紧时间继续喝(在它变凉之前喝掉它)。这是架构图:

[![Architect Diagram](img/8693b35cd1848e624a14adb308fba128.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BxPaDynz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rehanvdm.com/contents/data/2019/04/post5_CoffeeMonitor-Original.jpg)

**先决条件**:

*   熟悉 AWS
*   在你的手机上安装电报
*   安装并配置 AWS SDK
*   安装了 AWS SAM(进一步提供简要说明)

# 电报机器人诞生了

创建一个新的电报机器人比你想象的要容易得多。打开应用程序，搜索**机器人父亲**，然后输入*/新机器人*，给你的机器人起个名字，然后输入唯一的用户名。然后你会收到一条祝贺消息，点击第一个链接开始与你的机器人聊天，再往下你会得到一个机器人令牌。请保留此 bot 令牌的副本，因为我们稍后将使用它向此 bot 和我们的聊天发送推送通知。

[![Bots](img/d15b4c8963bf76b0556dc01a29e9d0bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3tVPXzSq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tymnkmeuw1f4xtwbaozl.png)

# IaC 和 SAM

作为代码的基础设施，这个概念包括描述和提供您的代码将在其中生存的环境。在自动气象站的情况下，这种描述是云形成模板的格式。云形成(CF)模板只是一个 YAML/JSON 文档，它描述了您的代码将使用的资源，然后这个文件与您的代码一起被签入。

这些 CF 模板随后被部署并成为 CF 栈。以这种方式编写应用程序有很多好处。有些包括，你可以点击一个按钮，摧毁应用程序和它使用的所有资源，所以你可以在不使用它的时候杀死它。这个 CF 模板也可以部署在许多地区，并且对 CF 栈的更新可以同时将更改部署到所有地区。

由于 CF 模板编写起来很繁琐，我们将使用 AWS **S** 无服务器 **A** 应用程序 **M** odel，SAM 的简称。SAM 只是一个额外的抽象，让我们更容易编写 CF 模板，减少代码/行数。SAM 模板被转换为 CF 模板，然后作为 CF 堆栈进行部署。你可以在这里找到更多关于山姆的信息。

如果这是你第一次使用 SAM，你需要按照说明安装它[这里](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)。如果你跟随然后你将不得不创建一个 S3 桶，山姆将存储封装的 CF 模板，我命名为“rehan-sam-packages”，现在这样做。

我们将在 template.yaml 文件中定义我们的资源和环境，稍后会详细介绍。

然后需要三个命令来在 AWS 上部署和运行我们的应用程序:

*   sam 构建
*   山姆·帕克
*   sam 部署

稍后将通过传递额外的参数(主要是环境变量)来使用它们。

# CF 模板

完整的代码可以在[这里](https://github.com/rehanvdm/CoffeeMonitor/blob/master/template.yaml)找到。

环境适合不到 150 行的 YAML，它可以在这里查看。让我们分析一下文件。第一部分需要告诉 CF 如何正确地将这个 SAM 模板转换成 CF 模板。

```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Description: Coffee Monitor 
```

接下来，我们为我们的堆栈定义环境参数，当我们构建和部署它时，这些参数将被传入:

```
Parameters:
  AppName:
    Default: coffeemonitor
    Type: String
    Description: Name of app
  AppEnvironment:
    Type: String
    AllowedValues:
      - prod
    Description: Environment of this stack
  AppVersion:
      Type: String
      Description: Version of this stack in form of SemVer
  AppBuild:
      Type: String
      Description: Build number of this stack
  TelegramBotToken:
    Type: String
    Description: "The  bot  token  received  from  botfather  after  using  /newbot"
  TelegramBotChatId:
    Type: String
    Description: "Send:  chat  id?  to  the  bot,  fill  this  in  and  then  redeploy  the  stack"
  MaxTemp:
    Default: 50
    Type: String
    Description: The temperature which we use to determine that a coffee is at a good drinking temperature
  MinTemp:
      Default: 40
      Type: String
      Description: The temperature which we use to determine that a coffee is now cold 
```

值得注意的是 *TelegramBotToken* 和 *TelegramBotChatId* 环境变量，在部署时您需要用自己的环境变量替换它们。其他字段仅用于版本控制。MinTemp 和 MaxTemp 是用来定义我们想要的咖啡的范围的值。对我来说，喝咖啡的最佳温度是 40℃到 50℃

> MinTemp 和 MaxTemp 是用来定义我们想要的咖啡的范围的值

然后我们有一个全局变量部分，我们说所有的函数都有这些值，除非另外指定。

```
Globals:
    Function:
        Timeout: 12
        Environment:
          Variables:
            APPNAME: !Ref AppName
            ENVIRONMENT: !Ref AppEnvironment
            VERSION: !Ref AppVersion
            BUILD: !Ref AppBuild
        Tags:
            APPNAME: !Ref AppName
            ENVIRONMENT: !Ref AppEnvironment
            VERSION: !Ref AppVersion
            BUILD: !Ref AppBuild 
```

在下面的参考资料部分，我们开始用 Lambda 函数定义我们的环境，该函数将监听物联网设备 ESP32。

```
Resources:
    CoffeeMonitorLambda:
        DependsOn: TelegramSNSTopic
        Type: AWS::Serverless::Function
        Properties:
            FunctionName: !Join ['', ['coffee-monitor-', !Ref AppEnvironment]]
            Runtime: nodejs8.10
            CodeUri: lambdas/coffee-monitor/
            Handler: app.handler
            Policies:
              - DynamoDBCrudPolicy:
                  TableName: !Join ['', ['coffee-monitor-', !Ref AppEnvironment]]
              - SNSPublishMessagePolicy:
                  TopicName: !GetAtt TelegramSNSTopic.TopicName
            Environment:
              Variables:
                SNS_TOPIC_TELEGRAM: !Ref TelegramSNSTopic
                TEMP_MAX: !Ref MaxTemp
                TEMP_MIN: !Ref MinTemp
            Events:
              IoT:
                Type: IoTRule
                Properties:
                  Sql: "SELECT  *  FROM  'coffee-monitor/#'"
                  AwsIotSqlVersion: '2016-03-23' 
```

我们指定我们的 Lambda 函数使用 NodeJS 版，并告诉它代码位于哪个文件夹中。然后我们定义策略，这些策略允许访问其他 AWS 资源，我们指定这个 lambda 可以在名为 *coffee-monito-prod* 的表上执行 CRUD，其中 prod 是参数中传递的环境名称。我们还指定允许向我们稍后定义的 TelegramSNS 主题发送消息。

那么触发这个λ的事件就是物联网规则。SQL 用于选择发送到这个 lambda 的数据，这里我们说所有来自我们的 coffee-monitor 设备的数据都必须发送到这个 Lambda。如果我们只对 1 个设备位置感兴趣，我们应该将 from 改为: *coffee-monitor/home/#*

接下来，我们定义 DynamoDB 表，我们将在其中存储 NoSQL 数据。我们定义了分区键和排序键列以及 TTL 列。这是 DynamoDB 删除旧数据的内部机制，如果我们提供一个带有 *expiration_ts* timestamp 属性的行，那么 DynamoDB 将在该时间到达时自动删除它。对于这个项目，所有时间序列数据都有 30 天的到期日。

```
DynamoDBControlTable:
  Type: AWS::DynamoDB::Table
  Properties:
    TableName: !Join ['', ['coffee-monitor-', !Ref AppEnvironment]]
    KeySchema:
      - AttributeName: "PK"
        KeyType: "HASH"
      - AttributeName: "SK"
        KeyType: "RANGE"
    AttributeDefinitions:
      - AttributeName: "PK"
        AttributeType: "S"
      - AttributeName: "SK"
        AttributeType: "S"
    TimeToLiveSpecification:
      AttributeName: expiration_ts
      Enabled: true
    BillingMode: PAY_PER_REQUEST 
```

还要注意，我们的 DynamoDB 表被设置为按请求付费。接下来我们定义第二个λ函数，电报λ。它将由两个事件触发。第一个是通过 API，我们需要为我们的 bot 设置回调 URL，这样当我们回复它时，Telegram 会将回复发送到我们的 URL，URL 又会将数据发送到这个 Lambda。当将 API 路径定义为 proxy 和 method any 时，所有到达该端点的数据都将被发送到 Lambda 函数。

第二个触发事件是 SNS 话题。我们的第一个 Lambda 函数将发布事件消息(比如 new coffee)到 SNS 主题，这个 Lambda 也订阅了这个主题。然后，它将按原样接收消息，并使用 HTTPS 将其发送到 Telegram，作为推送通知发送给我们。

```
ApiLambdaFunction:
   Type: AWS::Serverless::Function
   Properties:
     FunctionName: !Join ['', ['coffee-monitor-telegram-api-bot-', !Ref AppEnvironment]]
     Runtime: nodejs8.10
     CodeUri: lambdas/telegram-bot/
     Handler: app.handler
     Environment:
       Variables:
         TELEGRAM_BOT_TOKEN: !Ref TelegramBotToken
         TELEGRAM_BOT_CHAT_ID: !Ref TelegramBotChatId
     Events:
       ReceiveAPIRequest:
         Type: Api
         Properties:
           Path: /{proxy+}
           Method: ANY
           RestApiId: !Ref CoffeeMonitorApi
       SendSNSEvent:
           Type: SNS
           Properties:
             Topic: !Ref TelegramSNSTopic 
```

我们还向该函数传递发送推送通知所需的额外环境变量。最后的资源是 API 网关和贯穿全文的 SNS 主题。

```
CoffeeMonitorApi:
  Type: AWS::Serverless::Api
  Properties:
    Name: !Join ['', ['coffee-monitor-api-', !Ref AppEnvironment]]
    StageName: !Ref AppEnvironment

TelegramSNSTopic:
  Type: 'AWS::SNS::Topic'
  Properties:
    DisplayName: !Join ['', ['coffee-monitor-telegram-bot-', !Ref AppEnvironment]]

Lastly we define the Output section, which CF will produce when the Stack is deployed. It is usefull information that we can use.

Outputs:
    ApiURL:
      Description: "API  Gateway  endpoint  URL"
      Value: !Sub "https://${CoffeeMonitorApi}.execute-api.${AWS::Region}.amazonaws.com/${AppEnvironment}/"
    RegisterTelegramBot:
      Description: "Navigate  to  the  URL  below  to  let  your  bot  push  to  your  API  Endpoint"
      Value: !Sub "https://api.telegram.org/bot${TelegramBotToken}/setWebHook?url=https://${CoffeeMonitorApi}.execute-api.${AWS::Region}.amazonaws.com/${AppEnvironment}/bot_callback"
    TelegramSNSTopic:
      Description: "TelegramSNSTopic  ARN"
      Value: !Ref TelegramSNSTopic 
```

在这种情况下，它将输出 API URL，一个链接，我们可以单击该链接将这个 API 网关 URL 注册为我们的 bot 的回调(关于这一点的更多信息，请参阅部署部分),然后是 SNS 主题。

就是这样，这几行完整的描述了我们的架构图资源。接下来让我们看看重要的代码，然后是部署过程。

# Lambda 码–咖啡监控器物联网

完整的代码可以在[这里](https://github.com/rehanvdm/CoffeeMonitor/tree/master/lambdas/coffee-monitor)找到

首先，所有的 Lambda 代码都包装在一个 try-catch 中，最后，它自始至终使用 ES6 和本机承诺。我们打开传入的 AWS 物联网核心事件，在这种情况下，我们只有 2 个事件，即温度和按钮点击。开关为每一个做适当的代码块，如果是按钮点击，那么它插入一个记录到 Dynamo DB，并通知我们的 SNS 主题。

```
module.exports.handler = async (event, context) =>
 {
     let response = null;
     try
     {
         let dtNow = moment().toISOString();
         switch (event.event_type)
         {
             case "button_click":
                 console.log("BUTTON 0 was pressed", event.click_no);
                 let expiration_ts_30_days = (Math.round(Date.now() / 1000) + (60 * 60 * 24 * 30));
                 await db_event.Insert(DynmaoDB_Events.EVENTS.BUTTON_CLICK, dtNow, event.click_no, expiration_ts_30_days);
                 await SendSNSNotification("Button Clicked: " +  event.click_no);
                 break;

             case "temp":
                 console.log("TEMP is", event.value);
                 await TempChange(event, dtNow);
                 break;

             default:
                 console.log(event);
                 break;
         }
         response = true;
     }
     catch (err)
     {
         console.error(err);
         response = false;
     }
     finally
     {
         //console.log("* Response: " + response)
         return response;
     }
 }; 
```

如果是温度事件，它将进入*温度变化*功能。使用环境变量传入最小和最大温度值，然后我们得到咖啡的当前状态，最后的温度值，我们也保存当前的温度值。以上 3 个操作都是并行执行的。我们还处理一些边缘情况，比如没有先前的状态或温度，表明表是空的。

然后我们到了有趣的部分，状态机决定何时触发一个变更事件。以下条件会触发这些状态:

*   **新咖啡**:如果当前温度与之前温度相差超过 5 度，且状态为无咖啡或咖啡变冷，则转换
*   **好咖啡**:如果温度差变为负值，表示正在冷却，如果温度介于最小值和最大值之间，且处于 NEW_COFFEE 状态，则转换。
*   **变冷**:温度低于最小温度且处于良好温度状态时转换。

这些状态变化中的每一个都向 SNS 电报主题发送消息，并并行保存当前状态。

```
async function TempChange(event, dtNow)
{
    let minTemp = process.env.TEMP_MIN;
    let maxTemp = process.env.TEMP_MAX;
    let temp = event.value;

    let expiration_ts_30_days = (Math.round(Date.now() / 1000) + (60 * 60 * 24 * 30));

    /* PARALLEL: Get Current State, Last Temperature and save Current Temperature */
    let [pState, pLastTemp, pSaveTemp] = [db_State.GetCurrentState(),
                                            db_event.GetLastTempValue(),
                                            db_event.Insert(DynmaoDB_Events.EVENTS.TEMP, dtNow, temp, expiration_ts_30_days)];
    let currentState = await pState;
    let prevTemp = await pLastTemp;
    await pSaveTemp;

    /* If no prev state, then means clean table, just insert a No Coffee state so that checking logic not fail */
    if(!currentState)
        await db_State.Insert(DynmaoDB_State.STATES.NO_COFFEE, dtNow, temp, expiration_ts_30_days);

    /* If no prev temp then return, need at least one value to make decision */
    if(!prevTemp)
        return;

    let tempDifFromPrev = (temp - prevTemp.Value);

    /* First state is special, it looks at the Prev value and then starts the StateMachine */
    if( tempDifFromPrev > 5
        && (currentState.StateValue === DynmaoDB_State.STATES.NO_COFFEE || currentState.StateValue === DynmaoDB_State.STATES.GETTING_COLD))
    {
        /* NEW COFFEE */
        console.log("** NEW COFFEE");
        let pNotify = SendSNSNotification("Coffee: ☕ NEW, temp: " + temp + " °C");
        let pNewState = db_State.Insert(DynmaoDB_State.STATES.NEW_COFFEE, dtNow, temp, expiration_ts_30_days);

        await pNotify;
        await pNewState;
    }
    else if( (tempDifFromPrev < 0)  /* Only if getting colder then check this */
        && temp > minTemp && temp <= maxTemp
        && currentState.StateValue === DynmaoDB_State.STATES.NEW_COFFEE)
    {
        /* DRINK IT NOW, GOOD COFFEE */
        console.log("** DRINK IT NOW, GOOD COFFEE");
        let pNotify = SendSNSNotification("Coffee: 👍 GOOD, temp: " + temp + " °C");
        let pNewState = db_State.Insert(DynmaoDB_State.STATES.GOOD_TEMP, dtNow, temp, expiration_ts_30_days);

        await pNotify;
        await pNewState;
    }
    else if(temp <= minTemp
        && currentState.StateValue === DynmaoDB_State.STATES.GOOD_TEMP)
    {
        /* GETTING COLD FAST, DRINK */
        console.log("** GETTING COLD FAST, DRINK");
        let pNotify = SendSNSNotification("Coffee: 👎 COLD, temp: " + temp + " °C");
        let pNewState = db_State.Insert(DynmaoDB_State.STATES.GETTING_COLD, dtNow, temp, expiration_ts_30_days);

        await pNotify;
        await pNewState;
    }
} 
```

# λ码-API&SNS 电报

完整的代码可以在[这里](https://github.com/rehanvdm/CoffeeMonitor/tree/master/lambdas/telegram-bot)找到

与第一个 Lambda 非常相似，我们有 try-catch-finally，然后我们根据传入的数据决定采取什么行动。如果是来自 API 和/bot_callback 路径，那么我们执行 *ReceiveNotification* 函数，如果是其他路径，那么我们只需回复一个 HTTP 状态代码 200。否则，如果它来自主题为 *SendNotification* 的 SNS 主题，我们将执行 *SendNotification* 功能。

```
module.exports.handler = async (event, context) =>
{
    console.log(JSON.stringify(event));
    let response = null;

    try
    {
        if(event.resource === '/{proxy+}' && event.path === '/bot_callback') /* If receiving HTTP Request from Telegram callback */
            response = await ReceiveNotification(event);
        else if(event.resource === '/{proxy+}')  /* If receiving Any other HTTP Requests, just reply with default 200 */
            response =  { statusCode: 200, body: 'Thank you' };
        else if(event.Records && event.Records[0].Sns && event.Records[0].Sns.Subject === "SendNotification" )  /* If came from SNS topic */
            response = await SendNotification(event.Records[0].Sns.Message);

        return response;
    }
    catch (err)
    {
        console.error(err);
        response = false;
    }
    finally
    {
        console.log("* Response: " + response)
        return response;
    }
}; 
```

ReceiveNotification 有点有趣，因为这是我们如何对机器人的“智能”进行编程的。如果我们发送文字:**聊天 id？**对于我们的机器人，它将响应当前的聊天 id，这是我们稍后在部署步骤中需要的。我们还有另一个动作，如果我们输入 **Ping** ，它会用 Pong 回应，其他的它都不理解。我们总是向电报 API 返回 200。

```
async function ReceiveNotification(event)
{
    console.log(JSON.parse(event.body));

    let message = JSON.parse(event.body);

    let bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN);
    switch (message.message.text.toLowerCase())
    {
        case "chat id?":
            await bot.Send("Your chat id is: "+message.message.from.id, message.message.from.id);
            break;

        case "ping":
            await bot.Send("Pong", process.env.TELEGRAM_BOT_CHAT_ID); //message.message.from.id);
            break;

        default:
            await bot.Send("Sorry, I don't understand that", process.env.TELEGRAM_BOT_CHAT_ID); //message.message.from.id);
            break;
    }

    return {
        statusCode: 200,
        body: 'Thank you'
    };
} 
```

最后是 *SendNotification* 函数，它简单地获取 SNS 消息，并按原样发送给由我们的 bot 令牌和聊天 id 标识的我们。

```
async function SendNotification(event)
{
    let body = JSON.parse(event);
    let bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN);
    await bot.Send(body.message, process.env.TELEGRAM_BOT_CHAT_ID);
    return true;
} 
```

# 数据存储

数据存储在一个重载的 DynamoDB 表中。在*分区键*中，我们存储我们正在存储的记录类型的名称，然后*排序键*被用作该记录的标识符。看下面的例子，每次我们写温度事件，我们写 2 个记录，一个是那个日期时间，然后我们也放入/更新当前状态。

[![dynamodb table](img/0715af9cd340f17a26275c3ec9c0ae84.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AQi843DC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rehanvdm.com/contents/data/2019/04/post5_dynamo_1.png)

这样，我们就有了所有温度变化的历史记录，并且我们可以在不知道记录的最后日期时间值的情况下对当前温度记录进行单次查找。这使我们不必做过于复杂的应用程序逻辑或昂贵的表扫描。

注意，相同的模式用于状态转换，历史记录和当前记录都被写入。下面是运行模拟咖啡的集成测试后，表中的状态看起来是什么样子。

[![dynamodb table 2](img/277f0098d5f584cedd0760823164e16e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OmjB3-FI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rehanvdm.com/contents/data/2019/04/post5_dynamo_2.png)

[![dynamodb table 3](img/8e684d1738f345b55bb919dfd2b49e05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cbVWu1zU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rehanvdm.com/contents/data/2019/04/post5_telegram_4.png) 
**手机上收到的电报推送通知。**

# 正在部署

为了打包和部署应用程序，我们将使用前面提到的 SAM cli。重复同样长的命令行是乏味的，所以由于这是一个节点项目，我们可以通过使用 npm 脚本使我们的生活更轻松。这些在 package.json 中定义如下:

[![scripts](img/959a991185b4531966cb8619fa93668c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--czYewREa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rehanvdm.com/contents/data/2019/04/post5_deploy_1.png)

如果我们 cd 到项目根目录并运行命令: **npm run sam_deploy** 它将使用 sam 命令执行上面的 3 个脚本，以构建、打包和部署应用程序到 CloudFront 作为一个堆栈。蓝色部分只是我们传递给 SAM 模板中参数部分的环境变量。如果您正在跟进，这将是您在部署堆栈之前插入 bot 令牌和聊天 id 的地方。

要成功部署，请按以下顺序遵循说明:

*   使用电报机器人父亲获得一个新的机器人和机器人令牌
*   在上面的 package.json 文件中，只在传递给我们的堆栈的环境变量中填充 *TelegramBotToken* 值。
*   然后，当栈被部署时，转到 AWS 控制台，云形成，点击栈并查看输出。将会有一个名为 *RegisterTelegramBot* 的输出，单击它，它将注册我们的新 API 端点，以接收发送给我们的 Bot 的所有消息。然后 API 会将这些消息转发给我们的 Lambda。
*   要找到聊天 id，向机器人发送以下消息:**聊天 id？**它会回复你的聊天 id。
*   再次打开 package.json 文件，并在环境变量中填充 *TelegramBotChatId* 。重新部署堆栈，然后就完成了。接收实时温度值的 Lambda 函数现在可以成功地向您的机器人和聊天发送消息。

[![cf outputs](img/44cec349cb4f1c04dc57960d46028dcf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zL0l05JV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.rehanvdm.com/contents/data/2019/04/post5_deploy_2.png)

# 测试

编写了一些基本的测试来模拟触发我们的 Lambda 函数的事件，它们可以在这里[查看](https://github.com/rehanvdm/CoffeeMonitor/tree/master/lambdas/tests)。一个有趣的例子是，我们做了一个集成测试来测试一系列温度值，因为它们被发送到我们的咖啡监控器 Lambda。下面是测试的一个片段

# 结论

这是一个由 AWS、serverless 和 IaC 支持的有趣的小周末项目(虽然写这篇文章可能比这个项目花的时间要长)。现在我只需要更加关注收到的通知(没有解决办法),希望我不再需要冷咖啡🙂

*这是构建 AWS 物联网咖啡监控器的第 2 部分，共 2 部分。第一部分可以在这里找到*