# 用 Dialogflow 和 React Native 构建聊天机器人

> 原文：<https://dev.to/jscrambler/build-a-chatbot-with-dialogflow-and-react-native-3ndd>

聊天机器人是为任何软件产品提供对话体验的强大方式。每种对话体验都取决于聊天机器人的实现对最终用户来说是好是坏。现代世界生活在人工智能的技术浪潮中，机器人是其中的一个重要组成部分。

在本教程中，我们将使用 [Dialogflow](https://dialogflow.com/) 和 React Native 从头开始构建一个聊天机器人应用程序。在本教程中使用 Google 的 Dialogflow 的主要原因是，与其他 bot 框架或类似的服务提供商不同，您不必通过提供卡的详细信息来完成繁重的注册过程。

我们要建造什么？让我们构建一个聊天机器人，当以不同方式询问时，它返回当前日期。

本教程的完整代码可以在这个 [GitHub 库](https://github.com/JscramblerBlog/RNDialogflowChatbot)中找到。

## 要求

为了完成本教程，您需要:

*   `react-native-cli`版本`2.0.1`或更高版本可通过`npm`获得
*   React、React Native 和 JavaScript 知识
*   一个谷歌账户
*   `react-native-gifted-chat`，提供可定制的完整聊天 UI 界面
*   这将帮助我们将我们的应用程序与 Google Dialogflow 的 SDK 连接起来

## 入门

为了入门，首先要求使用`react-native-cli`并创建一个新的项目目录。从您的终端运行以下命令。

```
react-native init RNDiagflowChatbot

# traverse inside the directory
cd RNDiagflowChatbot 
```

此外，确保您现在位于终端窗口的项目目录中。这一步是必需的，因为我们将添加三个 npm 包，它们将帮助我们构建 Chatbot 应用程序。

```
npm install --save react-native-gifted-chat react-native-dialogflow react-native-voice 
```

请注意，我们不会直接使用`react-native-voice`，但是您需要安装并链接到 React 本地项目。原因是`react-native-dialogflow`需要`react-native-voice`作为对等依赖。下一步是将 Dialogflow SDK 库链接到 React 本地项目。

```
react-native link react-native-dialogflow
react-native link react-native voice 
```

当链接过程完成时，您将收到一条成功消息。此外，为了防止应用程序在运行时崩溃，您必须在根标签`<dict>`内的文件`iOS/RNDiagflowChatbot/Info.plist`内添加一些 iOS 权限。

```
// Info.plist

  <key>NSSpeechRecognitionUsageDescription</key>
  <string>Your usage description here</string>
  <key>NSMicrophoneUsageDescription</key>
  <string>Your usage description here</string>
  <key>UIRequiredDeviceCapabilities</key> 
```

现在，让我们继续创建第一个聊天组件。我们将在`App.js`文件中使用`App`组件，但是非常欢迎您以后重构您的代码。

```
// App.js
import React, { Component } from 'react';
import { StyleSheet, Text, View, Image } from 'react-native';
import { GiftedChat } from 'react-native-gifted-chat';

class App extends Component {
  state = {
    messages: [
      {
        _id: 1,
        text: `Hi! I am the FAQ bot 🤖 from Jscrambler.\n\nHow may I help you with today?`,
        createdAt: new Date(),
        user: {
          _id: 2,
          name: 'FAQ Bot',
          avatar: 'https://i.imgur.com/7k12EPD.png'
        }
      }
    ]
  };

  onSend(messages = []) {
    this.setState(previousState => ({
      messages: GiftedChat.append(previousState.messages, messages)
    }));
  }

  render() {
    return (
      <View style={{ flex: 1, backgroundColor: '#fff' }}>
        <GiftedChat
          messages={this.state.messages}
          onSend={messages => this.onSend(messages)}
          user={{
            _id: 1
          }}
        />
      </View>
    );
  }
}

export default App; 
```

我们首先需要必要的组件，包括`react-native-gifted-chat`包中的`GiftedChat`。在组件的状态中，每当组件被初始呈现时，您会发现一个静态或欢迎消息。

`createdAt`时间将在聊天界面显示当前时间和日期。`user`对象是发送消息的用户——在我们的例子中是机器人。它由用户名、唯一 ID 和头像等属性定义。`react-native-gifted-chat`会自动在 UI 中添加一个圆形头像。

[![Try JavaScript Threat Monitoring](img/13e59deaac8585b1930295de34efc2dd.png)](https://jscrambler.com/whats-new?utm_source=dev.to&utm_medium=referral)

render 函数中的第`<View style={{ flex: 1, backgroundColor: '#fff' }}>`行显示，您可以使用 Gifted Chat 的组件添加自己的定制样式。`GiftedChat`组件可以从组件的初始状态中获取像`messages`这样的道具，一个`onSend`道具，它是发送消息时使用的回调函数，以及消息的用户 ID。

这就是在 React 原生应用中实现聊天界面的简单程度。要在 iOS 模拟器中运行您的应用程序，请运行命令`react-native run-ios`。对于 Android 用户，运行命令`react-native run-android`,并确保你有 Android 模拟器(或通常称为 Android 虚拟设备)在后台运行。

请参见下面我们应用程序的当前运行状态。

<center>![Initial App](img/4c181f72dff414b67551f08711d8e721.png)</center>

## Google 的 Dialogflow 设置

Dialogflow 是谷歌的自然语言处理(NLP)服务。它有许多集成，多种语言的 SDK 和预构建的代理。它与谷歌助手一起工作非常简单。访问[dialog flow 网站](https://dialogflow.com/)并创建一个新帐户或使用您现有的 Google ID 登录。一旦你登录，你将受到一个由不同的**代理**组成的屏幕的欢迎。

[![Agents](img/a3e010e8160dc1a9d944fc29e13310f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---YxZyWCS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jscrambler.com/conteimg/2019/03/jscrambler-blog-build-chatbot-dialogflow-react-native-agents.png)

点击`Create Agent`按钮制作一个。我们将命名我们的代理: **faq-bot** 。如下填写详细信息。

[![Create Agent](img/4be314b10783fd134c203ac555388d7b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y8b1irAz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jscrambler.com/conteimg/2019/03/jscrambler-blog-build-chatbot-dialogflow-react-native-create-agent.png)

填写完详细信息后，点击按钮`Create`。

通常，对于小型应用程序，您将有一个代理。在 Dialogflow 中，对话的基本流程包括以下步骤:

*   用户给出输入
*   您的 Dialogflow 代理解析该输入
*   您的代理向用户返回回应

这些*代理*可以理解人类语言的大量和各种细微差别，并将其翻译成你的应用和服务可以理解的标准和结构化的含义。每个代理包含不同的**意图**。

意图是在聊天机器人应用程序中发送回用户的动作或响应。它可以包含不同类型的响应或操作。这个过程的下一步是建立你的第一个意图。

<center>![Intents](img/83b6a097f3f4e2fc51c7cdb688b548ed.png)</center>

意图可以是显示给用户的简单文本响应或一组经过训练的短语。还有从用户查询中提取信息的动作和参数。这类信息的例子包括日期、时间、姓名、地点等等。

现在，我们没有任何自己创造的意图。让我们在下一节创建一个。

## 第一次对话流意图

让我们加上我们的第一个意图，我们称之为`date.current`。这个意图的目的是在用户询问时将当前日期返回给用户。我们可以添加预定义的常用训练短语来识别用户向我们的机器人提出的问题。看看下面的训练短语。

<center>![First Intent](img/08203a8d7ee6c64f385d246c66df1d5e.png)</center>

由于“date”作为一个关键字在 Dialogflow 的 API 中可用，它被自动认为是一个要采取行动的参数。

[![Action Parameters](img/8a2c902f19a24d140b5d913fac27adf7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YOxTQoMi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jscrambler.com/conteimg/2019/03/jscrambler-blog-build-chatbot-dialogflow-react-native-action-parameters.png)

这个过程的最后一步是添加一个文本响应供用户接收。

[![Responses](img/dea84462c914383925dffde573272e13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fU3Sg-Fy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jscrambler.com/conteimg/2019/03/jscrambler-blog-build-chatbot-dialogflow-react-native-responses.png)

不要忘记点击对话框顶部的`Save`按钮。

## 连接 Dialogflow 和 React Native

我们需要一些访问密钥来使用 Dialogflow 的 npm 包和我们的应用程序。现在，从控制台窗口，转到设置(它是代理名称旁边的齿轮图标)。

<center>![Settings](img/a0e6b06e1891e69d23d8f4dec4b80b08.png)</center>

然后点击`Service Account`旁边的值。

[![Service Account](img/4db5c941b838375f90cf73d2b213becb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gZ_xYJ_a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jscrambler.com/conteimg/2019/03/jscrambler-blog-build-chatbot-dialogflow-react-native-service-account-1.png)

进入服务帐户后，找到名为`Dialogflow Integrations`的帐户，向右滚动直到看到三个点。点击该菜单，并点击`Create Key`。

<center>![Create Key](img/495dd6077f9bc2ef607c3e6544282002.png)</center>

将其下载为 JSON 文件，并在编辑器中打开该文件。您将在该文件中找到以下内容。

```
{  "type":  "service_account",  "project_id":  "faq-bot-XXXX",  "private_key_id":  "XXXX",  "private_key":  "-----BEGIN PRIVATE KEY-----XXXX\n-----END PRIVATE KEY-----\n",  "client_email":  "XXXX",  "client_id":  "XXXX",  "auth_uri":  "XXXX",  "token_uri":  "XXXX",  "auth_provider_x509_cert_url":  "XXXX",  "client_x509_cert_url":  "XXXX"  } 
```

JSON 文件中的每个键都有它的值(代替`XXXX`)，其中一些是敏感的。

## 打造聊天机器人

现在，创建一个名为`env.js`的新文件，并在其中放置与上面相同的值。

```
// env.js

export const dialogflowConfig = {
  type: 'service_account',
  project_id: 'faq-bot-XXXX',
  private_key_id: 'XXXX',
  private_key: '-----BEGIN PRIVATE KEY-----XXXX\n-----END PRIVATE KEY-----\n',
  client_email: 'XXXX',
  client_id: 'XXXX',
  auth_uri: 'XXXX',
  token_uri: 'XXXX',
  auth_provider_x509_cert_url: 'XXXX',
  client_x509_cert_url: 'XXXX'
}; 
```

您现在正在导出配置对象，您将在`App.js`文件中需要该对象。

```
import { Dialogflow_V2 } from 'react-native-dialogflow';

import { dialogflowConfig } from './env'; 
```

此外，我们正在重构用户对象，将它从状态中分离出来，如下所示。

```
const BOT_USER = {
  _id: 2,
  name: 'FAQ Bot',
  avatar: 'https://i.imgur.com/7k12EPD.png'
}; 
```

现在的状态如下图所示。

```
state = {
  messages: [
    {
      _id: 1,
      text: `Hi! I am the FAQ bot 🤖 from Jscrambler.\n\nHow may I help you with today?`,
      createdAt: new Date(),
      user: BOT_USER // <= note this
    }
  ]
}; 
```

[![Try Jscrambler Free](img/3b22f445f8691c74ce8f04a0dd6f6a5b.png)](https://jscrambler.com/code-integrity?utm_source=dev.to&utm_medium=referral)

现在让我们使用生命周期方法`componendDidMount`来应用 Dialogflow 的配置。

```
componentDidMount() {
    Dialogflow_V2.setConfiguration(
      dialogflowConfig.client_email,
      dialogflowConfig.private_key,
      Dialogflow_V2.LANG_ENGLISH_US,
      dialogflowConfig.project_id
    );
  } 
```

`Dialogflow_V2.setConfiguration()`中的所有值都来自于`env.js`文件。在`onSend`函数中，您必须确保它将消息的文本发送到 Dialogflow 代理。像下面这样重构它。

```
onSend(messages = []) {
    this.setState(previousState => ({
      messages: GiftedChat.append(previousState.messages, messages)
    }));

    let message = messages[0].text;
    Dialogflow_V2.requestQuery(
      message,
      result => this.handleGoogleResponse(result),
      error => console.log(error)
    );
  } 
```

方法`Dialogflow_V2.requestQuery`用于向代理发送文本请求。它包含三个参数:

*   文本本身作为第一个参数；在我们的情况下`message`
*   `result`和`error`回调函数

创建函数`handleGoogleResponse(result)`是为了处理返回的响应，然后调用函数`sendBotResponse()`。

```
handleGoogleResponse(result) {
    let text = result.queryResult.fulfillmentMessages[0].text.text[0];
    this.sendBotResponse(text);
}

sendBotResponse(text) {
    let msg = {
      _id: this.state.messages.length + 1,
      text,
      createdAt: new Date(),
      user: BOT_USER
    };

    this.setState(previousState => ({
      messages: GiftedChat.append(previousState.messages, [msg])
    }));
  } 
```

然后，`sendBotResponse`函数更新`App`组件的状态，并在聊天界面中向用户显示任何响应。请参见下面的实际应用。

<center>![Final App](img/3bd5f77eabd3e2b1b623dd3367d389bd.png)</center>

你可以在下面找到`App.js`的完整代码。

```
// App.js

import React, { Component } from 'react';
import { StyleSheet, Text, View } from 'react-native';
import { GiftedChat } from 'react-native-gifted-chat';
import { Dialogflow_V2 } from 'react-native-dialogflow';

import { dialogflowConfig } from './env';

const BOT_USER = {
  _id: 2,
  name: 'FAQ Bot',
  avatar: 'https://i.imgur.com/7k12EPD.png'
};

class App extends Component {
  state = {
    messages: [
      {
        _id: 1,
        text: `Hi! I am the FAQ bot 🤖 from Jscrambler.\n\nHow may I help you with today?`,
        createdAt: new Date(),
        user: BOT_USER
      }
    ]
  };

  componentDidMount() {
    Dialogflow_V2.setConfiguration(
      dialogflowConfig.client_email,
      dialogflowConfig.private_key,
      Dialogflow_V2.LANG_ENGLISH_US,
      dialogflowConfig.project_id
    );
  }

  handleGoogleResponse(result) {
    let text = result.queryResult.fulfillmentMessages[0].text.text[0];
    this.sendBotResponse(text);
  }

  onSend(messages = []) {
    this.setState(previousState => ({
      messages: GiftedChat.append(previousState.messages, messages)
    }));

    let message = messages[0].text;
    Dialogflow_V2.requestQuery(
      message,
      result => this.handleGoogleResponse(result),
      error => console.log(error)
    );
  }

  sendBotResponse(text) {
    let msg = {
      _id: this.state.messages.length + 1,
      text,
      createdAt: new Date(),
      user: BOT_USER
    };

    this.setState(previousState => ({
      messages: GiftedChat.append(previousState.messages, [msg])
    }));
  }

  render() {
    return (
      <View style={{ flex: 1, backgroundColor: '#fff' }}>
        <GiftedChat
          messages={this.state.messages}
          onSend={messages => this.onSend(messages)}
          user={{
            _id: 1
          }}
        />
      </View>
    );
  }
}

export default App; 
```

## 结论

使用 Dialogflow 这样的强大 API 的可能性是无限的。很快，您就可以在 React 本地应用程序中构建自己的聊天机器人界面，作为一个有价值的支持或营销工具。

我们希望本教程为您提供了一个简单的步骤来掌握概念并构建您自己的东西。

**最后，如果你正在用敏感逻辑构建 React 原生应用，确保用 [Jscrambler](https://jscrambler.com/code-integrity?utm_source=dev.to&utm_medium=referral)** 保护它们免受代码窃取和逆向工程。

这篇文章最初发表在 Aman Mittal 的 Jscrambler 博客上。