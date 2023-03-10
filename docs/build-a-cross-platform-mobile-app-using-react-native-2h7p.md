# 使用 React Native 构建跨平台移动应用

> 原文：<https://dev.to/ibmdeveloper/build-a-cross-platform-mobile-app-using-react-native-2h7p>

作为我在 IBM 开发人员宣传工作的一部分，我经常构建和共享演示来展示我们的产品以及开发人员如何将它们集成到自己的应用程序中。

[IBM Developer](https://developer.ibm.com/) 站点为专注于代码、内容和社区的开发人员提供了丰富的资源。代码模式是我们为开发人员在工作中经常遇到的常见问题提供完整解决方案的方式。这些[代码模式](https://developer.ibm.com/patterns/)“利用多种技术来解决我们的开发人员提倡的被认为是跨多个行业的常见用例的问题。”

我最近完成了一个新的代码模式，展示了如何使用 React Native 框架[创建一个跨平台的移动应用程序。](https://ibm.biz/BdzRGW)

[![](img/2a9018d8b1c8e5f667acd064effc706d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dIVObrk2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/974/0%2ALlyOdKvLfQzefBqV.png)

我构建的模式使用 [Watson Discovery News](https://www.ibm.com/watson/services/discovery-news/) 和 [React Native](https://facebook.github.io/react-native/) 来构建一个跨平台的移动应用程序，获取特定公司的最新新闻。React Native 是一个 JavaScript 框架，可以让你为 iOS 和 Android 编写移动应用。该应用程序使用 Watson Discovery News 获取最近的相关新闻文章以及情感、关键词和相关概念。该演示应用程序易于定制，为在您自己的 React 本机应用程序中使用 Watson Discovery 提供了一个方便的起点。不用学习 Swift、Java 或 Kotlin 来构建原生移动应用，您可以使用您的 JavaScript 知识快速创建应用。

React Native 使用与 [React](https://reactjs.org/) 相同的设计，允许您从声明性组件构建丰富的移动 UI。要想对 React Native 和只用 JavaScript 构建移动应用有一个很好的概述，可以看看文章[剖析 React Native](https://ibm.biz/BdzRnc) 。如果你有兴趣深入 React Native，我强烈推荐斯蒂芬·格里德的 Udemy 课程[《React Native 和 Redux 完全教程](https://www.udemy.com/the-complete-react-native-and-redux-course/)。

[![](img/b4926710ce44e7b798c42f41e475577d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kMNDKnaY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AhBTM4lTtdm8FXRWD.png)

如果你不熟悉这项服务， [IBM Watson Discovery](https://www.ibm.com/watson/developercloud/discovery.html) 是一个认知搜索和内容分析引擎，用于应用程序识别模式、趋势和可操作的见解。使用 Discovery，开发人员可以使用自然语言理解从返回的内容中提取元数据，如概念、关键字和情感。[沃森探索新闻](https://console.bluemix.net/docs/services/discovery/watson-discovery-news.html#watson-discovery-news)是一个索引数据集，预先丰富了自然语言处理(NLP)功能，如关键词、实体、语义角色、情感、关系和类别分类。因此，开发人员不用使用两个产品(沃森探索和[自然语言理解](https://www.ibm.com/watson/services/natural-language-understanding/)，而是可以制作一个 API 来获得组合结果。

即使您没有 JavaScript 专业知识，也很容易上手并试用该应用程序。只需克隆 repo，注册一个免费的 IBM Cloud 帐户，然后用您自己的帐户更新发现凭据。分分钟就能拥有自己的跨平台移动新闻 app 运行。

你可以在 Github [上找到完整的回购协议。如果你以此作为你申请的起点，请告诉我！](https://github.com/IBM/build-react-native-app-for-watson-discovery)

*原载于 2019 年 3 月 5 日*[*【tmarkiewicz.com】*](https://tmarkiewicz.com/build-a-cross-platform-mobile-app-using-react-native/)*。*