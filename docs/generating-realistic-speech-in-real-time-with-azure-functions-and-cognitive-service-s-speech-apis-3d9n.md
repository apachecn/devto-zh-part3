# 使用 Azure 功能和认知服务的语音 API 实时生成逼真的语音

> 原文：<https://dev.to/garypretty_50/generating-realistic-speech-in-real-time-with-azure-functions-and-cognitive-service-s-speech-apis-3d9n>

上周，微软的云开发者倡导者克洛伊·康登(Chloe Condon)发表了一篇很棒的文章，并附带了一个开源项目，帮助人们处理尴尬的社交场合。该项目结合了 Azure 功能、Twilio 和 Flic 按钮(可从 Shortcut Labs 获得)，允许用户使用离散的蓝牙按钮触发假呼叫，这触发了 Azure 功能，而 Azure 功能又使用 Twilio API 拨打指定号码并播放预先录制的 MP3。你可以在 Chloe 在 Medium 上写的关于这个项目[的文章中读到更多的细节。](https://medium.com/microsoftazure/an-ambiverts-guide-to-azure-functions-95931976c565)

顺便说一句，克洛伊把自己描述成一个“野心者”，我承认我从来没有遇到过这个词，但是在读了这个描述之后，我觉得这个词非常合适。就像克洛伊一样，人们认为我是一个外向的人，但尽管我完全适应在一屋子的与会者面前展示，并在会前会后与人们互动，但我很快发现自己需要充电，并想尽一切借口从这种情况中解脱出来——即使只是很短的一段时间。因此，这个项目引起了我的共鸣(似乎也引起了半个 Twitter 的共鸣！).

当我第一次看这个应用程序时，我突然想到的一件事是，它需要一个预先录制的 MP3。现在，这显然意味着您可以从中获得一些巨大的乐趣，有可能在电话中播放您最喜爱的艺术家，但如果您可以在拨打电话时动态生成自然的语音，这不是很好吗？进入微软认知服务套件的语音服务——这是我将在这篇文章中向你展示的。

在过去一年左右的时间里，语音服务经历了一些戏剧性的改进，在我看来，最令人难以置信的是神经语音。这是一种生成语音的能力，几乎与真实的人类声音无法区分。你可以[在这里](https://azure.microsoft.com/en-gb/blog/microsoft-previews-neural-network-text-to-speech/)阅读宣布神经声音的博文。

因此，基于所有这些，我想要实现的是触发 Azure 函数的能力——传递要转换成语音的文本——并让它为我生成一个 MP3 文件，并立即可用。

这就是我在这篇文章中要向你展示的。如果你想听一段由认知服务神经语音生成的片段(使用我将向你展示如何构建的功能)，你可以[下载这个 MP3 并试听](https://www.garypretty.co.uk/wp-content/uploads/2019/04/sample-neural-speech.mp3)。

# 创建我们的语音资源

我们需要做的第一件事是在 Azure 中创建新的语音服务资源。这将为我们提供文本到语音的端点，我们将在稍后的函数中使用它。

[![Speech resource in Azure](img/a8d3d07b34efade908218ecc9f41a862.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9RCwIbx2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/speech-resource.png)

一旦您创建了新的语音资源，请转到“密钥”部分，记下为您生成的两个订阅密钥中的一个，以及您在创建新的语音资源时为其指定的名称——稍后您会用到它们。

[![Speech resource Subscription Keys](img/565bb31cd63fdd780666fdf130e890ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LsBv8hTO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/keys-1024x330.png)

# 创建 Azure 函数

如果你不熟悉 Azure 函数，我会在这里带你创建一个，但你可以在微软文档网站上阅读更多关于它们的细节。

在 Azure 门户中创建一个新的功能应用程序。以及您通常需要提供的详细信息，如姓名。资源组等，您需要为您的函数选择运行时——对于这个例子，我选择。NET，因为我正在创建一个基于 C#的函数。

[![Azure Function Settings](img/06294b826693275bfe1c369a9b16f440.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Kz8en0Ky--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/create-function.png)

一旦创建了函数应用程序，就可以开始创建和配置生成语音的函数了。导航到门户中新创建的功能应用程序，并创建一个新功能。在这里，您将看到针对您的开发环境的几个选项，包括 Visual Studio、VS 代码或使用内置编辑器在门户中开发一个功能——在我的例子中，我选择使用门户中的内置编辑器，但是您可以自由选择最适合您的编辑器。选择其中一个选项将为您提供启动和运行环境的说明。

接下来，我们需要选择我们希望创建的函数的类型，即函数将如何被触发。可以将函数配置为以多种方式触发，包括 HTTP 请求(访问向 webhook 触发事件的 URL)或基于计时器的调度。对于这个函数，为 HTTP 触发器选择“web hook+API”——这意味着我们将能够按需触发语音生成。

[![Azure Function Trigger Type](img/5953fba66832f98531bbf9b891619bf2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RdRHTimc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/trigger-1024x704.png)

您现在应该有一个空白函数，看起来像下面这样。

[![Azure Function Trigger Type](img/24405df867ea79673fb85a4197cd409f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XrjT-q5U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/default-function-1024x398.png)

# 配置我们的输出绑定，用于存储我们生成的 MP3

当我们生成我们的语音时，我们将需要某个地方来存储输出，以便我们可以在其他地方使用它(例如，从 Chloe 的应用程序)。这就是 Azure 函数的输入/输出绑定集成可以提供帮助的地方。绑定允许我们以声明的方式将一个函数连接到另一个资源，从函数代码中提供更容易的访问和集成。例如，您可以创建一个绑定到 Azure 表存储的输入，当您的函数被触发时，为您提供对该表的访问。你可以在
[https://docs . Microsoft . com/en-us/azure/azure-functions/functions-triggers-bindings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-triggers-bindings)了解更多关于触发器和绑定的信息

然而，出于我们的目的，我们想要创建一个绑定到 Azure Blob 存储的输出，允许我们轻松地将生成的音频文件写入 Blob 存储。为此，导航到门户中函数下面的 Integrate 选项卡，并选择 Blob storage 创建一个新的输出绑定。然后我们定义一些设置；

*   Blob 参数名称–将传递给我们的函数并在其中可用的参数的名称。

*   路径–保存新文件的路径。我选择了指定“output container/generated speech . MP3”，这意味着我每次都会替换同一个文件。但是，您可以使用“outcontainer/{rand-guid}.mp3”每次生成一个新文件。

*   存储帐户连接–您无需更改，因为它会自动连接到您在创建功能应用程序时指定/创建的存储帐户。

# 构建出我们生成语音的函数

好了，现在我们有了语音资源和 Azure 函数，可以按需触发(通过 HTTP 请求)并将输出存储到 Blob 存储中。是时候把这一切都集中起来了。

我们需要做的第一件事是改变函数的签名，以接受我们之前创建的 Azure blob 存储的输出绑定，并创建一些我们将需要的属性。在下面的代码片段中，您将看到我为我们的存储绑定添加了一个参数“CloudBlockBlob outputBlob ”,然后创建了几个与我们之前创建的语音资源相关的属性。

这里是您需要使用您的订阅密钥/资源名称的地方，您在我们之前创建语音资源时记下了该名称。根据您的语音资源的位置，您可能还需要更改下面的端点以反映正确的位置(我在美国东部创建了我的资源)。您还会看到，这是我在生成语音时指定我希望使用的语音的地方——不同的地方支持不同的语音，因此请务必查看[https://docs . Microsoft . com/en-GB/azure/cognitive-services/speech-service/language-support # text-to-speech](https://docs.microsoft.com/en-gb/azure/cognitive-services/speech-service/language-support#text-to-speech)以了解每个地区支持哪些语音。这尤其适用于新的神经声音(听起来几乎与人类无法区分的声音)，目前只在某些地区可用。

```
public static async Task&lt;IActionResult> Run(HttpRequest req, CloudBlockBlob outputBlob, ILogger log)
{
    // Access key for the Speech resource created within Azure
    string subscriptionKey = "&lt;YOUR SPEECH SUBSCRIPTION KEY>";
    // Text to speech endpoint for your speech resource - be sure to check that the
    // voice you specify below is supported within your region
    string ttsEndpoint = "https://eastus.tts.speech.microsoft.com/cognitiveservices/v1";
    // Endpoint to fetch an access token for your text to speech request
    // Be sure that the region matches the region of your text to speech endpoint
    string accessTokenEndpoint = "https://eastus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    // The name of your speech resource in Azure
    string azureSpeechResourceName = "&lt;YOUR SPEECH RESOURCE NAME>";
    // The voice to use - make sure to use a voice that is available in your region
    // You can see available voices at 
    // https://docs.microsoft.com/en-gb/azure/cognitive-services/speech-service/language-support#text-to-speech 
    string voice = "Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)";
    // The above line specifies US male neural voice. Comment it out and uncomment
    // the line below to use the UK female neural voice
    //string voice = "Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)"; 
```

我们现在需要创建一个方法来获取访问语音资源的访问令牌——每次调用服务时都需要一个新的访问令牌。

```
public static async Task&lt;string> FetchTokenAsync(string tokenFetchUri, string subscriptionKey)
{
    using (var client = new HttpClient())
    {
        Console.WriteLine(subscriptionKey);
        Console.WriteLine(tokenFetchUri);

        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
        UriBuilder uriBuilder = new UriBuilder(tokenFetchUri);

        var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
        return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
    }
} 
```

最后，我们更新了函数的主体，将所有这些放在一起，动态地生成我们的语音音频文件，在调用函数时，接受我们希望转换为语音的文本作为 querystring 参数。在下面的代码片段中，我们执行了以下操作；

*   从查询字符串中获取我们想要转换的文本

*   使用上面显示的 helper 方法生成我们的访问令牌

*   使用 HttpClient 生成一个对语音服务端点的 HTTP 请求，传入我们想要转换成语音的文本和应该使用的声音。

*   最后，我们在 outputBlob 上使用 UploadFromStreamAsync 方法将我们的音频保存到 Blob 存储中，并将 URL 发送到文件，作为对函数调用的响应。

```
if(!string.IsNullOrEmpty(req.Query["text"]))
    {
        text = req.Query["text"];
    }
    else
    {
        return new BadRequestObjectResult("Please pass text you wish to generate on the query string or in the request body using param: text");
    }

    try
    {
        accessToken = await FetchTokenAsync(accessTokenEndpoint, subscriptionKey).ConfigureAwait(false);
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("Failed to obtain access token.");
    }

    string body = @"&lt;speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              &lt;voice name='" + voice + "'>" + text + "&lt;/voice>&lt;/speak>";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(ttsEndpoint);
            request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
            request.Headers.Add("Authorization", "Bearer " + accessToken);
            request.Headers.Add("Connection", "Keep-Alive");
            request.Headers.Add("User-Agent", azureSpeechResourceName);
            request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");

            using (var response = await client.SendAsync(request).ConfigureAwait(false))
            {
                response.EnsureSuccessStatusCode();

                using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
                {
                    await outputBlob.UploadFromStreamAsync(dataStream);
                    return new OkObjectResult(outputBlob.Uri.AbsoluteUri);
                }
            }
        }
    } 
```

# 完成的功能

一旦你完成了，完成的功能应该如下图所示；

```
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.Storage.Blob;

public static async Task&lt;IActionResult> Run(HttpRequest req, CloudBlockBlob outputBlob, ILogger log)
{
    string subscriptionKey = "&lt;YOUR SPEECH SUBSCRIPTION KEY>";
    string ttsEndpoint = "https://eastus.tts.speech.microsoft.com/cognitiveservices/v1";
    string accessTokenEndpoint = "https://eastus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    string azureSpeechResourceName = "&lt;YOUR SPEECH RESOURCE NAME>";
    string voice = "Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)";

    string accessToken = null;
    string text = null;

    if(!string.IsNullOrEmpty(req.Query["text"]))
    {
        text = req.Query["text"];
    }
    else
    {
        return new BadRequestObjectResult("Please pass text you wish to generate on the query string or in the request body using param: text");
    }

    try
    {
        accessToken = await FetchTokenAsync(accessTokenEndpoint, subscriptionKey).ConfigureAwait(false);
    }
    catch (Exception ex)
    {
        return new BadRequestObjectResult("Failed to obtain access token.");
    }

    string body = @"&lt;speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              &lt;voice name='" + voice + "'>" + text + "&lt;/voice>&lt;/speak>";

    using (var client = new HttpClient())
    {
        using (var request = new HttpRequestMessage())
        {
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(ttsEndpoint);
            request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
            request.Headers.Add("Authorization", "Bearer " + accessToken);
            request.Headers.Add("Connection", "Keep-Alive");
            request.Headers.Add("User-Agent", azureSpeechResourceName);
            request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");

            using (var response = await client.SendAsync(request).ConfigureAwait(false))
            {
                response.EnsureSuccessStatusCode();

                using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
                {
                    await outputBlob.UploadFromStreamAsync(dataStream);
                    return new OkObjectResult(outputBlob.Uri.AbsoluteUri);
                }
            }
        }
    }
}

public static async Task&lt;string> FetchTokenAsync(string tokenFetchUri, string subscriptionKey)
{
    using (var client = new HttpClient())
    {
        Console.WriteLine(subscriptionKey);
        Console.WriteLine(tokenFetchUri);

        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
        UriBuilder uriBuilder = new UriBuilder(tokenFetchUri);

        var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
        return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
    }
} 
```

# 调用你的新函数

现在您已经完成了函数的编写，您可以从浏览器中调用它，传递要在查询字符串上生成语音的文本。你可以在编辑器的顶部找到你的函数的 URL。

[![Azure Function - Finding the url](img/2384ba76899ef3ff5e1c3b4e9b9cb1cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--USb93q4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/get-url.png)

一旦有了 URL，只需追加一个新的查询字符串参数并指定想要生成的文本。调用这个 URL 应该会返回您生成的 MP3 文件的 URL，一切正常。以下是 URL 的示例，以及您应该在浏览器中看到的内容；

https://text =这是一些生成的语音，作为这篇文章的示例，演示了真实的声音听起来是什么样的。很酷吧？

[![Calling your new function](img/668ddc4aab07de58c3e9445897b3b438.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mGMFltIH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.garypretty.co.uk/wp-content/uploads/2019/04/called-function.png)

# 总结

在这篇文章中，我向您展示了如何创建一个 Azure 函数来调用语音认知服务，按需生成文本到语音，并将输出保存到 blob 存储中。你现在可以随心所欲地使用的输出，包括 Chloe Condon 出色的应用程序。

希望你会发现这很有用——请在评论中告诉我你的进展情况，如果你有任何问题，请在 Twitter 上联系我，我很乐意帮助你解决！