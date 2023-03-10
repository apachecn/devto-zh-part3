# 用 AWS Lambda 和。网络核心

> 原文：<https://dev.to/alexhyettdev/creating-a-contact-form-with-aws-lambda-and-net-core-192b>

我最近把我的网站从标准的 Wordpress 安装切换到用 Gatsby.js 和 React 构建的静态网站。有了 Gatsby.js，你得到了一个速度惊人的网站，但是你确实错过了一些 Wordpress 的标准功能，比如评论和表格。

我一直使用 Disqus 作为我的评论系统，使用 Formspree.io 作为当有人填写我的联系表格时发送电子邮件的简单方式。

在本文中，我将向您展示如何用 AWS Lambda 函数替换 Formspree.io 联系人表单。

## 入门

正如这篇文章的标题所暗示的，我将使用 C#。我的 Lambda 函数的 Net 核心。开始使用亚马逊 Lambda 和。你需要安装 Amazon Lambda 工具和模板。这也将使您能够访问我们稍后将使用的`dotnet lambda`命令，以及许多初学者模板

```
dotnet tool install -g Amazon.Lambda.Tools
dotnet new -i Amazon.Lambda.Templates 
```

Enter fullscreen mode Exit fullscreen mode

安装后，您可以通过输入`dotnet new -all`查看可用的模板。您应该得到一个类似这样的列表:

```
Usage: new [options]

Options:
  -h, --help Displays help for this command.
  -l, --list Lists templates containing the specified name. If no name is specified, lists all templates.
  -n, --name The name for the output being created. If no name is specified, the name of the current directory is used.
  -o, --output Location to place the generated output.
  -i, --install Installs a source or a template pack.
  -u, --uninstall Uninstalls a source or a template pack.
  --nuget-source Specifies a NuGet source to use during install.
  --type Filters templates based on available types. Predefined values are "project", "item" or "other".
  --dry-run Displays a summary of what would happen if the given command line were run if it would result in a template creation.
  --force Forces content to be generated even if it would change existing files.
  -lang, --language Filters templates based on language and specifies the language of the template to create.

Templates Short Name Language Tags
---------------------------------------------------------------------------------------------------------------------------------------------------------
Order Flowers Chatbot Tutorial lambda.OrderFlowersChatbot [C#] AWS/Lambda/Function
Lambda Detect Image Labels lambda.DetectImageLabels [C#], F# AWS/Lambda/Function
Lambda Empty Function lambda.EmptyFunction [C#], F# AWS/Lambda/Function
Lex Book Trip Sample lambda.LexBookTripSample [C#] AWS/Lambda/Function
Lambda Simple DynamoDB Function lambda.DynamoDB [C#], F# AWS/Lambda/Function
Lambda Simple Kinesis Firehose Function lambda.KinesisFirehose [C#] AWS/Lambda/Function
Lambda Simple Kinesis Function lambda.Kinesis [C#], F# AWS/Lambda/Function
Lambda Simple S3 Function lambda.S3 [C#], F# AWS/Lambda/Function
Lambda Simple SQS Function lambda.SQS [C#] AWS/Lambda/Function
Lambda ASP.NET Core Web API serverless.AspNetCoreWebAPI [C#], F# AWS/Lambda/Serverless
Lambda ASP.NET Core Web Application with Razor Pages serverless.AspNetCoreWebApp [C#] AWS/Lambda/Serverless
Serverless Detect Image Labels serverless.DetectImageLabels [C#], F# AWS/Lambda/Serverless
Lambda DynamoDB Blog API serverless.DynamoDBBlogAPI [C#] AWS/Lambda/Serverless
Lambda Empty Serverless serverless.EmptyServerless [C#], F# AWS/Lambda/Serverless
Lambda Giraffe Web App serverless.Giraffe F# AWS/Lambda/Serverless
Serverless Simple S3 Function serverless.S3 [C#], F# AWS/Lambda/Serverless
Step Functions Hello World serverless.StepFunctionsHelloWorld [C#], F# AWS/Lambda/Serverless
Console Application console [C#], F#, VB Common/Console
Class library classlib [C#], F#, VB Common/Library
Unit Test Project mstest [C#], F#, VB Test/MSTest
NUnit 3 Test Project nunit [C#], F#, VB Test/NUnit
NUnit 3 Test Item nunit-test [C#], F#, VB Test/NUnit
xUnit Test Project xunit [C#], F#, VB Test/xUnit
Razor Page page [C#] Web/ASP.NET
MVC ViewImports viewimports [C#] Web/ASP.NET
MVC ViewStart viewstart [C#] Web/ASP.NET
ASP.NET Core Empty web [C#], F# Web/Empty
ASP.NET Core Web App (Model-View-Controller) mvc [C#], F# Web/MVC
ASP.NET Core Web App webapp [C#] Web/MVC/Razor Pages
ASP.NET Core with Angular angular [C#] Web/MVC/SPA
ASP.NET Core with React.js react [C#] Web/MVC/SPA
ASP.NET Core with React.js and Redux reactredux [C#] Web/MVC/SPA
Razor Class Library razorclasslib [C#] Web/Razor/Library/Razor Class Library
ASP.NET Core Web API webapi [C#], F# Web/WebAPI
global.json file globaljson Config
NuGet Config nugetconfig Config
Web Config webconfig Config
Solution File sln Solution

Examples:
    dotnet new mvc --auth Individual
    dotnet new lambda.SQS
    dotnet new --help 
```

