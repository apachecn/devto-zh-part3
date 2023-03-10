# React Native 的 Square 应用内支付 SDK

> 原文：<https://dev.to/squaredev/square-in-app-payments-sdk-for-react-native-27o5>

自从我们的应用内支付 SDK 发布以来，我们已经收到了很多关于 React Native 何时可用的请求。它正式在这里！你可以简单地`npm install —save react-native-square-in-app-payments`进入你的 React 原生项目，并遵循[这里](https://github.com/square/in-app-payments-react-native-plugin/blob/master/docs/get-started.md)的设置指南，开始在你的 React 原生应用中接受支付。

如果你还不熟悉[应用内支付 SDK](https://medium.com/square-corner-blog/introducing-square-in-app-payments-sdk-1fc93b32814c) ，它使开发者能够在他们自己的移动应用内接受 Square-powered 支付。

现在，说只安装 SDK 并继续前进太容易了，所以我们将深入研究我构建的 React 本机应用程序，以展示这是如何工作的。

[![_Our Order Ahead React Native App for buying Square Legos and demoed at ShopTalk._](img/1e7893b9f40b98776a35cac5b6412113.png) ](//images.ctfassets.net/1wryd5vd9xez/28iYm4BTKHlHtUzHj3xk7u/c156837bef8288951c7b4576b4a2fc21/https___cdn-images-1.medium.com_max_2000_0_QrRbrkcWAMME8DWg) **我们的订单提前反应购买方形乐高的本地应用程序，并在 ShopTalk 演示。**

## 获取您的开发人员环境设置

先决条件:

*   [Android Studio](https://developer.android.com/studio) (点击链接下载安装)

*   [Xcode](https://developer.apple.com/xcode/) (可以通过 macOS 上的 App Store 安装)

*   [广场账户](https://squareup.com/us/en/developers)(在此注册)

*   [React Native CLI](https://facebook.github.io/react-native/docs/getting-started) (遵循“使用本机代码构建项目”指南)

需要明确的是，如果你计划让你的应用程序在各自的平台上运行，并希望使用它们的模拟器进行开发，你只需要 Android Studio 或 Xcode。

## 步骤 1:安装和设置 React 本机 CLI

```
npm install -g react-native-cli 
```

Enter fullscreen mode Exit fullscreen mode

确保遵循*“使用本机代码构建项目”*的 [React Native](https://facebook.github.io/react-native/docs/getting-started) 安装指南。使用`react-native-square-in-app-payments`插件需要应用内支付 SDK，这是 iOS 和 Android 的原生代码。此外，遵循该指南的一部分要求您安装 React Native CLI(上面的命令),这有助于在开发时方便链接库和运行模拟器。

## 第二步:将应用内支付 React 原生插件添加到项目中

设置 React Native 后，您需要遵循 Square [指南将应用内支付添加到 React Native 项目](https://github.com/square/in-app-payments-react-native-plugin/blob/master/docs/get-started.md)。如果你是从零开始，你可能想看一看[快速入门示例应用](https://github.com/square/in-app-payments-react-native-plugin/tree/master/react-native-in-app-payments-quickstart)，它展示了一个允许用户购买 cookie 的示例应用。您也可以下载示例应用程序，然后在那里进行修改。

[![*Quick-start App for React Native In-App Payments Plugin.*](img/68df6c6dac49b47351a9f193d4ca73b9.png)](//images.ctfassets.net/1wryd5vd9xez/2eV6kGyfWhXWr6ahT7k2B8/c320ba23d3cb5acc36c5e7cad09ad576/https___cdn-images-1.medium.com_max_2400_0_EjKx-XdCp-zNW1dG)**React 原生应用内支付插件快速入门应用。**

## 使用应用内支付 SDK 进行 React 本地开发需要了解的事项

### 为应用内支付 SDK 提供本地接口

**SQIPCore** —用于在 React 本地应用中初始化应用内支付 SDK。

**SQIPCardEntry** —处理标准信用卡表单捕获。值得注意的是，如果你想为你的用户在文件中存储一张[卡，那么你只需要使用这个接口，因为你不能使用数字钱包存储卡的详细信息。](https://docs.connect.squareup.com/payments/transactions/cookbook/save-cards-on-file)

**SQIPApplePay** —虽然名字相当简单，但这个接口是用来处理 ApplePay 流程的。

SQIPGooglePay —与 Apply Pay 接口相同，但用于处理 GooglePay。

每个接口都有一些方法来启动流、处理错误或用户关闭表单，以及完成授权以获得一个 nonce(一次性使用的令牌)。您仍然需要有一个后端实现来使用 nonce 来将卡[存储在客户档案](https://docs.connect.squareup.com/more-apis/customers/cookbook/save-cards-on-file)中，或者[处理交易](https://docs.connect.squareup.com/payments/transactions/overview)。您可以在上的 [Square 文档](https://docs.connect.squareup.com/payments/in-app-payments-sdk/how-it-works)中找到更多关于这个流程如何工作的信息。

## 路由/导航

尽管这可能会因您使用的库而异，但还是有必要解释一下我们在示例中使用的库。 [React 导航](https://reactnavigation.org/)是 React 原生应用中路由和导航的常用库。

可以通过运行:
来添加

```
npm install — save react-navigation react-native-gesture-handler 
```

Enter fullscreen mode Exit fullscreen mode

```
react-native link react-native-gesture-handler 
```

Enter fullscreen mode Exit fullscreen mode

导航库的基本前提是在 React 本机应用程序的根目录下创建一个中心枢纽，可以控制在任何给定时间应该显示哪个“屏幕”。这个库有几种不同类型的导航，但是我们坚持使用[堆栈导航器](https://reactnavigation.org/docs/en/hello-react-navigation.html)。它的工作方式就像一个堆栈数据结构，让每个屏幕“继续”到堆栈，当用户返回时，它只是将它们弹出堆栈。

## 订单提前示例应用

为了*(如此微不足道)*展示 React 原生应用内支付插件可以做什么，我们创建了一个应用程序，让人们在会议上挑选自己的方形乐高人，并演示新的[订单推送测试版](https://medium.com/p/orders-push-public-beta-25bda7c31521)如何将其推入方形销售点(POS)。

在我们应用的基础上，我们使用来自 [React 导航](https://reactnavigation.org/)的`createAppContainer`和`createStackNavigator`来包装我们的 React 应用并处理我们所有的路线和导航。这也是我们在`componentDidMount()`生命周期方法中使用`SQIPCore`初始化应用内支付 SDK 的地方。

```
import React, {Component} from 'react';
import {
 createStackNavigator,
 createAppContainer
} from 'react-navigation';
import HomeScreen from './screens/Home';
import CheckoutScreen from './screens/Checkout';
import {
 SQIPCore,
} from 'react-native-square-in-app-payments';

const AppNavigator = createStackNavigator({
 Home: HomeScreen,
 Checkout: CheckoutScreen
}, {
 initialRouteName: "Home"
});

const AppContainer = createAppContainer(AppNavigator);

export default class App extends Component {
 async componentDidMount() {
   await SQIPCore.setSquareApplicationId('YOUR_APP_ID');
 }
 render() {
   return <AppContainer />;
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们通过只有两个屏幕来保持简单。主屏幕显示我们所有的产品(在这种情况下，乐高人)，另一个屏幕是我们的结帐。

应用程序中的许多代码都致力于设计组件的样式，这可能是它自己的博客文章。从这里可以学到的关键部分是如何与应用内支付 SDK 进行交互。

接下来，我们将深入到我们的 Checkout screen 组件的`componentWillMount()`方法中。这是我们设置我们的 [iOS 卡入口主题](https://github.com/square/in-app-payments-react-native-plugin/blob/master/docs/get-started.md#ios) ( [你需要在 Android](https://docs.connect.squareup.com/payments/in-app-payments-sdk/cookbook/customize-payment-form) 的 styles.xml 中设置这些)。

```
 async componentWillMount(){
   if (Platform.OS === 'ios') {
     await SQIPCardEntry.setIOSCardEntryTheme({
       saveButtonFont: {
         size: 25,
       },
       saveButtonTitle: 'Order 💳 ',
       keyboardAppearance: 'Light',
       saveButtonTextColor: {
         r: 255,
         g: 0,
         b: 125,
         a: 0.5,
       },
     });
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

然后，在启动信用卡表单流之后，我们必须创建一些生命周期方法来处理事件，并获取我们的 nonce 来处理卡的详细信息。

```
onCardEntryComplete() {
   // Update UI to notify user that the payment flow is completed
 }

 async onCardNonceRequestSuccess(cardDetails) {
   try {
     // take payment with the card details
     // await chargeCard(cardDetails);
     await fetch('YOUR_BACKEND_URL', {
       method: 'POST',
       headers: {
         Accept: 'application/json',
         "Content-Type": "application/json"
       },
       body: JSON.stringify({
         nonce: cardDetails.nonce,
         item: this.state.cartItem.item_data,
         customer: this.state.customer
       })
     }).then((resp)=>{
       // Handle resp
     })

     // payment finished successfully
     // you must call this method to close card entry
     console.log(cardDetails);
     await SQIPCardEntry.completeCardEntry(
       this.onCardEntryComplete(),
     );
   } catch (ex) {
     // payment failed to complete due to error
     // notify card entry to show processing error
     await SQIPCardEntry.showCardNonceProcessingError(ex.message);
   }
 }

 onCardEntryCancel() {
   // Handle the cancel callback
 }
 async onStartCardEntry() {
   const cardEntryConfig = {
     collectPostalCode: true,
   };
   await SQIPCardEntry.startCardEntryFlow(
     cardEntryConfig,
     this.onCardNonceRequestSuccess,
     this.onCardEntryCancel,
   );
 } 
```

Enter fullscreen mode Exit fullscreen mode

为了分解这一点，我们开始卡流的基本方法是`onStartCardEntry()`方法。然后我们有`onCardNonceRequestSuccess`、`onCardEntryCancel`和`onCardEntryComplete`来处理流程中的不同事件。

`onCardNonceRequestSuccess` —当我们使用应用内支付 SDK 成功请求 nonce 时进行处理，以便我们可以将其发送到后端进行进一步处理。

`onCardEntryCancel` —应用于处理用户在填写卡条目表单并触发卡随机数响应之前是否关闭该表单。

`onCardEntryComplete` —用于关闭表单，但也可用于处理应用程序的任何状态更新。

[![The React Native Order Ahead App in action.](img/69538447cbfaa434ed7f3acf2aa16887.png)](//images.ctfassets.net/1wryd5vd9xez/2owUvWGUichot39kd9a7ge/6e828cb619df6bbf19e2cd8798a144dd/https___cdn-images-1.medium.com_max_2000_1_prFcvMdGncCv4nS7QP8Yyg.gif)*React Native Order Ahead App 在行动。*

现在，就我们的前端而言(在我们的 React 本机应用程序中)，这就是我们处理支付所需的全部内容。该应用程序应该只关心使用应用内支付 SDK 来安全地捕获这些卡的详细信息，获得随机数，将其传递到后端进行进一步处理，然后*对处理的结果做出* -ing *(再次，如此双关)*。

此外，需要明确的是，这只是在 React 原生应用中实现[应用内支付 SDK 插件](https://github.com/square/in-app-payments-react-native-plugin)的*种*方式。你当然也可以添加对 Google Pay 和/或 Apple Pay 的数字钱包支持，这只是集中在演示卡流。

我们其余的创建订单和将订单推送到 Square POS、[收取交易费用](https://docs.connect.squareup.com/payments/transactions/overview)(接受付款)和/或[存储客户卡详细信息](https://docs.connect.squareup.com/payments/transactions/cookbook/save-cards-on-file)的功能将发生在您的后端。如果你有兴趣为我们的[订单推送测试版](https://medium.com/p/orders-push-public-beta-25bda7c31521)和[文件交易卡](https://docs.connect.squareup.com/payments/transactions/cookbook)开发自己的应用程序，你可以点击链接了解更多信息，或者[加入我们的 Slack 社区](http://squ.re/slack)寻求帮助。

如果你计划使用我们的 React 原生应用内支付插件在 Square 上开发一些东西，并想写点什么*(或任何与 Square 相关的东西)*，请加入我们的 [Slack 社区](http://squ.re/slack)并让我们知道*(你也可以加入进来打个招呼)*，我们总是很乐意谈论你正在开发的任何东西。

如果您想及时了解我们的其他内容，请务必关注这个[博客](https://medium.com/square-corner-blog) &我们的[推特](https://twitter.com/SquareDev)账户，并注册我们的[开发者简讯](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog)！我们还有一个 Slack 社区，用于与其他实现 Square APIs 的开发者联系和交流。