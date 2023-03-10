# 将您的 Unity 项目更新到 Watson SDK for Unity 3.1.0(和 Core SDK 0.2.0)

> 原文：<https://dev.to/ibmdeveloper/updating-your-unity-project-to-watson-sdk-for-unity-3-1-0-and-core-sdk-0-2-0-451f>

<figcaption>Cover Image: Gaming Keyboard — Photo by [Aidan Granberry](https://unsplash.com/photos/ak4hw4r6xio?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/code?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)</figcaption>

今年的第一个主要版本中，许多突破性的变化冲击了所有的 Watson SDK，但是您在旧 SDK 版本上的现有代码应该继续工作得很好。

液体错误:内部

这意味着我一直在更新我现有的任何内容，这花费了我比预期更长的时间。这也意味着我想做的任何新工作(AR 基金会)也被推迟了。

不幸的是，在这个问题解决之前，我不能发布任何与我的 ARKit 相关的内容:[https://github . com/Watson-developer-cloud/unity-SDK/issues/566](https://github.com/watson-developer-cloud/unity-sdk/issues/566)

更不幸的是，看起来 Android land 也有类似的问题:[https://github . com/Watson-developer-cloud/unity-SDK/issues/515](https://github.com/watson-developer-cloud/unity-sdk/issues/515)

如果您碰巧没有在移动设备(或 Magic Leap)上运行，并且您对使用最新更新的 Watson SDK for Unity 感兴趣，我已经整理了一些步骤来启动和运行您的项目。我最终会将它打包成回购，但理想情况下，我会等待设备特定的问题得到解决，以便我可以在 ARKit 上进行测试，并最终转向使用 AR Foundation。

#### 删除旧沃森 SDK 版本

我只是删除项目窗口中的目录。我喜欢删除代码，所以目录是额外的满足！谢谢你的服务，也谢谢你，下一个。

#### 为 Unity 和 Core SDK 添加新的 Watson SDK

这里有两步，所以请注意这些说明。这些步骤适用于 Watson SDK for Unity 3.1.0 和 Core SDK 0.2.0。

**Git 将 Unity 的**[**Watson SDK**](https://github.com/watson-developer-cloud/unity-sdk)直接克隆到您的资产目录中(或者，如果您想挑挑拣拣，将它放在其他地方并删除您不想让项目膨胀的内容，让您不打算使用的示例或测试/脚本存在)。

**Git 将** [**核心 SDK**](https://github.com/IBM/unity-sdk-core) 直接克隆到您的资产目录中。这是一个新的步骤，因为一些功能已经转移到一个单独的回购中。您需要它来运行整个 Watson SDK for Unity 体验。

#### 改变命名空间

您现有的源代码可能会有如下名称空间:

```
using IBM.Watson.DeveloperCloud.Services.Assistant.v1;
using IBM.Watson.DeveloperCloud.Services.TextToSpeech.v1;
using IBM.Watson.DeveloperCloud.Services.SpeechToText.v1;
using IBM.Watson.DeveloperCloud.Widgets;
using IBM.Watson.DeveloperCloud.DataTypes;
using IBM.Watson.DeveloperCloud.Utilities;
using IBM.Watson.DeveloperCloud.Logging;
using IBM.Watson.DeveloperCloud.Connection; 
```

Enter fullscreen mode Exit fullscreen mode

这些名称空间应该继续工作，这就是为什么如果您不想更新到新的 SDK 版本，就不必更新的原因。

根据您正在做的事情，您的名称空间可能看起来像这样:

```
using IBM.Watson.Assistant.V2;
using IBM.Watson.Assistant.V2.Model;
using IBM.Watson.SpeechToText.V1;
using IBM.Watson.TextToSpeech.V1;
using IBM.Cloud.SDK;
using IBM.Cloud.SDK.Utilities;
using IBM.Cloud.SDK.DataTypes; 
```

Enter fullscreen mode Exit fullscreen mode

注意 IBM 中的服务。Watson 和助手函数和实用程序位于 IBM.Cloud.SDK 中。如果您缺少其中一个 SDK，您会看到红色的曲线。确保您已经将两个 SDK 添加到您的 Assets 文件夹，并让 Unity 重新加载项目，导入 SDK 文件。

#### 删除用户名/密码引用

此时，您应该使用带有 API 密钥的新服务，或者您应该正在将带有用户名/密码的旧服务迁移到新服务并创建新凭证，这将为您提供一个 API 密钥。

液体错误:内部

大多数服务真正需要的字段是 api 键和 URL，甚至 URL 也被认为是可选的，这取决于您所在的地区。根据您的服务所在的位置，您的 URL 可能会有所变化。

沃森助手现在应该是这样的:

```
[Header("Watson Assistant")]
     [Tooltip("The service URL (optional). This defaults to \"https://gateway.watsonplatform.net/assistant/api\"")]
     [SerializeField]
     private string AssistantURL;
     [SerializeField]
     private string assistantId;
     [Tooltip("The apikey.")]
     [SerializeField]
     private string assistantIamApikey; 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到对传递给助手的内容的检查少了很多，因为我们不必检查凭证的组合。

```
Credentials asst\_credentials = null;
 TokenOptions asst\_tokenOptions = new TokenOptions()
 {
   IamApiKey = assistantIamApikey,
 };

 asst\_credentials = new Credentials(asst\_tokenOptions, AssistantURL);

 while (!asst\_credentials.HasIamTokenData())
 yield return null;

 \_assistant = new AssistantService(“2019–02–08”, asst\_credentials);

 \_assistant.CreateSession(OnCreateSession, assistantId);

 while (!sessionCreated)
 yield return null; 
```

Enter fullscreen mode Exit fullscreen mode

这个片段硬编码了版本日期 2019–02–08。

你还需要为 OnCreateSession 创建一个回调函数(我认为这对助手 V2 尤其重要，而不是 V1)。大概是这样的:

```
private void OnCreateSession(DetailedResponse<SessionResponse> response, IBMError error)
     {
         Log.Debug("AvatarPatternError.OnCreateSession()", "Session: {0}", response.Result.SessionId);
         sessionId = response.Result.SessionId;
         sessionCreated = true;
     } 
```

Enter fullscreen mode Exit fullscreen mode

您可以将您的凭据放在一个文件中，并将其添加到您的。gitignore 以防止在场景文件中无意中共享您的凭证，但这取决于您。

#### 改变类型名称&回调函数

这将是最难解释的，所以我建议查看 SDK 示例和脚本，看看您的代码可能发生了什么变化。

我注意到这些变化的地方包括文本到语音的合成方法。

```
private void CallTextToSpeech(string outputText)
     {
         Debug.Log("Sent to Watson Text To Speech: " + outputText);

         byte[] synthesizeResponse = null;
         AudioClip clip = null;

         \_textToSpeech.Synthesize(
             callback: (DetailedResponse<byte[]> response, IBMError error) =>
             {
                 synthesizeResponse = response.Result;
                 clip = WaveFile.ParseWAV("myClip", synthesizeResponse);
                 PlayClip(clip);

             },
             text: outputText,
             voice: "en-US\_AllisonVoice",
             accept: "audio/wav"
         );
     } 
```

Enter fullscreen mode Exit fullscreen mode

传递给 Watson Assistant 的对象看起来有点不同，如果不是更干净的话。这个例子不包括传入上下文对象。

```
var input = new MessageInput()
{
   Text = "Hello"
};

\_assistant.Message(OnMessage, assistantId, sessionId, input); 
```

Enter fullscreen mode Exit fullscreen mode

请注意会话 id，我想这可能只是给助理 V2 的，所以请再次检查我。

Watson Assistant 回调的对象看起来也不一样，DetailedResponse 在处理 JSON 响应时给了你奇妙的点符号。

```
string intent = response.Result.Output.Intents[0].Intent; 
```

Enter fullscreen mode Exit fullscreen mode

随着 IBMError 类型的增加，错误处理也发生了变化。

这些似乎是最令人难忘的，但是请看一下 SDK 以获得更多信息。

#### 总结起来

希望这能帮你升级到 Unity 的新 Watson SDKs。正如我提到的，一旦至少 iOS 的设备问题得到解决，我将为新的 SDK 更新我的[代码模式](https://developer.ibm.com/patterns/build-an-ai-powered-ar-character-in-unity-with-arkit/)和 ARKit 内容。

你更新你的项目了吗？还是坚持旧版本？让我知道！