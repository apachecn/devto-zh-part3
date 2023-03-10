# 用 Java 进行文本到语音的电话通话

> 原文：<https://dev.to/vonagedev/make-text-to-speech-phone-call-with-java-aom>

## 简介

在之前的教程中，我们向您展示了如何使用 Java 来[接听电话，并使用](https://www.nexmo.com/blog/2018/08/09/receive-a-phone-call-with-java-dr/)[文本到语音转换](https://www.nexmo.com/tts)来回复。除了接听电话，您还可以拨打电话。

在本教程中，您将创建一个应用程序，它可以利用 Java 和 [Nexmo Voice API](https://developer.nexmo.com/voice/voice-api/overview) 进行文本到语音的电话呼叫。

## 先决条件

要完成本教程，您需要一个 [Nexmo 帐户](https://dashboard.nexmo.com/sign-up)。如果您还没有帐户，请立即注册。

您将使用 [Gradle](https://gradle.org/) 来管理您的依赖项并运行您的应用程序。此外，您需要确保安装了 JDK 的副本。在本教程中，我将使用 JDK 11，这是目前的 LTS。

最后，您需要安装 [Nexmo CLI](https://github.com/Nexmo/nexmo-cli#installation) 。您将使用它来购买电话号码，并配置您的 Nexmo 帐户以指向您的新应用程序。

## 用 Java 进行文字语音通话

本教程将引导您完成以下步骤:

1.  使用 Nexmo CLI 购买电话号码并创建应用程序。
2.  使用 Gradle 初始化一个新的 Java 应用程序。
3.  使用 Nexmo Java 客户端库发起电话呼叫并执行文本到语音转换。

### 采购数量

您需要一个 Nexmo 号码来发送电话。如果您没有号码，您可以使用 Nexmo CLI 购买一个:

```
nexmo number:buy --country_code US 
```

记下您刚刚购买的号码。您将需要这个号码来链接您的应用程序，并且您将在以后编写创建电话呼叫的代码时使用它。

### 配置您的 Nexmo 账户

如果您没有应用程序，可以使用 Nexmo CLI 创建一个。您需要定义应用程序的名称，以及语音 API 默认使用的答案和事件 URL:

*   当链接到您的应用程序的电话号码接到电话时，Nexmo Voice API 将向您的应答 URL 发出请求。
*   当各种状态发生变化时，Nexmo Voice API 将向您的事件 URL 发出请求。

要了解更多关于应用的信息，请参阅我们的 [Nexmo 概念指南](https://developer.nexmo.com/concepts/guides/applications)。

使用以下命令通过 Nexmo CLI 创建应用程序:

```
nexmo app:create "Send TTS Call" http://example.com/webhooks/answer http://example.com/webhooks/events --keyfile private.key 
```

该命令还将创建一个名为`private.key`的文件，您需要使用该文件通过 Nexmo Voice API 进行身份验证才能进行呼叫。该文件将保存到您运行命令的目录中。还要记下返回的应用程序 ID，因为稍后会用到它。

[![A screenshot of the output from the Nexmo CLI showing the new Application ID.](img/80e2e08160b282c774cf7506c5963163.png)](https://www.nexmo.com/wp-content/uploads/2019/03/app-create.png)

### 使用 Gradle 建立一个新的 Java 项目

您将使用 Gradle 来管理您的依赖项，并创建和运行您的 Java 应用程序。

`gradle init --type=java-application`命令将创建您需要的所有文件夹以及一个示例类，您将在其中编写代码。

在命令行中，使用下面的命令创建一个新的 Java 项目，并在交互提示下接受默认值:

```
mkdir make-tts-call
cd make-tts-call
gradle init --type java-application 
```

Gradle 将在`src/main/java/make/tts/call`文件夹中创建`App`类。这个类中有一个`getGreeting`和`main`方法。您不需要`getGreeting`方法，所以可以随意删除它。

您的`App`类应该是这样的:

```
/*
 * This Java source file was generated by the Gradle 'init' task.
 */
package make.tts.call;

public class App {
    public static void main(String[] args) {
        // Future Code Goes Here
    }
} 
```

#### 添加依赖关系

您将使用 Nexmo Java 库与 Nexmo Voice API 进行通信。将以下内容添加到您的`build.gradle`文件中的`dependencies`块:

```
// Nexmo Java Client
implementation 'com.nexmo:client:4.2.0' 
```

### 初始化 Nexmo 客户端

Nexmo Java 库包含一个`NexmoClient`类，让您可以访问各种 Nexmo APIs。您将使用您的应用程序 ID，以及上一步中您的`private.key`文件的路径。`NexmoClient`将使用这些信息对 Nexmo 语音 API 进行认证。

将以下代码添加到`App`类的`main`方法中，解析任何导入:

```
NexmoClient nexmoClient = NexmoClient.builder()
        .applicationId(APPLICATION_ID)
        .privateKeyPath(PRIVATE_KEY_PATH)
        .build();

VoiceClient voiceClient = nexmoClient.getVoiceClient(); 
```

如果私钥文件无法加载，`NexmoClient`会抛出一个`Exception`。为了方便起见，修改您的`main`方法签名以抛出任何异常。您的`main`方法现在应该是这样的:

```
public static void main(String[] args) throws Exception {
    NexmoClient nexmoClient = NexmoClient.builder()`
            .applicationId(APPLICATION_ID)
            .privateKeyPath(PRIVATE_KEY_PATH)
            .build();

    VoiceClient voiceClient = nexmoClient.getVoiceClient();
} 
```

### 构建 Nexmo 调用控制对象

使用 [Nexmo 呼叫控制对象(NCCO)](https://developer.nexmo.com/voice/voice-api/ncco-reference) 控制 Nexmo 语音 API。NCCO 是一个 JSON 数组，它包含一组语音 API 将执行的操作。

以下 NCCO 将指示 Nexmo 语音 API 在接听电话时与接听者通话:

```
[  {  "action":  "talk",  "text":  "This is a text-to-speech call from Nexmo"  }  ] 
```

Nexmo Java 客户端库提供了允许您构建 NCCO 的类。您将使用`Ncco`和`TalkAction`类来构建 NCCO。

将下面的代码添加到`main`方法中，解析任何导入:

```
TalkAction intro = TalkAction.builder("This is a text-to-speech call from Nexmo").build();
Ncco ncco = new Ncco(intro); 
```

### 打电话

`VoiceClient`包含一个名为`createCall`的方法，该方法需要一个`com.nexmo.client.voice.Call`。`Call`对象用于定义您呼叫的号码、您希望呼叫的接收者以及控制呼叫的 NCCO。

在`main`方法中创建一个新的`Call`对象，并用创建的对象调用`createCall`方法，解析任何导入:

```
Call call = new Call(TO_NUMBER, NEXMO_NUMBER, ncco);
voiceClient.createCall(call); 
```

### 测试您的应用程序

在您的`make-tts-call`目录中用`gradle run`命令启动您的应用程序。您应该会收到一个来自您的 Nexmo 号码的电话。

[![](img/5369dc691f28c1618d4fd1603b0a1ce4.png)](https://www.nexmo.com/wp-content/uploads/2019/03/phone-ringing.jpg)

一旦你接听了这个电话，Nexmo Voice API 就会说出这样的信息，“这是一个来自 Nexmo 的文本到语音的呼叫。”

## 结论

在几行代码中，您创建了一个可以呼叫接收者并说出消息的应用程序。

查看我们关于 [Nexmo 开发者](https://developer.nexmo.com)的文档，在那里你可以了解更多关于[调用流](https://developer.nexmo.com/voice/voice-api/guides/call-flow)或 [Nexmo 调用控制对象](https://developer.nexmo.com/voice/voice-api/ncco-reference)的信息。参见我们的[Nexmo Java 代码片段](https://github.com/nexmo/nexmo-java-code-snippets)获取关于[本教程](https://github.com/Nexmo/nexmo-java-code-snippets/blob/master/src/main/java/com/nexmo/quickstart/voice/OutboundTextToSpeechWithNcco.java)及更多内容的完整代码示例。

帖子[用 Java 进行文本到语音的电话通话](https://www.nexmo.com/blog/2019/03/21/make-text-to-speech-phone-call-java-dr/)最早出现在 [Nexmo](https://www.nexmo.com) 上。