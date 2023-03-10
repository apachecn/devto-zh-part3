# 用户帐户链接在 Alexa 与 ASP.NET 网络应用编程接口

> 原文：<https://dev.to/oktadev/user-account-linking-in-alexa-with-asp-net-web-api-2bhe>

REST APIs 通常是为企业级公司编写的，作为一种允许客户端应用程序访问核心业务逻辑的方式。对于许多公司来说，简单地通过添加一个用于 Alexa 集成的端点来扩展他们现有的 API 是管理该流程的最佳方式。对于这个例子，你将使用 ASP.NET 4.7 上现有的 Web API 项目，并演示如何在现有用户第一次通过 [Okta](https://developer.okta.com/) 访问 Alexa 技能时将他们链接起来。Web API 项目已经开始了吗？太好了！如果没有，在 Visual Studio 中创建一个新项目，目标是 ASP.NET Web API 4.7，让我们开始吧！

[![Alexa + C# + Okta Bracelet](img/64372dcbee8b98cb3c3229b1e2bcc923.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZV-reiul--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/46746f899b5ba413267a36d8b71be3710884d64b/9e58a/assets-jekyll/blog/alexa-aspnet-linking/alexabracelet-4e4711db8be996d48726aa3a456db02af09859106537eb7a6edd0f9fc547134f.png)

## 为你的账户创建一个 Alexa 技能链接演示

你需要一个亚马逊开发者账户来设置你的技能。创建好你的账户并登录后，导航到[https://developer.amazon.com/alexa/console/ask](https://developer.amazon.com/alexa/console/ask)并点击**创建技能**。对于这个例子，称之为“我的公司技能”保留默认语言。确保选择了**自定义**技能类型，并将托管技能后端资源的方法设置为**提供您自己的**。点击**创造技能**。

[![New Skill](img/b6d668e2fb2779a68c3f7d1029fe95b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g5M85Krb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/f9a41aa897cd3762802ae631d7d398ff433b1a6c/f12de/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_newskill-5ffaedea21521af43ba279dfc97d56ee1f06ac14ac82b8f8e8dd92c732210a88.png)

现在选择**从头开始**模板并点击**选择**。你应该登陆到你新创建的 Alexa 技能的仪表盘上！

[![Skill dashboard](img/05cfb2b1f1c3b96f45c004d153fcc2eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HaC6qFG0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/fd014d4d63296187e5f387a4e52735d845daa497/58b04/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_skilldashboard-a92de5ef2490b913d8b20ee050c863f8ce31102397f833a74eb49621a408e967.png)

点击**调用**，设置短语为“我的公司技能”，点击**保存模型**。这就是亚马逊如何知道如何将说这句话的用户与你的特定技能联系起来。

现在检查**意图**菜单。列出的内置方法很有帮助，但是我们需要为我们的服务创建一个示例意图(用户发起的问题或命令)来处理。点击该部分对面的 **(+)添加**按钮。来做一个简单的。称之为“hello int”并点击**创建自定义意图**。在下面的截图中填写用户可能会说的示例话语，以映射这一点。点击**保存模型**。

[![Hello intent](img/a3c1477320a187625366e20cd0706596.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xXaO7fbm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/61126a7ba683cd9aaeacb6f0d28ce1f5b82686f0/044d3/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_hellointent-f0180df8c75283c9cee0d2968eb298d5cd65b9d70a22027754f308469ce9c6a4.png)

太好了！现在再添加一个自定义意图，并将其命名为“FavoriteBandIntent”像下面的截图一样填写示例短语。点击**保存模型**。

[![Favofite band intent](img/002f33ff5145897abf450cca088b401a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L6OOaybV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/1fb290923f11af918b51b6f4a7ada7ce8de5ee2e/881e9/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_favoritebandintent-aa0d70632b821ec8bc36c12bb6efbee103e461b1979fb8da9c7e59e42fc1254b.png)

在本教程中，我们将使用 web 服务托管自定义技能。为了做到这一点，我们需要告诉 Alexa 向哪里发送请求。导航到**终点**并选择 **HTTPS** 。这是您放置 Alexa 技能服务端点的地方。接下来我们将构建它——但是现在，让我们设置想要的 URL。将您的根 API URL 添加到**默认区域**，并在末尾添加“/api/alexa/mycompanyskill”。

[![Endpoint URL](img/a26daa68a6644aef5841840df370b95c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3_wWc8GK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/0a779bcd23bbe27d1a0f24db800187126bcca6f8/62a8c/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_endpointurl-07f4b41cc7949d17e508d18e97f53c5d390a317364713efd8eb700a21b142e38.png)

对于这个例子，我之前已经将 Web API 项目发布到 Azure，作为 Azure 应用服务。如果您已经执行了相同的操作，则选择具有通配符证书的域的 SSL 证书类型**子域，然后单击**保存端点**。**

最后，返回到**调用**并点击**构建模型**。请确保等待，直到您在右侧获得成功的构建消息。现在我们的服务接入了 Alexa！

## 使用 Alexa 端点扩展您的 Web API 项目

有一些非官方的 Alexa 软件包。NET，但是它们都依赖于这两者。NET 标准，都是针对。NET 核心，或者有额外的第三方依赖，我觉得没有必要。由于这只是通过 POST 方法发送的 JSON 数据，我们可以通过为 Alexa 请求/响应模型添加几个 C#类来轻松处理。您可以在亚马逊的站点上找到请求/响应对象的参考文档。

在 Visual Studio 中打开现有的 Web API 项目。右键点击 **Models** 文件夹，添加一个名为“AlexaRequest.cs”的新 C#类，然后点击 **Add** 。在 Usings 部分替换以下代码:

```
using System;
using System.Collections.Generic;
using Newtonsoft.Json; 
```

接下来，在您的**模型**名称空间中，用下面的代码替换空的 **AlexaRequest** 类:

```
[JsonObject]
public class AlexaRequest
{
    [JsonProperty("version")]
    public string Version { get; set; }

    [JsonProperty("session")]
    public SessionAttributes Session { get; set; }

    [JsonProperty("request")]
    public RequestAttributes Request { get; set; }

    [JsonObject("attributes")]
    public class SessionCustomAttributes
    {
        [JsonProperty("memberId")]
        public int MemberId { get; set; }

        [JsonProperty("lastIntentName")]
        public string LastIntentName { get; set; }
    }

    [JsonObject("session")]
    public class SessionAttributes
    {
        [JsonProperty("sessionId")]
        public string SessionId { get; set; }

        [JsonProperty("application")]
        public ApplicationAttributes Application { get; set; }

        [JsonProperty("attributes")]
        public SessionCustomAttributes Attributes { get; set; }

        [JsonProperty("user")]
        public UserAttributes User { get; set; }

        [JsonProperty("new")]
        public bool New { get; set; }

        [JsonObject("application")]
        public class ApplicationAttributes
        {
            [JsonProperty("applicationId")]
            public string ApplicationId { get; set; }
        }

        [JsonObject("user")]
        public class UserAttributes
        {
            [JsonProperty("userId")]
            public string UserId { get; set; }

            [JsonProperty("accessToken")]
            public string AccessToken { get; set; }
        }
    }

    [JsonObject("request")]
    public class RequestAttributes
    {
        private string _timestampEpoch;
        private double _timestamp;

        [JsonProperty("type")]
        public string Type { get; set; }

        [JsonProperty("requestId")]
        public string RequestId { get; set; }

        [JsonProperty("timestamp")]
        public string TimestampEpoch
        {
            get
            {
                return _timestampEpoch;
            }
            set
            {
                _timestampEpoch = value;

                if (Double.TryParse(value, out _timestamp) && _timestamp > 0)
                    Timestamp = new DateTime(1970, 1, 1, 0, 0, 0, DateTimeKind.Utc).AddMilliseconds(_timestamp);
                else
                {
                    var timeStamp = DateTime.MinValue;
                    if (DateTime.TryParse(_timestampEpoch, out timeStamp))
                        Timestamp = timeStamp.ToUniversalTime();
                }
            }
        }

        [JsonIgnore]
        public DateTime Timestamp { get; set; }

        [JsonProperty("intent")]
        public IntentAttributes Intent { get; set; }

        [JsonProperty("reason")]
        public string Reason { get; set; }

        public RequestAttributes()
        {
            Intent = new IntentAttributes();
        }

        [JsonObject("intent")]
        public class IntentAttributes
        {
            [JsonProperty("name")]
            public string Name { get; set; }

            [JsonProperty("slots")]
            public dynamic Slots { get; set; }

            public List<KeyValuePair<string, string>> GetSlots()
            {
                var output = new List<KeyValuePair<string, string>>();
                if (Slots == null) return output;

                foreach (var slot in Slots.Children())
                {
                    if (slot.First.value != null)
                        output.Add(new KeyValuePair<string, string>(slot.First.name.ToString(), slot.First.value.ToString()));
                }

                return output;
            }
        }
    }
} 
```

太好了！在 **Models** 文件夹中添加另一个名为“AlexaResponse.cs”的 C#类，并用以下代码替换 usings 部分:

```
using Newtonsoft.Json; 
```

在**模型**名称空间中，用下面的代码替换空的 AlexaResponse 类。现在我们有了一些可以一起工作的对象(POCOs)了:( T2 )!

```
[JsonObject]
public class AlexaResponse
{
    [JsonProperty("version")]
    public string Version { get; set; }

    [JsonProperty("sessionAttributes")]
    public SessionAttributes Session { get; set; }

    [JsonProperty("response")]
    public ResponseAttributes Response { get; set; }

    public AlexaResponse()
    {
        Version = "1.0";
        Session = new SessionAttributes();
        Response = new ResponseAttributes();
    }

    public AlexaResponse(string outputSpeechText)
        : this()
    {
        Response.OutputSpeech.Text = outputSpeechText;
        Response.Card.Content = outputSpeechText;
    }

    public AlexaResponse(string outputSpeechText, bool shouldEndSession)
        : this()
    {
        Response.OutputSpeech.Text = outputSpeechText;
        Response.ShouldEndSession = shouldEndSession;

        if (shouldEndSession)
        {
            Response.Card.Content = outputSpeechText;
        }
        else
        {
            Response.Card = null;
        }
    }

    public AlexaResponse(string outputSpeechText, string cardContent)
        : this()
    {
        Response.OutputSpeech.Text = outputSpeechText;
        Response.Card.Content = cardContent;
    }

    [JsonObject("sessionAttributes")]
    public class SessionAttributes
    {
        [JsonProperty("memberId")]
        public int MemberId { get; set; }

        [JsonProperty("lastIntentName")]
        public string LastIntentName { get; set; }
    }

    [JsonObject("response")]
    public class ResponseAttributes
    {
        [JsonProperty("shouldEndSession")]
        public bool ShouldEndSession { get; set; }

        [JsonProperty("outputSpeech")]
        public OutputSpeechAttributes OutputSpeech { get; set; }

        [JsonProperty("card")]
        public CardAttributes Card { get; set; }

        [JsonProperty("reprompt")]
        public RepromptAttributes Reprompt { get; set; }

        public ResponseAttributes()
        {
            ShouldEndSession = true;
            OutputSpeech = new OutputSpeechAttributes();
            Card = new CardAttributes();
            Reprompt = new RepromptAttributes();
        }

        [JsonObject("outputSpeech")]
        public class OutputSpeechAttributes
        {
            [JsonProperty("type")]
            public string Type { get; set; }

            [JsonProperty("text")]
            public string Text { get; set; }

            [JsonProperty("ssml")]
            public string Ssml { get; set; }

            public OutputSpeechAttributes()
            {
                Type = "PlainText";
            }
        }

        [JsonObject("card")]
        public class CardAttributes
        {
            [JsonProperty("type")]
            public string Type { get; set; }

            [JsonProperty("title")]
            public string Title { get; set; }

            [JsonProperty("content")]
            public string Content { get; set; }

            public CardAttributes()
            {
                Type = "Simple";
            }
        }

        [JsonObject("reprompt")]
        public class RepromptAttributes
        {
            [JsonProperty("outputSpeech")]
            public OutputSpeechAttributes OutputSpeech { get; set; }

            public RepromptAttributes()
            {
                OutputSpeech = new OutputSpeechAttributes();
            }
        }
    }
} 
```

是时候为我们的 Alexa 端点添加控制器了。右击**控制器**并选择**添加**，然后选择**控制器**。选择 **Web API 2 控制器-清空**并点击**添加**。将控制器名称设置为“AlexaController”并点击**添加**。

现在在你的 AlexaController 类之上为控制器添加一个 route prefix:

```
[RoutePrefix("api/alexa")]
public class AlexaController : ApiController 
```

现在将下面的代码复制并粘贴到类中。请注意，在检查了 Alexa 请求对象之后，有一些私有方法可以处理每个意图:

```
[HttpPost, Route("mycompanyskill")]
public AlexaResponse MyCompanySkill(AlexaRequest request)
{
    AlexaResponse response = null;

    switch (request.Request.Intent.Name)
    {
        case "HelloIntent":
            response = HelloIntentHandler();
            break;
        case "FavoriteBandIntent":
            response = FavoriteBandIntentHandler();
            break;
    }

    return response;
}

private AlexaResponse FavoriteBandIntentHandler()
{
    var favoriteBand = "unknown.";

    var response = new AlexaResponse("Your favorite band is " + favoriteBand + ".");
    response.Response.ShouldEndSession = true;
    return response;
}

private AlexaResponse HelloIntentHandler()
{
    var response = new AlexaResponse("Hello from My Company.");
    response.Response.Reprompt.OutputSpeech.Text =
        "You can tell me to say hello, what is my favorite band, or cancel to exit.";
    response.Response.ShouldEndSession = false;
    return response;
} 
```

确保在 Usings 部分添加对您的 **Models** 文件夹的引用，以便解析 **AlexaRequest** 和 **AlexaResponse** 类对象。重新生成项目。

## 部署 Web API 服务，测试你的 Alexa 技能

现在你必须测试这个 API，确保它能和 Alexa 一起工作。将 Web API 项目部署或重新发布到托管它的任何地方。一旦您的服务部署成功，请返回 Alexa 仪表盘，查看“我的公司技能”并选择**测试**。该技能的测试将被禁用。要启用它，只需从下拉菜单中选择**开发**。

要测试这项技能，请在语言旁边的文本框中键入“告诉我的公司技能你好”,然后按回车键。您应该在 JSON 请求旁边看到来自您的服务的 JSON 响应，以及可以听到的声音片段回放。如果你有一个亚马逊 Echo，你可以说“Alexa，告诉我的公司 skill hello ”,只要设备登录到你的开发者帐户，就会得到相同的音频响应。

[![Test hello intent](img/fbe39f12a43c811381f5b1bf3a3f3dd2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--he7ON4wn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/c5630bc87d2003210ae9d07687117a64ab290edf/6e725/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_testhello-0d4d189708793ae248ebc3f5dd5030e073a228498f11c92533de57d2e12d510c.png)

恭喜你。您已经获得了一项与现有 Web API 项目相关联的工作语音技能。

## 添加安全的 Alexa 账户链接和用户管理

你得到了基本的工作技能，但是目前，任何亚马逊用户都可以访问它。为了保护技能并知道谁在使用它，Alexa 提供了一个名为**帐户链接**的初始认证过程，该过程控制技能的启用。Alexa 技能工具包中的帐户链接使用 OAuth 2.0——所以我们将利用 Okta 通过 JSON Web Token (JWT)范围和声明来管理登录和用户数据。为什么要用 Okta？所有的 OAuth 协商都是在 Okta 和 Amazon 之间完全配置的，所以您不必担心访问令牌是如何创建的——当它到达您的 API 时，您只需使用它，使一个原本繁琐的任务变得简单。

要做到这一点，前往[https://developer.okta.com/](https://developer.okta.com/)并创建一个 [Okta 组织](https://developer.okta.com/signup)，如果你还没有的话。使用您的 Okta 超级管理员帐户登录。点击**应用**，然后**添加应用**。选择**网**并点击**下一步**。

> 这将创建一个代表你的自定义 Alexa 技能的应用程序。即使你有一个 REST API 而不是一个网站，Alexa 的登录过程将通过 Alexa 移动应用程序进行，因此将通过 OpenID Connect 显示一个物理登录页面。好消息是——Okta 为您管理了这一点，您不必构建它！

您将看到一个配置 Okta OpenID Connect 应用程序的屏幕。将应用程序名称更改为“我的公司 Alexa Skill”对于**登录重定向 URIs** *，从技能页面上的 Alexa 应用程序中获取，向下滚动并点击链接导航栏的**账户。切换开关为**你允许用户创建一个账户或链接到你现有的账户吗？****

[![Account linking toggle](img/1a30daa12250639385ed8f20e8610ee2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---PDm4SQO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/3038ddf62c72a6777209403f1f7dca47014fe799/c9cd2/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_accountlinkingtoggle-b7b68a4b2947bd7ffea396e6a4e78838e45d976f14f5b8e3e13b729651ee1c2f.png)

向下滚动。你会在底部找到这个技能的重定向网址。将这三个条目分别添加到你的 Okta 申请中。

[![Okta web app](img/1428ca7c810810bcc26d9a770862d683.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DNxWwFK5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/288998766051b9d4841ffd38970207126ff18e2f/170c7/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_oktaalexaweb-d8144dccd91640c82ef30b5915a2725d6fbef1261ee36d11fe6424e5b903d9e2.png)

Okta 注册的应用程序必须使用 **offline_access** 作用域明确请求刷新令牌。即使请求 **offline_access** ，Okta 也不会返回刷新令牌——除非**刷新令牌**被启用为允许的授权类型，所以请确保选中该框。

[![Refresh token](img/d203a720d803eadd7a3a2d4b18399a29.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cm8Dq0Zn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/e1dfee7b14f501f1dc40ac6ac925f84ff41ac4f2/bbfc8/assets-jekyll/blog/alexa-aspnet-linking/alexa-dotnet-refreshtoken-c67a71f3e255aea5f43db28d5c61e4e39b27b493411ef18ebbe32875df52502f.png)

完成后点击**完成**。记下客户端 ID 和密码，稍后您将在帐户链接页面上使用它。

在 Okta 中为您技能的帐户链接功能创建自定义授权服务器。导航至: **API** > **授权服务器**。选择**添加授权服务器**。在**名称**字段中输入“AlexaSkillAuthServer”。将[放在 http://my company . com](http://mycompany.com%E2%80%9D)的**受众**栏中。描述后点击**保存**。

[![Auth server](img/4232fc9dc5dfc3db49483afd7e1bbbd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J3YaKRZb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/efe8dcee5577a5bb693c27255fbdfa46e1129df3/a057a/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_authserver-85a926ea0b882d05ae0dd37c8b05bae4363ffd8075bb36920020cb22227a3153.png)

现在创建一些自定义声明，这些声明将代表我们最喜欢的乐队和名字。点击**索赔**标签。点击**添加索赔**。为用户最喜欢的乐队添加一个:

[![Favorite band claim](img/76deadd9375db0786ab560650e0ce4d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ietfJYoQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/eb4e0161aefe229a66dd4abc1c9c35ebd1fe7145/10021/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_addclaimband-4177c03579fdb078e47af3daaa20b1bbe53804e62ce438ad6268c7c2c9056216.png)

> 很重要的一点是，您已经配置了**名字**和**收藏夹**声明，这些声明将始终包含在令牌请求中，并在 Okta 门户的任何范围内使用。

为用户的名字添加另一个自定义声明。将其命名为“firstName”，并将值设置为“user.firstName”。点击**访问策略**选项卡，点击**添加新的访问策略**。点击**添加规则**。取消选中除授权代码之外的所有授权类型。如果愿意，您可以调整令牌的寿命。保留其他默认设置，点击**保存**。

现在创建一个自定义用户配置文件属性来存储用户最喜欢的乐队。选择**用户** > **个人资料编辑**。单击 Okta 徽标右侧的铅笔图标。

添加一个名为 favoriteBand 的新属性。点击**添加属性**，为**显示名称**和**变量名**输入“favoriteBand”。填写描述，点击**保存**。

[![Favorite band attribute](img/4dcc811836fc752bcbf64ac66208512a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--trrlSGcq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/040bca874b4ea7913385481193f84954af20aaa1/5e179/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_bandattribute-f48f739cab46d9238b14ae233804668d8ded48c1f5a84520f49a143aeb5ae130.png)

因为用户的名字已经是默认配置文件的一部分，所以您不必在这里再次添加该属性。你现在已经准备好了 Okta！

## 为 Alexa 用户设置账户链接

返回到亚马逊门户中技能的帐户链接页面。一旦你在那个页面上，填写来自 Okta 的细节。需要的信息包括要使用的授权类型、Okta 授权服务器和令牌 URL、您之前创建的 Okta Alexa 应用程序的客户端 ID、允许的域和默认令牌过期时间。

要获得您的**授权 URI** ，请转到您的 Okta 定制授权服务器页面，您应该能够看到元数据 URI。然后追加“/授权。”**访问令牌 URI** 将是附加了“/token”的相同 URL。将**客户端认证方案**设置为“请求体中的凭证”

[![Account linking overview](img/1947dc595709f7cdaba9ca342f4896f4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SUc0R649--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/8957b80c760adeedacb4884c4af8dc4241d29f31/cabda/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_accountlinking_scope-d6419538ea87fbcdb7780b528ccbd3169cbc8a58e5bc25720e4030e8fb87d4c1.png)

对于**范围**，添加“openid”、“profile”和“offline_access”很重要——这将允许后续的刷新令牌与 Alexa 以及您添加的自定义用户数据字段一起工作。

## 解码 JSON Web Token 获得用户声明数据

好东西的时候到了，橡胶和路面相遇的地方！安装以下 NuGet 包:

```
System.IdentityModel.Tokens.Jwt 
```

将 Usings 一节替换为以下参考内容:

```
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Web.Http;
using BaseAlexaSkill.Models; 
```

打开您的 AlexaController 并转到**FavoriteBandIntentHandler**。对 Okta 创建的访问令牌进行解码，并设置在 AlexaRequest 的 access token 属性中(去掉了“载体”部分)——用下面的代码替换该方法:

```
private AlexaResponse FavoriteBandIntentHandler(AlexaRequest request)
{
    var jwtEncodedString = request.Session.User.AccessToken;

    if (request.Session.User.AccessToken != null)
    {
        //Decode name and favorite band from the access token
        var token = new JwtSecurityToken(jwtEncodedString: jwtEncodedString);
        var bandName = token.Claims.First(c => c.Type == "favoriteBand").Value;
        var name = token.Claims.First(c => c.Type == "firstName").Value;

        var response = new AlexaResponse(
            "Hello " + name + ", your favorite band is " + bandName + ".");
        response.Response.Card.Title = name + "'s Favorite Band";
        response.Response.Card.Content = bandName;
        response.Response.ShouldEndSession = true;

        return response;
    }
    else
    {
        var response = new AlexaResponse("You are not currently linked to this skill. Please go into your Alexa app and sign in.");
        response.Response.Card.Type = "LinkAccount";
        response.Response.ShouldEndSession = true;

        return response;
    }
} 
```

注意 else 语句——如果访问代码过期或为空，发送一个卡类型为 **LinkAccount** 的响应会让 Alexa 知道在登录过程中帮助用户，并将他们重定向到启用或禁用技能，然后重新启用技能，并将他们重定向到 Okta 的登录屏幕。最后，重新部署您的服务。

> 您可以使用您的公司徽标和颜色自定义 Okta 托管的登录页面，这样用户就不必看到 Okta 是提供商。有关登录小工具定制的更多信息，请查看[文档](https://help.okta.com/en/prod/Content/Topics/Settings/custom-okta-hosted-sign-in-page.htm)。

## 测试你的安全账号链接 Alexa 技能

快好了！在 Okta 中创建一个示例用户，其电子邮件地址不同于您的超级管理员电子邮件地址，以便模拟现有用户。虽然你可以使用你的超级管理员登录来测试，我总是认为建立一个 QA 测试帐户是一个更好的主意。

进入**用户** > **人物**，点击**添加人物**。填写详细信息，点击**保存**。

[![Add user](img/a984ff577fca7f1a725d53b5a99f1d16.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJ6d1dZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/1c12f25fb0c518f9abf765928089207f48d30666/ea494/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_adduser-4ed7df8e85f26ae3d014fe63c8eb5e9bae64a31a3706faf5cda6f097f93ad1cc.png)

现在返回并编辑该配置文件，在 **favoriteBand** 字段中添加乐队的名称。对于这个例子，我用“伊凡塞斯”，但任何乐队名称都可以。点击**保存**。

检查电子邮件以激活新的 Okta 用户帐户，按照步骤设置密码。现在你准备邀请这个测试用户测试你的 Alexa 技能。创建一个你可以通过电子邮件访问的测试亚马逊账户。对于这个例子，我的测试 Amazon 用户碰巧拥有与我们登录 Okta 时使用的相同的电子邮件——但是它可能不同。这就是帐户链接的用武之地。

回到你的 Alexa 技能页面，点击**分配**，填写所有必填项。隐私政策 URL 没有被标记为必需的，但为了发布你的技能，亚马逊 Alexa 需要一些 URL 信息放在这里-所以请确保你在隐私政策 URL 字段中提供了有效的 URL。点击**保存并继续**。完成隐私与合规页面中所需的信息。完成后，点击**保存并继续**。

在可用性页面下，您将能够添加哪些用户可以测试您刚刚开发的新创建的 Alexa 技能。添加您的测试用户的亚马逊电子邮件帐户，亚马逊将发送一封电子邮件，允许他们激活其亚马逊用户帐户上的技能。请注意，在你的技能可以在 Alexa 技能商店公开之前，亚马逊有一个验证过程，这只是测试版测试。你应该会收到一封来自亚马逊的关于你的 Alexa 技能邀请的邮件。点击链接，这样它可以直接带你到 Alexa 技能商店页面，指向你新创建的自定义技能。

[![Alexa beta test email](img/88af530f19bf4fad884e25a523bf0ee8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tBx6csmA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/29b559a37818f0a4bcf97a2ca54b855801e1d628/73151/assets-jekyll/blog/alexa-aspnet-linking/alexa_dotnet_betatestemail-89c6f2fcda1cf896490877522756c7ca480d1bc677a16db94f8d46dbc6f3a6b9.png)

现在，用您的测试用户 Okta 凭证登录！如果您已经通过认证，您将被带到 Alexa 最终用户技能页面。点击**我接受**。点击右上角的**设置**。点击**链接账号**。如果您还没有通过 Okta 认证，那么您将被提示使用您的 Okta 凭证登录。一旦您的认证成功，您应该会看到一个页面，显示您的用户的 Okta 帐户现在与他们的 Amazon one 相链接。

如果您正在使用移动应用程序，您的技能页面现在应该如下所示:

为了测试这一技能，要么在测试用户的亚马逊账户上登录一个 Echo 设备，要么将 Alexa 应用程序下载到你的手机上并在那里使用。你也应该能够用门户测试模拟器测试你的 Amazon 开发者帐户——只要你有一个链接到 Okta 管理员用户的帐户。就说:

“Alexa，问我公司技能，我最喜欢的乐队是什么？”。

如果你通过点击 Alexa 图标在移动应用程序上问了这个问题，这是你应该在屏幕上看到的摘要(除了听到音频响应之外):

[![](img/95cf566750bb11919d5481fccccc2b4d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EgiqLRIz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d33wubrfki0l68.cloudfront.net/9dfddd99b11fd120bb9d4d335d30e82ced816e98/ae028/assets-jekyll/blog/alexa-aspnet-linking/alexa-dotnet-testmobile-0072dfda307137ab8aa1b1ee25918409b3ac9a4e88cda56f58a681a6da662363.jpg)

厉害！你已经成功地扩展了一个现有的 ASP.NET API 来支持一个安全的 Alexa 自定义技能。我们将有更多关于 Alexa 技能的文章，所以如果你已经尝试过**Alexa**+**Okta**+**c#**，请在下面评论！

## 了解有关 ASP.NET 和安全用户管理的更多信息

如果您想了解更多关于在 ASP.NET 使用安全 OAuth 和用户管理的信息，我们还发布了一些您可能会感兴趣的帖子:

*   如何保护你的？带有令牌认证的. NET Web API
*   [用 ASP.NET 框架 4.x Web API 和 Angular 构建 CRUD App](https://developer.okta.com/blog/2018/07/27/build-crud-app-in-aspnet-framework-webapi-and-angular)
*   [使用 OpenID Connect 和 Okta 保护您的 ASP.NET 网络表单应用](https://developer.okta.com/blog/2018/08/29/secure-webforms-with-openidconnect-okta)
*   [在您的 ASP.NET MVC 框架 4.x 应用中使用 OpenID Connect 进行授权](https://developer.okta.com/blog/2018/04/18/authorization-in-your-aspnet-mvc-4-application)

一如既往，如果你对这篇文章有任何问题或评论，请在下面留下你的评论。有关 Okta 开发团队的其他精彩内容，请关注我们的 [Twitter](https://twitter.com/oktadev) 和[脸书](https://www.facebook.com/oktadevelopers)！