Enter fullscreen mode Exit fullscreen mode

当我第一次开始时，我使用空的函数模板`dotnet new lambda.EmptyFunction`来创建新的 lambda 函数。

如果你想要一个已经设置了依赖注入、Serilog 和配置文件的样板模板，那么你可以在 GitHub 上查看我的[项目。](https://github.com/alexhyett/lamda-dotnet-console)

如果你只是想要一个工作联系表，那么你可以查看一下。

## 设置 Lambda 功能

首先，我们将从我的 [lamda-dotnet-console](https://github.com/alexhyett/lamda-dotnet-console) 项目中设置一个简单的 Lambda 函数。

这个函数获取一个字符串并将其大写。如果你正在使用我的 lamda-dotnet-console 项目，你会看到我添加了一个前缀，从 appsettings.json 中读取以确保配置被正确读取。

Lambda 函数被设置为库项目，这意味着要测试它，要么创建另一个使用该库的控制台应用程序，要么使用它附带的单元测试。

首先，我们将部署现有资源。

### 部署到 AWS

我将假设您以前使用过 AWS，并且已经在您的计算机上设置了一个凭据配置文件。

使用我们安装的 Lambda 工具，部署很简单。

```
dotnet lambda deploy-function lambda-dotnet-console 
```

Enter fullscreen mode Exit fullscreen mode

deploy-function 后面的参数是函数的名称。您可以随意命名它，但是您需要更新您在默认文件 aws-lambda-tools-defaults.json 中所写的内容。

在部署过程中，它会要求您设置 IAM 凭据或选择一个您以前创建的凭据。

部署完成后，您可以使用以下命令对其进行测试:

```
dotnet lambda invoke-function lambda-dotnet-console --payload "Hello World" 
```

Enter fullscreen mode Exit fullscreen mode

如果你正在使用我的 GitHub 项目，你应该会看到类似这样的内容:

```
Amazon Lambda Tools for .NET Core applications (3.1.1)
Project Home: https://github.com/aws/aws-extensions-for-dotnet-cli, https://github.com/aws/aws-lambda-dotnet

Payload:
"TestHELLO WORLD"

Log Tail:
START RequestId: b40718bb-fec0-11e8-b682-07112db8d970 Version: $LATEST
END RequestId: b40718bb-fec0-11e8-b682-07112db8d970
REPORT RequestId: b40718bb-fec0-11e8-b682-07112db8d970 Duration: 3523.77 ms Billed Duration: 3600 ms Memory Size: 256 MB Max Memory Used: 67 MB 
```

Enter fullscreen mode Exit fullscreen mode

不要太担心计费持续时间，因为你每月可以获得 100 万次请求和 400，000 GB 秒的免费流量。

### 设置环境变量

为了配置我们的函数，我们将以与 Docker 容器相同的方式使用环境变量。使用环境变量，我们可以覆盖 appsettings.json 中的设置。

我们可以这样做，因为我们已经安装了 NuGet 包`Microsoft.Extensions.Configuration.EnvironmentVariables`。

这在我们的函数中是这样设置的:

```
var configuration = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appsettings.json", optional : false, reloadOnChange : true)
    .AddEnvironmentVariables(prefix: "LAMBDA_")
    .Build();

_appSettings = new AppSettings();
configuration.GetSection("App").Bind(_appSettings); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用以下环境变量覆盖前缀:`LAMBDA_App__Prefix`(注意双下划线)。该项目设置为使用 Serilog，如果你想要一个查看日志的好方法，我推荐 Seq。你可以跟随我的[教程](https://dev.to/app-logs-dotnet-seq-5-serilog/)学习如何在 AWS 上设置这个。

如果要更改环境变量，您需要登录 AWS 控制台。可能有一种方法可以在命令行上实现这一点，但是对于一些奇怪的变化，使用界面来实现更容易。

登录 AWS 后，使用服务搜索来查找 Lambda。然后点击你的 Lambda 函数，向下滚动到环境变量部分。

[![Lambda Environment Variables](img/055a9a09c5d50102d520b5300bb7cb1e.png)](///static/4b91b463ae8b69c95a88961387fb071b/1cb21/lambda-environment-variables.png)

我们将更新前缀，以确保它能正常工作。记得单击 save，然后我们可以像以前一样再次调用我们的函数。您的输出应该如下所示:

```
Amazon Lambda Tools for .NET Core applications (3.1.1)
Project Home: https://github.com/aws/aws-extensions-for-dotnet-cli, https://github.com/aws/aws-lambda-dotnet

Payload:
"LambdaHELLO WORLD"

Log Tail:
START RequestId: 9b30342f-fec5-11e8-b090-d5e24f128b73 Version: $LATEST
END RequestId: 9b30342f-fec5-11e8-b090-d5e24f128b73
REPORT RequestId: 9b30342f-fec5-11e8-b090-d5e24f128b73 Duration: 3520.72 ms Billed Duration: 3600 ms Memory Size: 256 MB Max Memory Used: 67 MB 
```

Enter fullscreen mode Exit fullscreen mode

如果你设置正确，你应该看到`LambdaHELLO WORLD`而不是`TestHELLO WORLD`。

## 创建联系人表单端点

我的联系人表单端点的目标是能够用 lambda 函数替换联系人表单上的操作 URL，这样我就不再需要使用 FormSpree 了。

### 要求

这些是我的新端点的需求。

*   应该接受的领域名称，电子邮件，电话，网站和消息。
*   应该从表单提交中获取字段，例如内容类型为`application/x-www-form-urlencoded`。
*   应该发送电子邮件到我选择的地址。
*   提交时应该重定向到一个感谢页面。
*   应该将消息和错误记录到 Seq。

### 发送邮件。网络核心

我使用 MailKit 和 MimeKit NuGet 包发送电子邮件。这是我正在使用的代码:

```
public async Task SendEmail(string body)
{
    var message = new MimeMessage();
    message.From.Add(new MailboxAddress("Contact Form", _settings.EmailFrom));
    message.To.Add(new MailboxAddress(_settings.EmailTo));
    message.Subject = "Contact Request";
    message.Body = new TextPart(TextFormat.Text)
    {
        Text = body
    };

    using(var client = new SmtpClient())
    {
        client.Connect(_settings.Host, _settings.Port, _settings.Port == 465);
        client.AuthenticationMechanisms.Remove("XOAUTH2");
        await client.AuthenticateAsync(_settings.Username, _settings.Password);
        await client.SendAsync(message);
        await client.DisconnectAsync(true);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经将电子邮件的所有设置放在 appsettings.json 中，因此可以使用环境变量覆盖它们。同样，你可以在 GitHub 上看到一个[工作示例。](https://github.com/alexhyett/lambda-contact-form)

我还更新了 FunctionHandler，以获取一个 ContactRequest 对象并返回一个 JSON 对象，其中包含感谢页面的位置。默认情况下，该函数会将您的对象转换为字符串，因此我们需要添加一个额外的 JSON 序列化程序来获得正确的输出。你的函数应该是这样的:

```
[LambdaSerializer(typeof(Amazon.Lambda.Serialization.Json.JsonSerializer))]
public object FunctionHandler(ContactRequest input, ILambdaContext context)
{
    // Function code here.
    return new { location = _appSettings.ReturnUrl };
} 
```

Enter fullscreen mode Exit fullscreen mode

部署后，您需要为您的特定电子邮件地址和 SMTP 服务器设置更新环境变量。我使用亚马逊 SES 发送我的电子邮件，但是你可以使用任何 SMTP 服务器。

### 设置亚马逊 API 网关

所以现在，你应该有一个 lambda 函数，当在命令行上调用它时，它会给你发送一封电子邮件。你应该可以用这个调用你的函数并得到一封邮件:

```
dotnet lambda invoke-function lambda-contact-form -payload "{ 'name': 'Alex Hyett', 'email': 'hello@alexhyett.com', 'phone': '0123456789', 'website': 'https://www.alexhyett.com', 'body': 'This is a message'}" 
```

Enter fullscreen mode Exit fullscreen mode

然后，您应该得到一个 JSON 有效负载，其中包含您想要重定向到的 URL:

```
Payload:
{"location":"https://www.alexhyett.com/thank-you"} 
```

Enter fullscreen mode Exit fullscreen mode

这一切都很好，但还不是终点，所以我们不能将其用于我们的联系表单。我们将使用 Amazon 的 API 网关来创建端点，该端点将调用我们的 lambda 函数。

我们的 lambda 函数只接受`application/json`作为内容类型。然而，当我们从表单提交中调用它时，我们需要在 API Gateway 中将`application/x-www-form-urlencoded`转换为`application/json`，然后再传递给我们的 Lambda 函数。

最后，我们将让 API Gateway 返回一个 302 重定向到我们选择的页面，这样用户在单击 submit 后会看到一个漂亮的页面。

#### 创建 API

使用 AWS 控制台中的服务搜索找到 API 网关页面。

[![API Gateway](img/d56a55a29210937186452eed5f433a20.png)](///static/c38cfae91d20529565b692b6d8bb8072/a1a4b/api-gateway.png)

单击“开始”后，您就可以创建 API 了。

[![New API](img/0ce6691baa49883403f9f53629aeb88f.png)](///static/53ea03d3b67a0bd112d90f7f56c963f5/b88bf/new-api.png)

创建后，使用“操作”菜单创建一个方法，然后选择“发布”。然后在提供的框中搜索您的 lambda 函数。

[![Method Setup](img/fc85c79c332b01689cb0aac48c6d7578.png)](///static/92edbcea24f0d9c42f0bd0f3ad392367/96191/post-setup.png)

在弹出窗口中单击 OK，允许 API Gateway 调用您的 lambda 函数。

#### 将应用程序/x-www-form-urlencoded 转换为应用程序/json

在下一个屏幕上，您将看到调用 Lambda 函数的所有步骤。

[![Method Execution](img/d3b415f1e19daa55487d29ee4e6c8b50.png)](///static/d4da9bc1e84f8b422ffedc7460b451a8/4a70a/method-execution.png)

我们将更新集成请求，以便在将它传递给我们的 Lambda 函数之前，它将接收到的`application/x-www-form-urlencoded`转换为`application/json`。

单击集成请求并向下滚动到映射模板。选择推荐的“当没有定义模板时”选项，并添加`application/x-www-form-urlencoded`作为内容类型。

对于模板，我们将使用开发者伙伴[瑞安·雷](http://www.ryanray.me/)的这个奇妙的[要点](https://gist.github.com/ryanray/668022ad2432e38493df)。

万一它下线了，你可以在这里找到它，但是请查看[要点](https://gist.github.com/ryanray/668022ad2432e38493df)以获得最新版本。

```
## convert HTML POST data or HTTP GET query string to JSON

## get the raw post data from the AWS built-in variable and give it a nicer name
#if ($context.httpMethod == "POST")
 #set($rawAPIData = $input.path('$'))
#elseif ($context.httpMethod == "GET")
 #set($rawAPIData = $input.params().querystring)
 #set($rawAPIData = $rawAPIData.toString())
 #set($rawAPIDataLength = $rawAPIData.length() - 1)
 #set($rawAPIData = $rawAPIData.substring(1, $rawAPIDataLength))
 #set($rawAPIData = $rawAPIData.replace(", ", "&"))
#else
 #set($rawAPIData = "")
#end

## first we get the number of "&" in the string, this tells us if there is more than one key value pair
#set($countAmpersands = $rawAPIData.length() - $rawAPIData.replace("&", "").length())

## if there are no "&" at all then we have only one key value pair.
## we append an ampersand to the string so that we can tokenise it the same way as multiple kv pairs.
## the "empty" kv pair to the right of the ampersand will be ignored anyway.
#if ($countAmpersands == 0)
 #set($rawPostData = $rawAPIData + "&")
#end

## now we tokenise using the ampersand(s)
#set($tokenisedAmpersand = $rawAPIData.split("&"))

## we set up a variable to hold the valid key value pairs
#set($tokenisedEquals = [])

## now we set up a loop to find the valid key value pairs, which must contain only one "="
#foreach( $kvPair in $tokenisedAmpersand )
 #set($countEquals = $kvPair.length() - $kvPair.replace("=", "").length())
 #if ($countEquals == 1)
  #set($kvTokenised = $kvPair.split("="))
  #if ($kvTokenised[0].length() > 0)
   ## we found a valid key value pair. add it to the list.
   #set($devNull = $tokenisedEquals.add($kvPair))
  #end
 #end
#end

## next we set up our loop inside the output structure "{" and "}"
{
#foreach( $kvPair in $tokenisedEquals )
  ## finally we output the JSON for this pair and append a comma if this isn't the last pair
  #set($kvTokenised = $kvPair.split("="))
 "$util.urlDecode($kvTokenised[0])" : #if($kvTokenised[1].length() > 0)"$util.urlDecode($kvTokenised[1])"#{else}""#end#if( $foreach.hasNext ),#end
#end
} 
```

Enter fullscreen mode Exit fullscreen mode

你的页面现在应该看起来像这样，记得点击保存。：

[![Mapping Template](img/7594741a58d9a04a9d7753d7ae6691de.png)](///static/780063b169cc615399791e8c6d08676d/b79a5/mapping-template.png)

最后，我们需要设置响应，以便它返回一个 302，带有指向我们返回页面的位置头。

#### 返回 302

返回“方法执行”页面，然后单击“方法响应”。我们将**删除**默认的 200 响应，并替换为带有位置头的 302。您的方法响应应该如下所示:

[![Method Response](img/036b743930dc63aebf6dbd6833c87f56.png)](///static/d94a44fbeba830c8c517a2a64c59582f/a2ef2/method-response.png)

返回到方法执行并单击集成响应。再次删除现有的 200 响应，并创建一个响应状态为 302 的新响应。

然后，您需要添加一个映射值`integration.response.body.location`。结尾的`location`应该与 lambda 函数在 JSON 响应中返回的值相匹配。

你的回答应该是这样的:

[![Integration Response](img/7507f07f1c8deca0d47f79ad877a36d8.png)](///static/14973b6f32fbc738f3cd05b0a9c0cb31/75d3b/integration-response.png)

最后，在保存之后，从 Actions 菜单中选择 Deploy API。值得注意的是，如果您将来更新 API Gateway 中的任何设置，您需要单击部署 API，更改才会生效。

将提示您创建新的部署阶段。称之为类似 prod 的东西，然后单击 Deploy。

[![Deployment Stage](img/6280bd1b5494c9c87cc65a5c989dd48f.png)](///static/094e9339fa7c187a9b5b237b28dfddf2/c1b63/deployment-stage.png)

部署后，您将在页面顶部获得一个调用 URL。您应该能够将此用作您的联系表单上的操作 URL。

#### 自定义域名

最后，您可能希望为您的 API 使用自己的域名。为此，请转到侧面的自定义域名，并输入您想要使用的域名。比如 api.yourdomain.com。

您需要使用美国东部 1 区的 ACM 为您的域设置一个证书，以便 SSL 能够工作。

[![Custom Domain Name](img/601d965114211fa91c45f0c5e1d2444f.png)](///static/abb74a88d790723d8b53f8a5286b2bf9/ad12c/custom-domain.png)

保存后，初始化和开始工作大约需要 40 分钟。但是，您将获得一个 CloudFront Url，您需要将它作为 A 记录别名添加到 Route 53 中。

最后，您需要添加一个映射到端点的基本路径。我已经用/contact 做我的了。

[![Base Path Endpoint](img/8b2b99c8ddeedcbc83b7c772eaa0169e.png)](///static/f4d2e46b0059a008cbce201f1cc240f3/a4262/base-path-endpoint.png)

一旦初始化，你应该可以用[https://api.yourdomain.com/contact](https://api.yourdomain.com/contact)替换你的网址。

## 结论

使用 AWS Lambda 函数是创建可伸缩的小功能的好方法。这既可以像这种方法一样是独立的，也可以是更大的微服务架构的一部分。也有可能主持一个完整的。Net 核心 API 与 Lambda 和 API 网关，但我会保存到另一个职位！