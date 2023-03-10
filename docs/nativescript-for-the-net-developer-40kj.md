# 的 NativeScript。网络开发者

> 原文：<https://dev.to/progress/nativescript-for-the-net-developer-40kj>

> 做我自己
> 做一名. net 开发人员，没有 visual studio 之外的生活
> 老板说，“已经写了一个移动应用”
> iamscrewed.jpg
> 做了很多研究
> 看看 xamarin
> 看看 cordova
> mrw 我找到了 nativescript

[![positive reaction gif](img/7883532c83bc3623ab924156bb0274ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--P6CHu2Xe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/nativescript-for-net-dev/nap-dyn.gif)

我的大部分开发生涯都是在 Visual Studio 中度过的，致力于开发平庸的 web 表单应用程序，当移动优先成为 Web 开发的口头禅时，我的系统感到震惊。再加上移动应用程序的开发，再加上 T2 本地应用程序的开发，你会感到孤独和困惑。NET 开发者。

虽然 Xamarin 对许多人来说似乎是合理的选择。NET 开发者，恐怕他们可能没有把目光放在万能的 C#身上。这里不敲 Xamarin(见鬼，我们为 Xamarin 提供 [Telerik UI！)，但是让我们来看看一个免费的开源框架可能，仅仅是可能，是一个更好的选择。](http://www.telerik.com/xamarin-ui)

## 输入 NativeScript

如果你以前从未听说过 [NativeScript](https://www.nativescript.org/) ，它是一个开源的 JavaScript 框架，可以让你创建真正的原生移动应用。不仅是原生应用，还有 iOS 和 Android 的*跨平台*原生应用。我提到本地人了吗？NativeScript 中的 UI 层由 100%的原生 UI 元素组成(请阅读:无 jank、全性能 UI)。

**“JavaScript？咩，我出来了！”**坚持住！我知道有很多。仍然害怕 JavaScript 的. NET 开发人员。没关系，我明白。但是在你走之前，你仔细看过[的打字稿](https://www.typescriptlang.org/)了吗？

TypeScript 是我们在微软的朋友提供的“JavaScript 的类型化超集”。通俗地说，TypeScript 是 JavaScript 之上的一层，它提供了强类型、类、模块以及您习惯于用 C#获得的更多功能。所以你写 TypeScript，TypeScript 编译器把它编译成普通的 JavaScript。

那么，为什么我在文章的开头就偏离了打字稿的主题呢？因为 [NativeScript 完全支持 TypeScript](https://www.nativescript.org/using-typescript-with-nativescript-when-developing-mobile-apps) ，这使得我们从 C#的迁移更加顺畅。

## NativeScript 101

如果您已经做到了这一步，那么您至少已经部分接受了 TypeScript 作为您的桥梁。NET/C#到带有 NativeScript 的原生移动开发。为此，我给你第一份[松脆玉米饼](https://www.heytaco.chat/) : [![hey taco](img/8503bed5bf7074074ddd874a51acd7ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cm_-8bEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/nativescript-for-net-dev/taco.png)

现在，让我们看看如何在一些现有的基础上创建应用程序的单个本机脚本页面或视图。净技能。三种基本成分是:

*   用于用户界面标记的 XML
*   用于设计用户界面样式的 CSS
*   和用于应用程序逻辑的 **JavaScript/TypeScript**

### XML

虽然我们许多人都有 SOAP 诱发的讨厌的 XML 语法的噩梦，但 NativeScript 标记实际上非常容易理解。

它也非常像 XAML，因为许多 NativeScript UI 标记非常类似于 XAML 语法(实际上利用了 XAML 的一些更好的功能)。

例如，下面是一些 XAML 标记:

```
 <StackPanel>
        <Label Margin="20" Content="Hello!"></Label>
        <Button Margin="10" HorizontalAlignment="Right">OK</Button>
    </StackPanel> 
```

...而在 NativeScript 中的确切对应是:

```
 <StackLayout>
        <Label text="Hello!" style="margin:20"></Label>
        <Button text="OK" style="margin:10; horizontal-align:right"></Button>
    </StackLayout> 
```

不仅有[大量的原生 UI 元素](https://docs.nativescript.org/ui/components)可以利用，而且你还可以利用各种布局选项以无数种方式排列你的 UI 元素。

你已经看到了上面的`<StackLayout>`标签(垂直或水平堆叠元素)，但是还有`<AbsoluteLayout>`、`<DockLayout>`、`<GridLayout>`和`<WrapLayout>`选项。

幸运的是，Jen Looper 为我们提供了关于 [NativeScript 布局](http://developer.telerik.com/featured/demystifying-nativescript-layouts/)的全面报道。一定要把它收藏起来，以备将来参考。

### CSS

NativeScript 的美妙之处并不局限于它对 TypeScript 和原生 UI 的使用。相反，我认为开发人员最大的乐趣来自于用 CSS 设计应用程序的能力。我不是设计师，所以能够利用 CSS 这样的现有技能可以减轻很多痛苦。

让我们明确一点:这是你多年来和 ASP.NET 一起使用的 CSS。这里窗帘后面没有人！

有意义吗？吃一个玉米卷，你已经赢得了: [![hey taco](img/8503bed5bf7074074ddd874a51acd7ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cm_-8bEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/nativescript-for-net-dev/taco.png)

关于在 NativeScript 中使用 CSS，您应该了解以下几点:

**1)你可以将样式应用于整个应用程序，应用于特定的页面，和/或特定的元素。**例如，在你的`app.ts`或`app.js`文件中(这是你的应用程序初始化的地方)，你可以为你的整个应用程序加载一个 CSS 文件:

```
 import application = require("application");
    application.cssFile = "app.css";
    application.start({ moduleName: "main" }); 
```

同样，如果您的应用程序中有一个名为“main”的视图，您可以(可选地)添加一个`main.css`文件，该文件会自动将这些样式仅应用于该视图！

最后，尽管多年来一直不被认可，但你可以欺骗并直接将样式应用于 UI 元素，就像这样:

```
 <Button text="I (kind of) love CSS!" style="background-color: red" /> 
```

你可以使用你熟悉和喜爱的 CSS 选择器。例如，您可以通过以下方式选择元素:

*   类型:`button { background-color: blue }`
*   类别:`.button-large { font-size: 44 }`
*   ID: `#cancel-button { border-color: green }`
*   层次:`StackLayout > Button { background-color: orange; }`
*   属性:`button[someAttribute]{ background-color: yellow; }`

此外，您可以结合以上内容来创建[复杂的选择器场景](https://docs.nativescript.org/ui/styling#supported-selectors)。

你可以使用许多你已经使用多年的 CSS 属性。比如`margin`、`border`、`padding`、`color`、`width`、`height`等属性都支持，远远不止这些。你可以在[这个支持属性列表](https://docs.nativescript.org/ui/styling#supported-css-properties)中看到它们。

### JavaScript/TypeScript

就像在 ASP.NET 一样，每个标记文件背后都有一个关联的代码文件。不是 C#，是 JavaScript(或者 TypeScript)。即使您访问的是原生平台 API，您正在编写的 JavaScript 也没有什么特别之处。这是因为您正在利用 NativeScript 模块，这些模块是在本机 API 之上的有效的跨平台抽象。

> 虽然这超出了本文的范围，但是模块是 NativeScript 跨平台方面的关键(好奇者可能想[阅读更多的](https://docs.nativescript.org/core-concepts/modules))。

因此，通过在 file 后面添加 JavaScript 代码，项目中的文件结构将如下所示:

[![nativescript file structure](img/07e7355eaf867c1aff5faef92c85d086.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v-GBWxzB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/nativescript-for-net-dev/vs.png)

> 请记住，`main-page.css`文件是可选的，在这种情况下，它只适用于`main-page.xml`视图。

### 一起拔吧！

至此，您已经大致了解了如何构建一个 NativeScript 应用程序:为您的原生 UI 标记使用 **XML** ，为您的 UI 设置样式使用 **CSS** ，为您的应用程序逻辑使用 **JavaScript/TypeScript** 。吸一口气，吃个玉米卷: [![hey taco](img/8503bed5bf7074074ddd874a51acd7ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cm_-8bEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/nativescript-for-net-dev/taco.png)

现在，在你开始[之前，浏览一下我们的深入教程](http://docs.nativescript.org/)(不要点击那个链接，不要这样做！)，您可能想了解更多关于 NativeScript 模式和我们新的 Angular 2 支持的信息:

## 原生脚本模式

现在，您可能想知道如何为您的代码提供一些结构。作为一个现代人。NET 开发人员，您可能熟悉 MV*系列的模式，如 MVC 或 MVVM。幸运的是，NativeScript 有一个[奇妙的 MVVM 实现](http://nativescript.github.io/nativescript-style-guide/#chapter1.0)，允许您轻松地将数据绑定到原生 UI 元素。

例如，这里有两个本机 UI 元素，您可以用 NativeScript:
来表达它们

```
 <TextField id="first" text="{{ first }}" hint="First Name" />
    <TextField id="last" text="{{ last }}" hint="Last Name" /> 
```

...而后面的支持代码调用构造函数定义一个新的`awesomeDude`对象，这个对象是页面的视图模型，并将`awesomeDude`设置为绑定上下文:

```
 var Observable = require("data/observable").Observable;

    var awesomeDude = new Observable({
        first: "Rob",
        last: "Lauer"
    });

    exports.loaded = function(args) {
        page = args.object;
        page.bindingContext = awesomeDude;
    }; 
```

### 角形支架

最后，如果你已经赶上了 [Angular](https://angular.io/) 的潮流，你会很高兴地知道 NativeScript 为 Angular 提供了[一流的支持。](https://www.nativescript.org/nativescript-is-how-you-build-native-mobile-apps-with-angular)

**你问为什么有棱角？Angular 为你的应用程序提供了普通 JavaScript 甚至是 TypeScript 都无法提供的结构。从路由到依赖注入到数据绑定——一切都作为 Angular 框架的一部分提供。**

更重要的是，使用 Angular，你不仅可以使用相同的代码库来交付原生 iOS 和 Android 应用，还可以交付 web 应用。没错，一个适用于 iOS、Android 和 Web 的代码库。惊艳。

如果你已经走了这么远，你应该再吃一个墨西哥玉米卷: [![hey taco](img/8503bed5bf7074074ddd874a51acd7ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cm_-8bEg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/rdlauer/articles/master/nativescript/nativescript-for-net-dev/taco.png)

## 接下来去哪里？

我希望现在我已经吊起了您的胃口，您至少很想知道更多关于 NativeScript 的事情。如果是这样，我有一些进一步学习的建议:

1)查看我们的 [NativeScript 教程](http://docs.nativescript.org/tutorial/chapter-0)，按照一步一步的说明创建您的第一个原生移动应用。

2)如果你是一个 Angular 的人(或者只是想看看 Angular 是怎么回事)，你可以做同样的教程，但是使用 [Angular 和 TypeScript](http://docs.nativescript.org/angular/tutorial/ng-chapter-0) 而不是 vanilla JavaScript。

尽情享受吧！