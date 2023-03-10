# 颤振的设计模式

> 原文：<https://dev.to/andrious/a-design-pattern-for-flutter-2iie>

### 颤振 MVC

*近距离观察颤振应用的设计模式*

在我的上一篇文章中，我看了一下在[菲利克斯·安杰洛夫](https://medium.com/@felangelov)的故事中首次介绍的示例应用程序，带有“flutter _ bloc”的[天气应用程序，并将其从使用 Bloc 转移到使用更 MVC 的方法。下面是那篇文章，供你参考。](https://medium.com/flutter-community/weather-app-with-flutter-bloc-e24a7253340d)

<figure>[![](img/4a374ff4d301a82ebcae0e49007972ce.png)](https://medium.com/flutter-community/mvc-weather-app-78feaa616b12) 

<figcaption>[天气 App 带【MVC _ pattern】](https://medium.com/flutter-community/mvc-weather-app-78feaa616b12)</figcaption>

</figure>

在这篇文章中，我将更深入地研究 MVC 方法。这就像是在 BloC 和 Redux 的“引擎盖下寻找”。在这种情况下，我们将进入 MVC 框架的杂草中，因为我已经实现了它。因为它会，它会变得有点复杂。对于一些人来说，你的眼睛可能会呆滞，但希望使用相同的样本应用程序将有助于我们继续前进。这将是一篇很长的文章——先冲杯咖啡。

<figure>[![](img/9abae817f8c64f68a1c9cb5473100fad.png)](https://medium.com/@greg.perry) 

<figcaption>[格雷格佩里其他故事](https://medium.com/@greg.perry)</figcaption>

</figure>

### MVC 方式

MVC 设计模式是分离软件应用程序的三个方面的明确努力。在这种情况下，模型-视图-控制器描述了应用程序的数据(模型)与应用程序的界面(视图)和应用程序的逻辑(控制器)的分离。如果你还没有，有一篇文章解释了我对 MVC 的理解，因为它是在 Flutter 包中实现的， [mvc_pattern](https://pub.dartlang.org/packages/mvc_pattern) :

<figure>[![](img/4b9b502c0ef4ac6b56d777a97dc8002e.png)](https://dev.to/andrious/flutter--mvc-at-last-2jd3) 

<figcaption>[终于扑+ MVC 了！](https://dev.to/andrious/flutter--mvc-at-last-2jd3)</figcaption>

</figure>

### 一个 MVC 例子

现在在这篇文章中,“样本应用”的三个方面被进一步分离——合并和集成了 Flutter 自己的 API 和框架。这个示例应用有自己的 Github 知识库，名为 [Weathercast](https://github.com/Andrious/weathercast) ，也可以在 [Google Play](https://play.google.com/store/apps/details?id=com.andrioussolutions.weathercast) 上下载。

### 设计模式提供了结构

设计模式提供了结构。它们提供了一种组织代码的方法。希望这样做可以让熟悉设计模式的开发人员在被分配到一个项目时“立即进入角色”。我们将会看到设计模式是如何帮助组织你的代码，你的文件，甚至是你的目录来组成你的项目的。

本文不仅会建议一个结构，还会建议在开发 Flutter 应用程序时要执行的具体编程实践。请注意，这里所有的建议都与标准相去甚远。这些是我做的事情——你可能会发现这样做也很有用。最后，你做你该做的。

### 一个 MVC 目录结构

在这个示例应用程序中，MVC 设计模式甚至体现在它的目录结构中。查看下面的三个截图，您看到的是存储示例应用程序的目录结构。每个截图都是前一个，但是子目录打开得更远。请注意，有些文件甚至是目录名为，模型，视图和控制器。因此，即使只看一眼，您就知道什么文件和什么目录中有什么“类型”的代码。不错，不是吗？

[![](img/c346f3f5c44a6be42a58cb19839bd188.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0CyogIqm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAYn-lNRp5ipPFqTvq0g7vQ.png)

### 导出 MVC

注意，在上面的最后两个截图中，三个库文件 model.dart、view.dart 和 controller.dart 用红色小箭头突出显示。下面列出了它们，您会发现每个都包含一个导出语句列表。每一个都代表了 MVC 设计模式的三个方面。有了这些，你就可以猜测库文件 controller.dart 中包含了什么。

<figure>[![](img/887fa17c6f746c89a21feec1d8e0c1fd.png)](https://github.com/Andrious/weathercast/tree/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src) 

<figcaption>[控制器.镖](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/controller.dart)和[模型.镖](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/model.dart)和[视图.镖](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/view.dart)</figcaption>

</figure>

库文件 controller.dart 包含应用程序的所有“控制器”代码。你会问，这一切背后的想法是什么。当[组织一个库包](https://www.dartlang.org/guides/libraries/create-library-packages#organizing-a-library-package)时，这种方法受到 Dart 文档本身的启发，有助于加速开发阶段。我会解释的。

### 一切都各就各位

因此，位于“控制器”目录下的所有源代码都列在库文件 controller.dart 中。在此应用程序中被视为“视图”一部分的所有源代码都可以在库文件 view.dart 中找到。最后，文件 model.dart 包含对与应用程序数据相关的所有代码的引用。这是以这种方式组织代码以加速开发过程的实践的一部分。毕竟时间就是金钱。

### 继续吧

例如，您可以看到上面列出的三个文件现在在下面截图中用三个红色箭头突出显示的导入语句中引用。在开发过程中，在创建“新的 Dart 文件”后立即自动提供这三个导入语句的做法可以确保您作为开发人员拥有继续工作所需的所有依赖项。在开发接近尾声时，我会加入“show”指令以供将来参考(第二个截图)。有了它，几周、几个月、几年后，我或另一个开发人员就可以回到这个库文件，并很容易看到相关的依赖关系。不错，不是吗？

<figure>[![](img/1cf86ebaea55798beb7756d2d621e6a5.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/home.dart#L32) 

<figcaption>[类风标](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/home.dart#L32)</figcaption>

</figure>

不可否认，对于较大的应用程序，这种做法被证明是不切实际的，但是，对于简单的应用程序，想法是使用这三个导入文件来快速收集所需的依赖关系。时间就是金钱。

### 一个扑动的目录结构

请注意，在应用程序的目录结构中，有一个名为 home 的目录。这告诉你,“Home”小部件的代码在哪里。你可能知道，在大多数情况下,“主页”窗口通常是移动应用的主屏幕。它以类 MaterialApp 中的属性命名。

[![](img/4cd9237f3aa08df51f2791f5ecaf4710.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4i5QP30g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AsDm8WnfjC7St1w1bnA88tQ.png)

### MVC 内的 MVC

请注意，在最后一个屏幕截图中，在主目录下，有另一组目录，分别名为 model、view 和 controller。它们都属于“主页”小部件。猜猜每个目录下都有什么？明白我的意思了吗？一眼就能看出什么代码做什么。

[![](img/f4c9600349b72f03c579a33164a11c0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LYAg610h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AAfVmHfBrojqHhTj0b1MEFw.png)

### 又来了，一切都各就各位

一眼看去，您可以看到在“Home”小部件中有一个抽屉小部件。看上面最后一张截图。看到抽屉代码在哪里了吗？很符合逻辑的地方，对吧？那么，我想知道“Home”小部件访问的数据在哪里？目录下，当然是型号。

### 开始

所以让我们后退一点，问一下这个应用程序中的“Home”小部件在哪里？和大多数 Flutter 应用一样，你可以猜测它被传递给了某个 MaterialApp 小部件的“home”属性。但是在哪里？看下面，你就会知道在哪里。

<figure>[![](img/b7aaf4300e0d62ba2678459b5b83724d.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

### 这都是什么？

所以有 home 属性被传递给了 class 对象，Weather。这个班在哪里，天气？这个班在哪里，WeatherApp？这个扩展 WeatherApp 的 AppView 类是什么？这是什么方法， **onTheme()** ？最后，这个 con.WeatherApp 传递给属性的是什么，con？这里发生了很多事。我们一步一步来。

### 沃尔多在哪里…我是说天气？

首先，这个班在哪，天气？你已经知道在哪里了。它毕竟是“主页”小部件。更具体地说，它是“主页”小部件的屏幕或界面。您知道“Home”小部件代码所在的位置，并且您知道“Home”小部件的“接口材料”在名为 view 的目录下。在那里，你会看到一个名为 home.dart 的库文件，我敢打赌它就在那里。

[![](img/22b7fbe610afdf7e9eec5eedcbe4937f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iNV6LPIS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARgPvZnc78kUUrmOvkecnrA.png)

我是对的！与 Java 不同，您不必在文件中用“main”类的名称来命名文件。您可以在该文件中拥有任意数量的类、变量和高级函数！巨大的。因此，在 home.dart 文件中，我们找到了类 Weather。下面列出了本课程的第一部分。

<figure>[![](img/36dfc3b06a110bb3fd5d649e6e43956f.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/home.dart#L46) 

<figcaption>[国内一流天气](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/home.dart#L46)</figcaption>

</figure>

顺便说一下，注意上面最后三个 import 语句。你看过之前的那些[三档。注意，一眼就可以看出这个特定的库文件所涉及的依赖关系。不错，不是吗？](https://medium.com/p/db6ccaea2413#52e2)

### 都在名字里

那么这门课在哪里呢，WeatherApp？这似乎是这个应用程序的开始。注意，类名 WeatherApp 的末尾附加了“App”一词。这应该会给你一些线索，告诉你在哪里可以找到这些代码。让我们再次看看示例应用程序的目录结构。注意这里有一个名为 app 的目录。打赌，你能猜出里面有什么。是的，三个可怕的…哦，两个目录！猜测 app 不需要访问任何数据。那很好。

[![](img/256dccdc10fb088890bb673f89a119cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7SCwf2En--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A0AtEFd0BsGpHN8N9aumZ0w.png)

### 一次又一次，万物各就其位

那么，这门课在哪里，WeatherApp？我们来看下面三张截图。好吧，我们可以解决这个问题。这是界面的一部分。因此，它应该位于名为 view 的目录下。在 view 目录中，我们看到一个名为 weatherapp.dart 的库文件。让我们把那个打开。

[![](img/752cc342b1a2ffe46c6e662ce89496a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tsjI9vrI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ANbUxy7SsC_NqB4MQUBomog.png)

这太容易了。我们很快就开始了解这款应用了，不是吗？好像是故意的一样！看看下面的课程，WeatherApp。看看最后两条 import 语句。又是我们的老朋友——除了模特。一眼就能看出这个库文件中使用了什么以及它们的位置。

请注意，在最后一条 import 语句中使用了“as”指令。当两个类同名时，这是 Dart 处理这种情况的方式。

<figure>[![](img/016f6f6bd7e99801229f0e0ce5e74227.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)

<figcaption>[weather app . dart 中的类 weather app](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

### 开始？

这是应用程序的开始吗？我们来看看库文件 main.dart 怎么样，按照惯例，这是大多数 Flutter 应用的开始，因为它包含了函数， **main()** 。

<figure>[![](img/9e853431ab6fd5ee6f3e4639f660ffa5.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/main.dart#L24) 

<figcaption>[主镖](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/main.dart#L24)</figcaption>

</figure>

### App 层

你看到了什么？你会看到一个名为 App 的类从库包 [mvc_application](https://medium.com/flutter-community/flutter-faster-db1e0fef57ba#5340) 中被实例化。那些熟悉我过去文章的人可能会认为这是我即将发布的开发 Flutter 应用程序的[包的一部分。](https://medium.com/flutter-community/flutter-faster-db1e0fef57ba)

您还可以看到，我们的类 WeatherApp 在三个“导出”文件之一 view.dart 中被引用。那么， **runApp()** 接受什么方法来启动这个应用程序呢？我们知道函数 **runApp()** 接受一个小部件，因此我们必须假设 App 对象是一个小部件。下面是类 App 的截图，然后是它的父类 AppMVC 的截图。

<figure>[![](img/968e42ba4dd261dd25c01902a2a2ac31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yLl3ZCFY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ARXTDLn3OVLHJBR3Wr7SwWQ.png) 

<figcaption>[类 App](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L121)[类 AppMVC](https://github.com/AndriousSolutions/mvc_pattern/blob/708ec70defb6648700ef52c33a5447df67ef612b/lib/mvc_pattern.dart#L1109)</figcaption>

</figure>

是啊，原来这是个有状态的小工具。简而言之，这个' App '对象使用下面的组合开始事情: **App** ( **视图** ( **控制器** ( **模型**()))。注意，第二张截图的最后两个红色箭头。这些函数， **initApp()** 和 **init()** 很快就会在本文中出现。

因此，按照这个组合，提供给 App 对象的参数必须是应用于应用程序的 MVC 方法的“视图”。在这种情况下,“视图”是类 WeatherApp，它扩展了类 AppView。顺便说一句，我们正在偷看“引擎盖下的观察”。

<figure>[![](img/54ce7d9a411a5d4793f6f903ee1469e1.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

注意，接下来，控制器以 con.WeatherApp()的形式提供给“View”类。再次跟随构图， **App** ( **视图** ( **控制器** ( **模型**()))。

### 导入控制

请注意，我不愿意在 main.dart 文件中把控制器作为参数传递，这太麻烦了。让你的代码更加模块化，在视图内部以 con.WeatherApp()的形式实例化控制器类。见下文。它碰巧与视图 WeatherApp 具有相同的类名，因此使用“as”指令来进行区分。那很好。一些一致性。

<figure>[![](img/55f98fd3009ec7702d80c8c59cbbb9c7.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

### App 的控制器

顺便说一下，这个“应用程序层”的目的是为应用程序的运行设置“环境”。下面您看到的是上面以 con.WeatherApp()的形式首次实例化的应用程序的控制器。

<figure>[![](img/82aa3c4e605684086b5df52f4f1d8d1c.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28)</figcaption>

</figure>

你可以看到这里发生了很多事情。正如它应该的那样，它毕竟是在启动时“初始化”应用程序。你可以看到它扩展了 AppController 类。还记得我之前提到的那两个函数， **initApp()** 和 **init()？**嗯，它们已经开始发挥作用了。看到他们了吗？我们一会儿会谈到它们的细节。

### App 的视图

让我们暂时回到应用程序的视图，也称为 WeatherApp。它扩展了 AppView 类，并首先在 main.dart 文件中进行实例化，还记得吗？

<figure>[![](img/36af3c1548e56f8341930920fec3cf00.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

### 引擎盖下的景色

看看这个类的截屏，AppView。这里可以看到它的 **build()** 函数。快看。这就是我们一直在寻找的 MaterialApp！

<figure>[![](img/c977321d0a69940a4d3fc1e607e063a7.png)](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L362) 

<figcaption>[类 AppView](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L362)</figcaption>

</figure>

在那里，我们看到 MaterialApp 类对象以类变量和或函数的形式被提供了一长串参数。这是怎么回事？你可能已经猜到了，你可以用这些变量或函数“传入”组成 MaterialApp 类的所有属性。有了这些函数，如果你愿意，你可以用新的属性值动态地“重建”部件树。事实上，这个特性将在示例应用程序中演示。

### 能保持静止就保持静止

顺便说一下，我在写这篇文章的时候注意到我犯了一个错误。如果可能的话，保持代码的静态和不可变总是好的编程实践。由于这两个类在“应用程序级别”应该只有“一个实例”，所以使用静态工厂是有意义的，因此我们刚才讨论的两个类也相应地进行了更改:

<figure>[![](img/c24143f1ef6b694205935a7ca7962eb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BTuf1Fip--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AlvcCc_iZmp36GAA_Y6Ljlw.png) 

<figcaption>[类 WeatherApp](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28) 和[类 WeatherApp](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

这超出了本文的范围，但是为了强调这种情况，这里有一些对 Joshua Bloch 的著名出版物《有效的 Java》的参考。当然，它不仅仅适用于 Java，对于任何面向对象的编程语言来说都是很好的实践。

> [第 1 项:考虑静态工厂方法而不是构造函数](https://books.google.ca/books?id=ka2VUBqHiWkC&pg=PA5&dq=%22effective+java%22+%22Consider+static+factory+methods+instead+of+constructors%22&hl=en&sa=X&ved=0ahUKEwiXnPeGx9rgAhVGgK0KHSkaA2wQ6AEIKDAA#v=onepage&q=%22effective%20java%22%20%22Consider%20static%20factory%20methods%20instead%20of%20constructors%22&f=false)
> 
> [第 15 项:最小化可变性](https://books.google.ca/books?id=ka2VUBqHiWkC&pg=PA73&dq=%22effective+java%22+%22Item+15:+Minimize+mutability%22&hl=en&sa=X&ved=0ahUKEwie1tPrxdrgAhVI3IMKHeJzBDEQ6AEIKDAA#v=onepage&q=%22effective%20java%22%20%22Item%2015%3A%20Minimize%20mutability%22&f=false)

### 抽屉里有什么？

好了，回到正题。现在，这个应用程序中使用了一个抽屉。记得吗？你怎么知道的？同样，我们在 view 目录下的目录结构中看到了它。见下文。很好。

因此，通过对目录结构施加这种命名约定，您可以很容易地看到这个特定示例应用程序的抽屉是由什么组成的。在目录 drawer 下，还有一个名为 weather_locations 的目录。在这个目录中，我们又看到了我们的老朋友:目录、控制器、模型和视图。我们也看到了一些新的东西。名为 mvc.dart 的库文件。

[![](img/3942c147a2c7b0a3741bbcae9cd33df1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3OLlNorb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AW_ZrqwloG-xPowlZ5aozRQ.png)

看上面的最后一个截图，我们可以推断 weather_locations 是抽屉内显示的另一个屏幕。这是一个屏幕，因为有一个名为 view 的目录。有一个目录叫，控制器，甚至还有一个目录叫，模型？！一定有特定于这个屏幕的数据，weather_locations？！太好了！现在，这个库文件是什么，mvc.dart？下面我们来看看。

<figure>[![](img/d830ee74fa4e8e1bd0a39d62ec4ce833.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/main.dart)

<figcaption>[MVC . dart](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/main.dart)</figcaption>

</figure>

你看看这个。它包含一个导出语句列表。仔细观察就会发现，这是构成“天气位置”屏幕的所有代码。第一行包含模型，第二行包含视图，第三行包含控制器:M-V-C。都在一个名为 mvc.dart. Fun 的文件中！看下面，你可以看到它引用的库文件和一个在抽屉里显示的“天气位置”屏幕的例子。

[![](img/36997f8bd894f30aefb44d9f9ee220ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VorIma-D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AbbAeBBYYA4gI5KRJhJDoYQ.png)

这些文件都有相同的名称，这又增加了一致性，不是吗？让我们看看那些文件里有什么。首先，我们来看看控制器的第一部分:

<figure>[![](img/e28a1e5f3ab739dc3e0a24c9bec75428.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/controller/weather_locations.dart#L37)

<figcaption>[weather _ locations . dart](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/controller/weather_locations.dart#L37)</figcaption>

T8】</figure>

### 这都在名字里

请注意控制器类的名称。它有缩写，Con，附加在末尾。开发人员会将这个类识别为控制器。(当然，你可以随便怎么称呼它，但事实就是如此。)注意导入语句。他们使用了“show”指令，这样下一个开发人员打开这个文件时就可以很容易地看到使用了什么以及它在哪里。注意，最后一个导入语句是 mvc.dart 文件。所有这一切都是试图强加一个标准，一种一致性，一种帮助下一个开发人员在这个项目移交给他们时“立即进入角色”的方法。

我们来看看模型课的第一部分。和往常一样，你可以点击下面的截图，在 Github 中查看它的完整代码。

<figure>[![](img/7b8332304e0b9bbae7e11bbf5596f3be.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/model/weather_locations.dart#L30)

<figcaption>[weather _ locations . dart](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/model/weather_locations.dart#L30)</figcaption>

T8】</figure>

import 语句一目了然地告诉您涉及到哪些类。类名告诉你这是一个模型类。注意，和控制器一样，它也使用静态工厂而不是传统的构造函数来实例化类。毕竟你只需要这些类的一个实例。现在，为了继续下去，这个视图看起来像什么？

<figure>[![](img/36bfba42c47598ca8bbe4cfe7e85e973.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/view/weather_locations.dart#L34)

<figcaption>[weather _ locations . dart](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/view/weather_locations.dart#L34)</figcaption>

T8】</figure>

查看视图，您会发现它甚至不是一个类，而是一个高级函数？！它返回类型为**列表>** 的变量 _demoItems。查看导入，您会看到该视图确实与控制器 LocationCon“对话”,并引用“MVC 模式”库包的 StateMVC 类。那么这个高级函数在 app 中的调用位置呢？

### 位置！位置！位置！

由于目录结构的排列方式，我们可以“回溯”并根据经验猜测这个高级函数在哪里被调用。整洁！

[![](img/d89774e4b6f2d9249cfd141792faccbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nPIDx--j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ac4s_zISSmPAWYWp2H3z9rw.png)

从视图 weather_loctions.dart 退一步，使用高级函数，我们仍然在抽屉目录中。现在，在目录结构中向上一级，但仍然在抽屉目录中，您看到了什么？如果你猜它在文件 settings_drawer.dart 中被调用，你就猜对了。

### 谁叫谁

下面第一个截图是文件 settings_drawer.dart，可以看到函数调用 LocationCon()。 **listLocations()** 。这个函数位于控制器类 LocationCon 中。这是有意义的，因为在这个 MVC 安排中，视图并不直接与模型“对话”。相反，您是通过控制器来访问高级功能的。查看第二个截图，您可以看到我们一直在寻找的高级功能 weatherLocations 确实是在控制器中访问的。

[![](img/74c5ab5a924164f966a2d30e7bf911ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d5OkEH4I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Aiwv5esMii2XduJAdm02gQA.jpeg)

<figure>[![](img/c27a765bcfd879d0ba01eda322aca91b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uniOa3cY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ADwOfNpQERIOpUToBDhPWrw.png) 

<figcaption>[类设置抽屉](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/settings_drawer.dart#L32)和[类位置图标](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/drawer/weather_locations/controller/weather_locations.dart#L37)</figcaption>

</figure>

### 退一步看大局

现在，让我们后退一步，看看这个示例应用程序的一个特殊特性。随着每一个天气状况的报告，一个“颜色主题”被提出来代表一种特定的“类型”的天气。给应用程序增加了一点亮点。

<figure>[![](img/d96df4c0d1cfff25b4bc139e2b0b995e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5HFMWCZz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/300/1%2ARf_W_XnGoSNkNMnj7Qnb3Q.gif) 

<figcaption>颜色主题变化</figcaption>

</figure>

### 主题是什么

为了能够“动态地”改变应用程序的颜色主题，你需要能够在包含主题的状态对象上调用 **setState()** 函数。换句话说，在它的 **build()** 函数中有 MaterialApp 类的状态对象。

在这个示例应用程序中,“主题”类是一个名为 ThemeCon 的控制器，它根据指定给某个位置的天气条件显式地改变应用程序的颜色。它控制了颜色，如果不是天气的话。

那么这个“主题控制器”在哪里呢？它位于“Home”目录下。为什么？因为它被称为【家】控制器。“家”控制器在哪里？在文件 home.dart 里找到的，跟我到目前为止？

下面是“主题控制器”中负责改变颜色主题的一段代码，以及这个文件所在位置的截图。

<figure>[![](img/ed0f27715917fa82a8225ae238552752.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/theme.dart#L46) 

<figcaption>[主题曲](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/theme.dart#L46)</figcaption>

</figure>

现在，这个“主题控制器”可能在“主页”小部件中被调用，但它改变了 MaterialApp 类的“主题”属性，我们已经知道它回到了状态对象 AppView 的“应用程序级别”。那么，如何访问应用程序的状态对象呢？当然是通过应用程序的控制器。

让我们回到应用程序的控制器 WeatherApp，它运行在“应用程序级别”。最后，我将讨论这两个函数， **initApp()** 和 **init()** 。

你可以在下面看到这个应用的控制器可以访问很多东西。有道理——它是应用的控制器！在这两个“init”函数之一中， **initApp()** ，应用程序的状态对象 stateMVC 添加了控制器 ThemeCon。看下面。

换句话说，这个名为 ThemeCon 的控制器可以访问应用程序的状态对象——可以在应用程序的视图 AppView 中找到。ThemeCon 现在可以在包含主题的状态对象中调用 **setState()** 函数。你迷路了吗？我们会后退一点点…事实上后退很多！

<figure>[![](img/582013336d81ced2510b68c0eeb51816.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28)</figcaption>

</figure>

### 一切从引擎盖下开始！

让我们从头开始。还记得传递给函数的“App”对象吗， **runApp()** ？这是一个 StatefulWidget，记得吗？这意味着它创建了一个状态对象。它创建应用程序的状态对象。StatefulWidget 及其状态对象分别调用这两个“init”函数。这都是设置运行应用程序的环境的过程的一部分。让我们看一眼。

<figure>[![](img/f606dc51ee9c3b7c1b5a0fc06a2ea9cc.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/main.dart#L24) 

<figcaption>[主镖](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/main.dart#L24)</figcaption>

</figure>

### 跟随断点

下面是我的 IDE 的屏幕截图，其中示例应用程序的执行在特定的断点处暂停。第一张截图停在 app 的状态对象的 **initState()** 函数中。查看在那里调用的 StatefulWidget 函数的名称。它也叫做， **initApp()** 。一致性。

第二个屏幕截图显示了 Statefulwidget 中的函数——您现在看到的是传递给函数的“App”对象， **runApp()** 。断点已在属于分配给应用程序视图的控制器的 **initApp()** 上停止。属性 *_vw* ，是应用程序的视图，它是 main.dart 中的 WeatherApp 类。

<figure>[![](img/458402300705552f70cfbc239b7483d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tRo9oRu_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AcPy-vrJXmkcRwP5sS82Eiw.png)

<figcaption>[Class _ AppState](https://github.com/AndriousSolutions/mvc_pattern/blob/708ec70defb6648700ef52c33a5447df67ef612b/lib/mvc_pattern.dart#L1198)和 [Class App](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L121)</figcaption>

</figure>

如果你迷路了，不要担心。我的意思是，如果你不是那种遍历代码的人，例如，试图了解其他设计模式如 Redux、Scoped Model 或 BLOC 是如何工作的，那么你可能是在这里浪费时间。我猜，你只是其中一个想完成它的人，对吗？尽管如此，我还是建议这本书里少一些样板文件。进一步阅读怎么样？你已经走了这么远。

<figure>[![](img/a41f209c4cdc2e5b658d16ffabb89ac5.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28)</figcaption>

</figure>

所以现在我们回到了 **initApp()** 函数，其中“主题控制器”被赋予了对应用程序的状态对象的访问权限， *stateMVC，*以蓝色突出显示。看到了吗？

因此，事实证明，是应用程序的状态对象的 **initState()** 函数调用了应用程序控制器中的 **initApp()** 函数。这都是设计好的。

这是执行同步操作的地方。那么异步呢？

### 等待未来！

对于任何一个计算机程序来说，在它准备好供用户使用之前，必须发生很多事情。首先要做很多“初始化”工作。这是一个简单的小样本应用程序，但它也必须先“准备好”,然后才能……准备好。让我们进一步看看这个 Flutter 应用程序是如何准备的。

<figure>[![](img/e44d7cb584591b43101c31a7846058cf.png)](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L121) 

<figcaption>[类 App](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L121)</figcaption>

</figure>

在应用程序的状态对象的 **initState()** 函数中调用 **initApp()** 后，应用程序对象触发自己的 **build()** 函数。在上面的第一个屏幕截图中，请注意“App”对象利用了 FutureBuilder 类。属性 future 被赋予一个名为 **init()** 的函数。认识这个名字吗？

这就是异步操作的处理方式。然而，这样一来，用户可能不得不等到他们完成。在那之前，用户会看到什么？当然是屏幕中央那个旋转的小圆圈。

在上面的第二个截图中，您可以看到那个 **init()** 函数的内部内容。它停在了应用程序的 View own **init()** 函数上。让我们看看下面这个 **init()** 函数中有什么。请注意，我们正在向上移动框架的层。

<figure>[![](img/b9fdd8abdadcf4a8076e8809bf899384.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--alFOGNlY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AEcEplP4EUjRILLS9DqdA7A.png) 

<figcaption>[类 AppView](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L362) 和[类 AppViewState](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L489)</figcaption>

</figure>

现在，在第一个截图中，我们看到我们在应用程序的视图中，一些“内部”的东西在它停止在父类 AppViewState 的 **init()** 函数之前完成。上面的第二个截图让我们看到了那个 **init()** 函数，我们现在要进入应用程序的控制器并运行它的 **init()** 函数。这里发生了很多事情——就像一个框架应该做的那样。

<figure>[![](img/98975c6a1af018e1a1ec42e0c9ef575b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vYFTf784--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AR453kAPzLScFk1_lk6bngw.png) 

<figcaption>[类 WeatherApp](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/controller/weatherapp.dart#L28) 和[类 AppController](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L577)</figcaption>

</figure>

现在我们回到了应用程序的控制器中。对于我们简单的小示例应用程序，它被称为 WeatherApp，它就在那里，在它的 **init()** 函数中，它实际上调用了“位置控制器”——我们最初在目录 weather_locations 中介绍的那个。记得吗？在这里，它打开的数据库列出了“Home”小部件中那个抽屉的位置。记得抽屉吗？打开数据库可能需要几微秒的时间，因此应用程序在为用户准备好之前会“等待”它完成。很好。

还有一个叫做 LocationTimer 的东西，看起来也列在那里，但是我会让你在下载示例应用程序时发现它是什么。

### 回到我们的主题！

现在让我们回到这个应用程序如何根据命令改变它的颜色主题。app 的视图有一个功能叫做， **onTheme()** 。记住，使用这样的函数允许 MaterialApp 在下次“重建”它的窗口小部件树时(即，下次调用 **setState()** 函数时)动态地改变它的属性。)都凑在一起了！

<figure>[![](img/ae3cba8776209617e1887ea5dc097d6b.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32) 

<figcaption>[类天气预报](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/app/view/weatherapp.dart#L32)</figcaption>

</figure>

查看下面列出的“主题控制器”，您会看到属性 Theme 实际上是一个 *getter* 。事实上，下面这个函数叫做**【weather changed()**，它给变量 _theme“重新赋值”。

<figure>[![](img/4adc1f7af084a72bc4fed3d5569d6f09.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/theme.dart#L30) 

<figcaption>[类 ThemeCon](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/theme.dart#L30)</figcaption>

</figure>

因此，回到应用程序的视图类，在框架内部，有一个 MaterialApp，它要么接受一个变量，要么接受一个函数(如果变量为空)。在我们的示例应用程序中，我们利用函数动态分配一个新主题。我建议“逐步”通过示例应用程序来了解我的意思。

<figure>[![](img/4fdad965844ea137a2f24a9535d5c9ce.png)](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L421) 

<figcaption>[类 AppView](https://github.com/AndriousSolutions/mvc_application/blob/f85f0aba36537c652d449e0c5c34936269bfbb6c/lib/src/controller/app.dart#L421)</figcaption>

</figure>

### 效果如何……真的

我知道这篇文章要变成小说了。然而，这里我还想介绍两件事。首先，这个框架是如何执行这个简单的小应用程序的主要功能的:获取一个城市的天气。让我们快速“演练”一下。如果你读过[菲利克斯·安杰洛夫](https://medium.com/@felangelov)的故事，[的天气应用“flutter _ bloc”](https://medium.com/flutter-community/weather-app-with-flutter-bloc-e24a7253340d)，你就知道它是怎么回事了。你输入城市名，它就会显示天气。

[![](img/5498c3090ddd611eb37c18042cea9eff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gDh4gvt5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/160/1%2AJmhzXY6L26Fm5WON4SjUFw.gif)

### 跟随代码

好了，我们开始吧。在“Home”小部件文件 home.dart 中，有一个 State 对象，它允许你点击一个放大镜并输入一个城市名来获得它的当前天气。下面(红色小箭头的正上方)有一个 IconButton 小部件，当按下它时，在一个名为 city 的变量中返回一个城市。该变量被传递给“天气控制器”见下文。

<figure>[![](img/d9aaf8b499dc96104635820157bcdef2.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/home.dart#L53)

<figcaption>[Class _ WeatherState in home . dart](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/view/home.dart#L53)</figcaption>

T8】</figure>

注意，“天气控制器”有一个名为 **onPressed()** 的函数。我尝试模仿视图代码中使用的 API，以便在控制器代码中使用。又是一致性的问题。让我们看看这会把我们带到哪里。

在下面列出的 **onPressed()** 函数中，我们看到变量 city 被传递给另一个名为 **getWeather()** 的函数。我们看到在函数 **getWeather()** 内部，有一个‘then’方法。函数 **rebuild()** 就是在这个‘then’方法中被调用的。最后，“主题控制器”进入画面调用它的功能， **weatherChanged()** 。还记得那个功能吗？

<figure>[![](img/0749aa9e15c5df55f42c102cbf3e490c.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/home.dart#L109) 

<figcaption>[风向标](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/home.dart#L109)</figcaption>

</figure>

现在，如果您猜测这两个函数， **refresh()** ，最终实际上调用了一个状态对象的 **setState()** 函数，那么您就猜对了。恭喜你，你开始看到全貌了。

主题控制器的 **refresh()** 函数用它的 MarterialApp 对象调用 AppView 的状态对象。这将更改应用程序的 AppBar 颜色。而上面看到的第二个 **refresh()** 函数调用‘Home’小部件的状态对象。这将改变屏幕的背景颜色。魔法。

### 你更喜欢什么？

我想快速介绍的最后一件事是应用程序偏好。如果你试用这个示例应用程序，你会注意到它会“记住”你选择的最后一个城市，事实上，下次你启动这个应用程序时，它会显示当前的天气情况。这是因为输入的“最后一个城市”会记录在应用程序的首选项中。

### 一切尽在 Init

“天气控制器”WeatherCon 将在其函数 **initState()** 中获取最后一个城市(如果有的话)。在那里，它调用函数**，initFetch()** 。下面是控制器 WeatherCon 的截屏。红色箭头显示了应用程序偏好设置的访问位置。

<figure>[![](img/cd768c1cfe88dae3d609e2d2424922f3.png)](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/home.dart#L32) 

<figcaption>[类风标](https://github.com/Andrious/weathercast/blob/dbc1fa6774057631f16576a2715cf3123a7cd9a3/lib/src/home/controller/home.dart#L32)</figcaption>

</figure>

### **留作后用**

您可以在上面的截图中看到检索“最后一个城市”的位置，事实上，也是记录“最后一个城市”的位置——在函数 **fetchWeather()** 中。记住，函数**【fetchWeather()**最终会在用户每次单击放大镜时被调用，函数**【on pressed()**会在视图代码中执行，然后在控制器代码中执行。

### Prefs

你在上面的截图中看到的 Prefs 类是我在第一次发现 Flutter 几个月后编写的库。在我的项目中，我需要这样一个库，我在不久后写的一篇文章中谈到了这一点:

[存储并读取你的应用偏好](https://medium.com/@greg.perry/store-and-read-your-apps-preferences-4139e836cfe9)

Prefs 库实际上是我正在开发的框架的一部分，你已经在上面的 AppController 类中看到了，当应用程序设置它的环境时。

### Prefs 已经发布！

哦！见鬼去吧！我决定以库包的形式发布 Prefs！我刚刚做到了！刚才！给你:

[首选项 1.0.2](https://pub.dartlang.org/packages/prefs)

它在我的项目中被证明是有用的。不妨分享一下，对吧？！试试看。喜欢就用吧。如果不是，那就不要，对吧？！同样，请阅读上面的文章，了解如何使用它。当它发布时，它仍然是框架的一部分。不管什么时候会发生？！只是时间不够，是吗？反正！

### 结论

好吧，这是一个阅读。我肯定你饿了，或者想继续你的生活。然而，我将继续关注这篇文章，因为正如你所知道的，在开发软件时有更多的东西需要考虑。事情就是这样，这个框架将允许您这样做，我将在其他文章中向您展示如何做到这一点。

干杯。

* * *