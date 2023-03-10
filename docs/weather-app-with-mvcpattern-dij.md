# 带有“mvc_pattern”的天气 App

> 原文：<https://dev.to/andrious/weather-app-with-mvcpattern-dij>

*使用示例阻塞*

<figure>[![](img/ae78f2ad434ef499f858dd74558f1662.png)](https://medium.com/@greg.perry) 

<figcaption>[格雷格佩里其他故事](https://medium.com/@greg.perry)</figcaption>

</figure>

2019 年 2 月 9 日，[菲利克斯·安杰洛夫](https://medium.com/@felangelov)写了一篇关于 Flutter 包的精彩文章， [flutter_bloc](https://pub.dartlang.org/packages/flutter_bloc) ，演示了如何管理多个“业务逻辑组件”(或 bloc)来实现动态主题化、拉至刷新等等。这个包帮助实现了 [**阻塞模式**](https://www.didierboelens.com/2018/08/reactive-programming---streams---bloc/) ，本文用一个简单的天气应用程序演示了这一点。

该文章可从以下网址获得:

<figure>[![](img/271a5e6ffa26ca41c43656ab4ef65129.png)](https://medium.com/flutter-community/weather-app-with-flutter-bloc-e24a7253340d) 

<figcaption>[天气 App 带【颤振 _ bloc】](https://medium.com/flutter-community/weather-app-with-flutter-bloc-e24a7253340d)</figcaption>

</figure>

现在，Felix 在本文中的重点是管理应用程序中的多个块。因此，这个天气应用程序有一个区块负责获取天气信息，另一个区块负责提供“主题”或配色方案以匹配特定的预报，最后还有一个区块涉及应用程序的设置:在华氏和摄氏之间切换温度。事实上，这样的要求并没有充分利用集团所能提供的优势。

然而，它确实考虑到了另一种方法。因为该应用程序涉及大量的用户交互，包括获取数据、动态主题化、拉至刷新等。所有这些都是由用户单独发起的，对于这样一个应用来说，流媒体并不一定是一个重要的特性，没有流媒体也可以很容易地完成。例如，这样的应用可以用 Flutter 包来完成， [mvc_pattern](https://pub.dartlang.org/packages/mvc_pattern) 。

这不是对集团的反映。远非如此。对应用程序的规格做了一个改动。例如，如果需要“实时”天气预报，我建议你已经用 Bloc 实现了一半。一款需要间断(如果不是持续)传输变量数据的应用程序正处于 Bloc 的掌控之中。然而，根据应用程序示例的当前规范，并在 Felix 的允许下，我编写了完全相同的天气应用程序，公然使用了他的示例应用程序的大部分现有代码来传达另一种方法。

你看，有些开发人员，今天，可能没有 MVC 的个人经验。反正不是直接的。毕竟，这是一个 40 岁的设计模式。然而，他们可能间接地接触到了 MVC，因为今天的许多设计模式都是它的后代。本文试图使用一个 Bloc 示例作为参考来进一步解释 MVC。它们的实现可能有所不同，但是它们遵循的一般概念是相同的。

我们开始吧！

### 一些现成的假设

一开始，我假设读者对 Bloc 和 MVC 有些熟悉。然而，同样，由于我的目标受众是那些对 MVC 经验很少或没有经验的开发人员，我将只建议，此时此地，首先阅读解释我对在 Flutter 包中实现 MVC 的解释的文章， [mvc_pattern](https://pub.dartlang.org/packages/mvc_pattern) :

<figure>[![](img/4b9b502c0ef4ac6b56d777a97dc8002e.png)](https://dev.to/andrious/flutter--mvc-at-last-2jd3) 

<figcaption>[终于扑+ MVC 了！](https://dev.to/andrious/flutter--mvc-at-last-2jd3)</figcaption>

</figure>

嘿！你回来了！太好了。

### 点击即可

在我们继续之前，再补充一点意见。我将在本文中展示一些截图。与嵌入式 gists 不同，截图允许我显示感兴趣的代码片段。在我看来也更容易阅读。然而，与往常一样，读者可以点击屏幕截图，然后被导向 gist 版本或 Github 上的代码，例如，如果他们愿意，可以轻松复制代码。截图标题也是 Github 或 Gist 源代码的链接。

### 相同的看法

就像 flutter 包使用的 Bloc 实现一样， [flutter_bloc](https://pub.dartlang.org/packages/flutter_bloc) ，状态对象的 **build()** 函数就像 Flutter 包中 MVC 的‘视图’方面， [mvc_pattern](https://pub.dartlang.org/packages/mvc_pattern) 。然后，这种 **build()** 函数会穿插一个或多个“业务逻辑组件”(无论是块还是控制器)的实例。正是它们提供了要在 **build()** 函数中显示的“数据”。

查看两个应用程序版本的“主视图”或主页(这是一个 StatefulWidget。一个叫 WeatherHome 另一个分别命名为 Weather)，您可以看到 Felix 和我都决定在 StateWidgets 的函数中实例化我们各自的“业务逻辑组件”。

<figure>[![](img/e544f6bafb7263263a9cf54855744c59.png)](https://github.com/Andrious/weather/tree/master/lib/src/app/view) 

<figcaption>[类天气](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather_home.dart#L34)家在 weather_home.dart | [类天气](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather.dart#L101)在 weather.dart</figcaption>

</figure>

### 并排

进一步观察各自的 StatefulWidgets，两个 **build()** 函数实际上都点缀着它们的“业务逻辑组件”实例，负责响应所有的用户交互并控制随后显示的结果。并排看两张截图，你会发现它们的实现可能不同，但结果是一样的。注意，我不会“深入细节”描述它们在这些 **build()** 函数中各自的实现——这超出了本文的范围。

<figure>[![](img/4dd195c6ffd0c3146277527b69fa349a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hgJERtmg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AZ2NaU1GVgja-nByP5WrNjQ.png)

<figcaption>[build()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather_home.dart#L55)in weather _ home . dart |[build()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather.dart#L123)in weather . dart</figcaption>

</figure>

### 回到我们的主题

此外，这两个版本都利用了 MaterialApp 类的“theme”属性。该类在分别为两个应用程序创建的第一个小部件中被实例化。在 Bloc 中，它被称为 App。在 MVC 中，它被称为 WeatherApp。但是名字里有什么呢？在这两个版本中，我们接着分别调用它们各自的“home”小部件——类 WeatherHome 和类 Weather。上面那些你已经看过了。

### **业务逻辑组件数二！**

正是在这里，在这些“应用程序”类中，向您介绍了每个应用程序中使用的第二个块和控制器。两者都负责提供“主题”,以伴随从上面介绍的第一对中检索到的下一个天气预报——当在应用程序中选择一个城市位置时。用红色小箭头突出显示，你可以在下面看到每一个都被注入到 Flutter 自己的本地框架中，让每一个都可以访问它们各自的状态对象。第二组红色小箭头显示了属性 theme 在哪里被赋予了各自的值。

<figure>[![](img/c28a32fc6140a535dc5a31a4c67f9565.png)](https://github.com/Andrious/weather/tree/master/lib/src/app) 

<figcaption>[类 App](https://github.com/Andrious/weather/blob/master/lib/src/app/controller/app.dart#L35) in app.dart | [类 weather App](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather.dart#L81)in weather . dart</figcaption>

</figure>

### 设置您的温度

Felix 示例中的第三个也是最后一个块涉及到确定温度是以华氏温度还是摄氏温度显示。下面你可以看到每个版本都在检索和应用当前的设置。这个简单的天气应用程序的默认设置是摄氏度。

<figure>[![](img/9d9a1509e2d8e1149c913979f593cbd0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UFxZ1BgM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Avr4uHoo_w0ksS-P-iMc4Yg.png)

<figcaption>[build()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/combined_weather_temperature.dart#L48)in combined _ weather _ temperature . dart |[build()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather_temperature.dart#L48)in weather _ temperature . dart</figcaption>

T10】</figure>

### 没有要设置的设置

注意，当我从 Github 中检索示例代码时，我没有发现应用程序的设置被进一步实现。就这样，我很随意地做了一个简单的抽屉，上面有一个拨动开关，可以在摄氏和华氏之间切换。

<figure>[![](img/0a91c412bd693a25bddba2eeb880e86b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3LlZdfrb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AhmUOgeHXMHzRTYuoWU2wzg.png) 

<figcaption>设置选项</figcaption>

</figure>

### 设置您的温度

提供这样一个菜单允许我在 block 版本中实现第三个也是最后一个 block，并在另一个版本中实现它的控制器对应物。因此，下面是两个版本，用于向用户提供一个拨动开关，如果他们想将温度读数从摄氏温度改为华氏温度或再改回来。同样，用红色小箭头突出显示的是在两个版本中检索当前单位设置的位置，单位被切换的位置，以及应用程序屏幕被“刷新”以显示新温度读数的位置。

<figure>[![](img/fa34a77cad4a2635cfd9b71939ff859a.png)](https://github.com/Andrious/weather/tree/master/lib/src/app/view)

<figcaption>[settings drawer()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather_home.dart#L59)in weather _ home . dart |[settings drawer()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather.dart#L126)in weather . dart</figcaption>

</figure>

<figure>[![](img/8e58f7ff18b67ac7ecdba1031344ef61.png)](https://github.com/Andrious/weather/blob/master/lib/src/app/view/settings_drawer.dart#L72) 

<figcaption>[设置中的阶级切换器](https://github.com/Andrious/weather/blob/master/lib/src/app/view/settings_drawer.dart#L72)_ drawer . dart</figcaption>

</figure>

注意，Switcher 类是我自己创建的——向抽屉传递两个特定的值。

### 回调

回到每个应用程序中使用的第二个块和控制器——同样，它们为每个新的预测提供主题。两者实际上都回调了它们最初关联的状态对象。特别地，两者都调用它们状态对象的 **setState()** 函数。让我们看看他们在哪里这样做。

<figure>[![](img/5dc6a39cf263a460d54d76d5ac7fc696.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JV27o85c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/978/1%2Au0BFnWT9jFGw7k5PkiRxEQ.png) 

<figcaption>不同的主题进行不同的预测。</figcaption>

</figure>

### 开始

在应用程序开始时，将第一个 StatefulWidget 传递给 **runApp()** 函数，块和控制器被实例化并与状态对象“关联”。它们是第一个被实例化的，但也是本文介绍的第二个:Theme 块和 Theme 控制器。

<figure>[![](img/e0e5f9f0da8aac6ee6337d7b9955f12e.png)](https://github.com/Andrious/weather/blob/master/lib/main.dart)

<figcaption>[main()](https://github.com/Andrious/weather/blob/master/lib/main.dart#L34)in main . dart |[main()](https://github.com/Andrious/weather/blob/master/lib/main.dart#L44)in main . dart</figcaption>

</figure>

下面是传递给 **runApp()** 函数的 StatefulWidgets。同样，您可以看到各个块和控制器与它们的状态对象“链接”的位置。对于 Bloc 版本，类对象 BlocBuilder 本身就是一个 StatefulWidget。它是 StatefulWidget 与 BLoC 类 ThemeBloc 相关联的状态对象。MVC 版本只是将它的控制器提供给遇到的第一个状态对象 _WeatherAppState。

<figure>[![](img/0af62c4bfb6ca6e0ed416d193de71eb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n4rxSInO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A_jxt7RptKR3EN44giiIj5A.png) 

<figcaption>[类 App](https://github.com/Andrious/weather/blob/master/lib/src/app/controller/app.dart#L35) in app.dart | [类 weather App](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather.dart#L81)in weather . App</figcaption>

</figure>

### 设置状态

在这两个版本中，你可以在下面的第一对截图中看到它们各自的“天气变化”事件是从哪里开始的。因此，您可以在下面的第二组屏幕截图中看到，函数 **setState()** 在各自的样板文件中被调用。换句话说，两种方法都有适当的机制，让它们各自的“业务逻辑组件”调用它们相关的状态对象来重建它们的小部件树。

<figure>[![](img/f4b07ea17d8b9f5da7d364f0c513fb22.png)](https://github.com/Andrious/weather/tree/master/lib/src/app/view)

<figcaption>[build()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather_home.dart#L55)in weather _ home . dart |[_ subscribe()](https://github.com/felangel/bloc/blob/b6a0914a7a6116f6c51dbc0cb64dc054d5fa541d/packages/flutter_bloc/lib/src/bloc_builder.dart#L81)in bloc _ builder . dart |[build()](https://github.com/Andrious/weather/blob/master/lib/src/app/view/weather.dart#L123)in weather . dart |[refresh()](https://github.com/AndriousSolutions/mvc_pattern/blob/aceb3d06fe4d4f5e778e2daf9d94bf05e5b1ca62/lib/mvc_pattern.dart#L728)in MVC _ pattern . dart</figcaption>

</figure>

### 代码举例

这个 [Github 库](https://github.com/Andrious/weather)将使用这种替代方法实现天气应用。事实上，正如你可能已经意识到的那样，Felix 的天气应用程序示例代码也在那里——我最初在 2019 年 2 月 11 日星期一检索的代码。转到文件 [main.dart](https://github.com/Andrious/weather/blob/master/lib/main.dart) ，如果你愿意的话，你可以在这两种方法之间切换。

<figure>[![](img/b8d303ccc897e87e6dc6bb8637cc52e8.png)](https://github.com/Andrious/weather/blob/master/lib/main.dart)

<figcaption>[main . dart on Github](https://github.com/Andrious/weather/blob/master/lib/main.dart)</figcaption>

</figure>

### 这不仅仅是代码，还是一种想法

本文确实提供了一种替代方法，但是它们背后的思想是相同的。Bloc 如果不是更好的话，至少也是一样的……特别是它的“实时”特性。这里的主要信息是，通过阅读本文，您可以看到 Bloc 遵循 MVC 设计模式，明确地努力将业务逻辑从表示层和数据层中分离出来。就像在生活中一样，永远不会只有一个答案。永远不要只有一种方法。外面有很多选择。开发人员应该始终了解并接受这些选项。

干杯。