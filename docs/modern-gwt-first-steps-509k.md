# 现代 GWT，第一步

> 原文：<https://dev.to/ibaca/modern-gwt-first-steps-509k>

## GWT 语境💎

GWT 是一个强大的工具，但有时这种力量可能会被视为复杂性。

*这里长篇大论不必要的解释，如果你只是想从 GWT 跳到[这个向导本身](https://dev.to/ibaca/modern-gwt-first-steps-509k#the-starting-guide)。*

我认为这种复杂性可以分为两个原因；1.大项目是复杂的，当你试图解释如何解决一个复杂的情况时，解释本身也是复杂的。2.GWT 有一个巨大的生态系统，每个问题都有不同的解决方案。这迫使你做出复杂的决定。

但是，我们是如何到达那里的呢？

GWT 诞生于 2006 年，从那以后，网络生态系统的发展令人惊叹，甚至在 GWT 内部也发生了许多变化、替代方案和第三方，他们创造了令人惊叹的解决方案来管理大型客户端网络应用。

从 2006 年到 2012 年，GWT 被称为“谷歌网络工具包”，它是一个一体化的解决方案。2012 年 6 月，谷歌将控制权交给了[指导委员会](https://groups.google.com/forum/#!forum/gwt-steering)，并将该项目重新命名为“GWT”。从那时起，GWT 已经发展成为一个更加开放的工具，嵌入到 GWT 本身的大多数架构解决方案已经被第三方替代方案所克服。如今，解决方案的数量是巨大的，在这一点上，GWT 只是核心，它不会偏向于你应该如何组织你的代码。因此，GWT 不再是一个*的网络工具包*，也不再是一个*的客户端框架*，它只是一个*的传输器*，周围有一个巨大的生态系统。

例如，现在如果你想选择一个客户端架构模式，你必须在 [gwt-activities](http://www.gwtproject.org/doc/latest/DevGuideMvpActivitiesAndPlaces.html) 、 [gwtp](https://github.com/ArcBees/GWTP) 、 [errai](http://erraiframework.org/) 、 [react4j](https://github.com/react4j/react4j) 、 [vue-gwt](https://github.com/VueGWT/vue-gwt) 、[路娜](https://github.com/NaluKit/nalu)等等之间做出选择！或者，如果你正在寻找一个传输层，你应该在 [gwt-rpc](http://www.gwtproject.org/doc/latest/tutorial/RPC.html) 、 [request-factory](http://www.gwtproject.org/doc/latest/DevGuideRequestFactory.html) 、 [resty-gwt](https://resty-gwt.github.io/) 、 [gwtp-rest-dispatch](https://dev.arcbees.com/gwtp/communication/) 、 [autorest](https://github.com/intendia-oss/autorest) 等等之间进行选择！

所有这些复杂性(只有对于有经验的 java 团队的大型项目才是合理的)和所有这些可选的架构解决方案(这实际上是一件好事)使得从 GWT 开始有点令人困惑。

说了这么多，**什么时候该用 GWT？我认为只有满足这两点，你才应该在公司项目中使用 GWT。**

1.  你有一个已经了解 Java 和 Web 开发(HTML5、CSS3、ES6)的高级 Java 团队。为了和 GWT 一起开发，你*必须*懂浏览器，你不需要成为一个 JavaScript 专家，但是你需要了解很多网络世界的知识( [MDN](https://developer.mozilla.org) 是你的朋友)。
2.  你必须**与客户端或者甚至是 iOS 与 [J2ObjC](https://github.com/google/j2objc) 共享你已经在 Java(服务器或 Android)中使用的 Java 逻辑**、库和工具。这就是 GWT 的全部意义，在平台之间共享代码。这是很有用的，它解决了逻辑重复的问题，例如，每当你编辑一个字段时，你经常用一些规则来验证它，但是随后它被发送到服务器并应该被再次验证。这个简单但常见的用例用 GWT 完美地解决了。您编写验证器代码，并在所有平台上使用完全相同的验证代码，这为您提供了客户端的响应能力以及服务器端的安全性([跨平台示例](https://github.com/xemantic/github-users))。

如果你已经有多年使用 GWT 的经验，你可以用它做很多事情，甚至是小程序。但是为了证明入门的努力，你应该满足这两点。但是，呃！在公司项目之前，你可以只是为了好玩而学习，我希望你是为此而来！

## 首发指南☝️

为了使本指南更简单，我将把重点放在客户端。此外，我会做出一些我认为常见且经得起未来考验的个人决定。但是请记住，GWT 比我在这里解释的要多得多！。这是我选择的:

1.  *创建一个微型客户端专用的应用程序*，因此没有传输和查看组件。通常指南从客户端-服务器应用程序开始，它要复杂得多，当你开始使用 GWT 时，你会将客户端和服务器端混淆为一个独特的神奇虚拟机。尽管一切都是 java，并且您可以在客户机或服务器上运行相同的 java 代码，但两者都是独立的运行时环境，这个概念应该很清楚。

    此外，如果你创建一个客户端专用的应用程序，指南会变得更小，你可以更容易地使用它。更重要的是，我强烈建议你找到用 JS 或 TypeScript 编写的微型游戏或微型应用程序，并将其迁移到 GWT。这是适应 GWT 的一个很好的练习。比如这是一款从 JS 项目 [rxsnake-gwt](https://github.com/ibaca/rxsnake-gwt) 移植过来的贪吃蛇游戏。

2.  *用 Maven* 管理，不需要 IDE 或者插件，只需要`java+maven`。我将使用 IntelliJ IDEA。我们不打算使用任何插件，IntelliJ Ultimate 有 GWT 支持，Eclipse 有一个非常好的插件，但我个人不喜欢依赖这些插件。此外，这些插件的问题是项目配置(主要是类路径)不容易在终端或 CI 与 IDE 插件中保持完全相同。因此，通过这个解决方案，Maven 解决方案，您将能够在所有这些环境中以相同的方式运行、测试和打包您的 GWT。IMO Maven 是目前编译 GWT 最简单的策略。

### 1。Maven 基本原型💩

首先，我们创建一个最小的 maven 项目。如果您已经安装了 maven，您可以遵循这个[指南](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html)或者执行这个命令。

```
› mvn archetype:generate \
    -DarchetypeRepository=https://repo.maven.apache.org/maven2 \
    -DarchetypeGroupId=org.apache.maven.archetypes \
    -DarchetypeArtifactId=maven-archetype-quickstart \
    -DarchetypeVersion=1.4 -DinteractiveMode=false \
    -DgroupId=me -DartifactId=hello-app -Dversion=HEAD-SNAPSHOT 
```

Enter fullscreen mode Exit fullscreen mode

希望你对`maven`有所了解。但是，如果没有，请注意，您可以使用`mvn verify`编译、测试和打包您的应用程序。现在运行它！😉

您现在可以在 IntelliJ 中打开创建的项目，只需单击 open 并转到文件夹。IntelliJ 自动检测 maven，它将使用 maven 配置来配置您的项目。我已经重新格式化并删除了注释，将目标/源 java 版本改为 11(好玩的🥳，我们可以用`var`)，并将`maven-compiler-plugin`更新为`3.8.1`、`maven-surefire-plugin`更新为`2.22.1`、`junit`更新为`4.13.2`。每当您更改 POM IntelliJ 中的某些内容时，它会要求您“导入更改”，并单击它来更新项目配置。应该是这样的:
[![Step 1: IDE layout](img/07ed8c148f9767d232279451fe97ff09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RWc-nRw8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/rfxzhtloepwa2mbfz30d.png)

再次尝试`mvn verify`，但这次使用 IntelliJ maven 菜单。任何 JDK ≥11 应该可以，但如果你有任何问题，就用 JDK 11。
[![Step 1: Configure JDK11](img/023b8badbf81efe4c05a4744b83a04e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_b1skWpg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://dev-to-uploads.s3.amazonaws.com/i/qwhcqmwximjymooqphb2.png)

我从这个基本的 maven 原型开始，因为我想指出，GWT 项目使用整个 Java 生态系统，它使用类似的模式，但应用于前端开发。

[第一步不同。](https://github.com/ibaca/gsg-hello-app/commit/3488b27b)

### 2。GWTify it🌈

首先，修改`pom.xml`来添加 GWT 依赖项和 GWT Maven 插件，这将添加一些有用的目标来编译和运行 GWT。将此添加到依赖项部分:

```
<dependency>
  <groupId>com.google.gwt</groupId>
  <artifactId>gwt-user</artifactId>
  <version>2.9.0</version>
</dependency>
<dependency>
  <groupId>com.google.gwt</groupId>
  <artifactId>gwt-dev</artifactId>
  <version>2.9.0</version>
</dependency> 
```

Enter fullscreen mode Exit fullscreen mode

这些是最小的 GWT 依赖。`gwt-user`包含客户端 java 实用程序，例如，`EntryPoint`接口在 GWT 被用作`static void main(args)`的对等物(稍后将详细介绍)。而`gwt-dev`包含了编译器和其他开发工具的需求。

然后，您需要配置 GWT 插件并将项目定义为一个`gwt-app`。将它添加到构建部分:

```
<plugins>
  <plugin>
    <groupId>net.ltgt.gwt.maven</groupId>
    <artifactId>gwt-maven-plugin</artifactId>
    <version>1.0.1</version>
    <extensions>true</extensions>
    <configuration>
      <moduleName>me.App</moduleName>
      <skipModule>true</skipModule>
    </configuration>
  </plugin>
</plugins> 
```

Enter fullscreen mode Exit fullscreen mode

这是开头，在`artifactId`和`version`元素之后:

```
<packaging>gwt-app</packaging> 
```

Enter fullscreen mode Exit fullscreen mode

这将扩展 maven，将 GWT 特定的执行添加到 maven 生命周期中。例如，添加这个之后，如果你再次运行`mvn package`，你会注意到有各种新的步骤，最重要的一个是运行 GWT 编译器，包括我们的项目源代码，并输出最终的 JS。注意，这个现在不行，因为我们还没有更新 java 源代码！有 2 个配置，`moduleName`指向主`gwt.xml`文件(稍后解释),`skipModule`用于避免一个很好但有点“神奇”的实用程序从这个插件中为你生成`gwt.xml`文件，现在，我更喜欢在下一步手动创建它，所以我只是设置插件不为我生成这个文件。

更新 IntelliJ 项目，以便下载依赖项并将其添加到项目类路径中。

现在，我们将更新运行在 JVM 中的当前的`App`类，使它运行在客户端。让`App`实现`com.google.gwt.core.client.EntryPoint`，移除`static main`方法，用等价代码实现`onModuleLoad`。简单吧？几乎是一样的，JVM 的`main`和 GWT 的`onModuleLoad`。你的上课内容应该是:

```
package me.client;

import com.google.gwt.core.client.EntryPoint;
import com.google.gwt.dom.client.Document;
import com.google.gwt.dom.client.Text;
import com.google.gwt.user.client.ui.RootPanel;

public class App implements EntryPoint {

    @Override
    public void onModuleLoad() {
        Text textNode = Document.get().createTextNode("Hello World!");
        RootPanel.getBodyElement().appendChild(textNode);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我认为代码是不言自明的，但是……是的，`getBodyElement`返回网页的`body`,所以我们可以添加`Hello World!`,并在我们的页面上看到它。

关于`App`类还有一件事，把它移到`client`包中(如果你复制了&粘贴了前面的代码，你的 ide 可能已经抱怨过了)。这让事情变得简单了一些。请注意，GWT 应用程序都是关于共享代码的，所以你的项目中可能会有一个`server`、`shared`和`client`包。尽管这是一个最小的仅客户端的例子，我们将遵循这个惯例，所以将它移动到`client`包中(警告:如果您正在使用 IntelliJ，它可以在将`App.java`移动到`client`包后将 pom.xml `<moduleName>`从`me.App`更改为`me.client.App`，但是它应该是`me.App`，所以检查一下，有时 IntelliJ 太聪明了)。

接下来，创建名为`App.gwt.xml`的`gwt.xml`文件(在`me`包中创建，而不是在`client`中)。该名称应与我们在`gwt-maven-plugin`配置中配置的包和名称相匹配。创建包含以下内容的文件:

```
<module>
  <inherits name="com.google.gwt.user.User"/>
  <entry-point class="me.client.App"/>
  <!--<source path="client"/>-->
  <!--<public path="public"/>-->
</module> 
```

Enter fullscreen mode Exit fullscreen mode

这个文件叫做 [GWT 模块文件](http://www.gwtproject.org/doc/latest/FAQ_Client.html#What_is_a_GWT_Module?)。它是必需的，因此 GWT 编译器知道应该编译哪些源代码，也就是说，GWT 并不编译类路径中的所有内容，它只编译在`source`标签中指明的源代码。它被注释是因为模块隐式地添加了这两行。`source path="client"`表示包`{module-package}.client`中的所有 java 类都将被包含，这就是为什么我们将`App`移到了`client`包中。`inherits`模块是一个依赖项，这个模块依赖于`gwt.user.User`模块，而`User`源将递归地依赖于其他模块。第二行`entry-point`指定一个入口点。因此，当 GWT 最终汇编加载到网页上时，这些入口点将被调用。这是我们在`App`类中创建的`onModuleLoad`。这相当于一个`jar`文件的`Main-Class`清单属性。

最后，在`me.public`包中创建一个`index.html`文件，内容为:

```
<!DOCTYPE html>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
GWT • Hello App
<script type="text/javascript" src="me.App.nocache.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

前两行是“强”推荐，`title`是可选的，唯一实际有趣的行是`script`。这将引导 GWT 编译的代码，经过一些步骤后，它将调用您的`onModuleLoad`入口点！耶！可以看到，文件的名字是`{moduleName}.nocache.js`。`nocache`只是 GWT 的一个简单约定，编译器会生成各种文件，有些包含`.nocache.`，有些包含`.cache.`。带有`.nocache.`的文件永远不应该被缓存，而`.cache.`可以永远被缓存。这使得向服务器添加过滤器以添加缓存头变得很简单，这被称为 [GWT 完美缓存](http://www.gwtproject.org/doc/latest/DevGuideCompilingAndDebugging.html#perfect_caching)。

注意，这个`public/index.html`是在 GWT 模块文件中配置的实用程序，但是类似于`<source path="client"/>`约定，所有模块都包含一个隐式的`<public path="public"/>`行。并且，GWT 编译器会将所有模块`public`路径中的内容复制到最终的编译器输出中。但是，我建议你尽量少添加到`public`文件夹中。GWT 有很多选项可以将托管资源添加到你的项目中，公共文件夹只是 GWT 盲目复制的一个插件。我不想在这里解释你，但是，相信我，如果你在一个`public`文件夹中添加了太多的东西，这可能会被认为是代码味道。在这一点上，寻求帮助(在这篇文章的最后我会解释哪里是寻求帮助的最好地方)。

[第二步不同。](https://github.com/ibaca/gsg-hello-app/commit/007cc944)

### 3。打包、运行和调试🔥

GWT trans files 代码到 JS 和看到的结果，你需要在你的浏览器中打开它。但是首先，您需要编译和打包项目。运行:

```
mvn package 
```

Enter fullscreen mode Exit fullscreen mode

这将生成所需的文件，并将它们放在项目目标文件夹中。然后成功后，打开结果，在 mac 中，可以在终端中使用`open target/hello-app-HEAD-SNAPSHOT/me.App/index.html`。否则，请在浏览器中打开该文件或将该文件拖到浏览器窗口中。您应该会在浏览器中看到`Hello World!`。

这里发生了什么事？`me.App`文件夹中的`index.html`就是我们之前在`public`包中写的那个。所以这不是由 GWT 生成的，它是从我们的`public`资源中盲目复制的。这个文件指向被 GWT 编译器放在同一个文件夹中的`me.App.nocache.js`。这个文件是 GWT 引导程序。它负责选择最终的客户端排列。它被称为置换，因为 GWT 定义了一种特殊类型的编译属性，可以用来在不同的“实现”之间进行选择，并且它可以用来为所有这些属性的所有可能组合生成特定的最终编译结果，这些组合中的每一个都被称为*一种置换*(例如。`chrome+es`是浏览器 chrome 和语言西班牙语的置换。

如果您可以看到`Hello World!`，这意味着所有这些步骤都已成功完成。我建议在你的浏览器中打开开发工具控制台并检查网络面板，你可以看到浏览器加载了`nocache`文件，然后它加载了一个奇怪的`{hash}.cache.js`文件，这是实际的排列。您可以尝试另一个浏览器，看看这个置换文件是不同的，因为默认情况下，GWT 将产生一个特定的置换每个浏览器。这是可配置的，但是我将在本指南中省略它。在版本之间，应该检查的唯一文件是`nocache`，因为如果有什么变化，它将指向新散列的文件(这就是为什么`cache`文件可以永远被缓存)。

[![Step 3: Local run](img/901246369ffd21ca5af9daf7d7cd981e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bkh59Wkx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hcv1ncsfrupj5esay8ad.png)

这很好，但如果您需要调试或重新加载小的更改，这就不那么好了。在开发过程中，您将使用一个名为`codeserver`的特殊 GWT 特性。`codeserver`是一个服务器，它将动态编译并提供代码。您可以使用`mvn gwt:codeserver`运行`codeserver`，但是在本例中我们将使用目标`mvn gwt:devmode`。好吧，对不起，这有点令人困惑，但这将运行 2 个服务器，`codeserver`和另一个 jetty 只是为了打开`index.html`。这个我不想解释，因为有点混乱，需要很长的解释。但是现在，只要记住我们使用的是`codeserver`，我们使用`devmode`只是为了能够打开`index.html`(所有这些在以后关于服务器端的教程中会更有意义)。所以，请运行`mvn gwt:devmode`。在 IntelliJ 中，你可以简单地在 maven 面板中双击`gwt:devmode`目标，超级方便！

[![Step 3: Run codeserver](img/c56a4a906c3572b1f3cd7eeae9371aa9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jW5a2yWC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fmaoyacnwy27x1cblcd1.png)

如果您仔细检查日志，您可以看到两个服务器，端口 9876 处的`codeserver`和端口 8888 处的第二个 jetty 服务器。从`codeserver`开始。在浏览器中打开它。现在只检查第二点，那里有一个模块列表，你看到你的`me.App`模块。希望你看到！然后，在 8888 端口打开另一台服务器(两者都可以像`localhost:9876`或`localhost:8888`一样在任何浏览器中打开)。当您打开 jetty 服务器时，您将看到您的模块目录，单击`me.App/`打开该文件夹。这将最终打开我们的`index.html`，然后它将获取`me.App.nocache.js`文件，但这一次(这有点儿`devmode`的魔力)它将使用一个特殊的`nocache.js`文件来检测排列，它还将与`codeserver`通信，动态编译代码，并将排列加源代码发送到您的浏览器。在几秒钟内，你应该注意到一个“编译我。应用程序”对话框，该对话框将在成功时消失。您可以更改`hello world`消息，例如移除`world`部分，并重新加载页面。这将再次启动编译。这是标准的开发生命周期，编辑和重新加载。

现在，我将要求您转移到 chrome。你可以在任何浏览器中这样做，但是用一个特定的浏览器更容易解释。转到`devtools`并打开`Sources`选项卡。现在找到一个资源(快捷键 ctr+p 或者 cmd+p)写`App`找到我们的入口点类。你应该在浏览器中看到 Java 文件，这是因为`codeserver`自动生成 JS 源码映射，chrome 解析映射的 Java 源码并显示在`devtools`中。点击`RootPanel.getBodyElement`行号添加一个断点，它应该变成蓝色，表示一个活动断点。然后，重新加载页面。如果您做得正确，调试器应该在那一行停止，您应该看到类似这样的内容:

[![Step 3: debug](img/6582d4867a7d961b73a30fcf6256736f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--J0uSs2tE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yenc1zv5nipouztyp7ey.png)

这就是调试 GWT 代码所需的全部内容。但是，请注意，您可以用纯 Java 编写大量代码，这些代码在 JVM 中运行或使用 JUnit 进行测试是微不足道的，尤其是如果您避开了 UI 最终层。GWT 是关于共享和使用 Java 工具的，所以如果你正在开发像空间索引这样的工具，使用普通的 Java 来测试、运行和调试它会更加容易和快速。然后在客户端使用它。

### 4。增加情趣🌮

这就是个人风格变得疯狂的地方。现在我将解释如何添加 2 个库，一个“纯”JS 库(Elemental2)，和一个“纯”Java 库(RxJava)。我特意选择了这两个库作为相反的极端情况。使用“纯”JS 库，您应该可以看到如何在 GWT 访问原生 JS 代码，以及它是多么简单和轻量级。使用“纯”Java 库，你会发现在 GWT 你可以在浏览器中使用经典的纯 Java 库。但是，我们不会直接依赖这两个库，相反，我们将使用此依赖关系图:

[![Step 4: dependencies](img/6654a56a512bc86f4a9531304cd0cd12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ajhmkzn5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v1i2deyh8vix5341hhy2.png)

[Elemental2](https://github.com/google/elemental2) 是一个 Google 库，用 Java 公开了整个浏览器 API。这个库唯一的小问题是，在某些情况下，API 不够 javaish，特别是，事件和元素创建需要强制转换，这在 Java 世界中并不常见。因此，虽然我们将在幕后使用这个库，但我们将直接依赖一个名为 [Elemento](https://github.com/hal/elemento) 的超级小红帽库来添加那些缺失的部分。这个库是为*元素*和*事件*手工制作的 API，这个漂亮的库给了我们 JS 的力量和 Java 的类型安全。

RxGWT 是一套在 GWT 使用 RxJava 的实用程序，即与 RxAndroid 相同，但不适用于 GWT。RxGWT 依赖于 [RxJava GWT](https://github.com/intendia-oss/rxjava-gwt) ，一个基于 RxJava 的瘦 GWT 适配器库。是的，好吧，你不能直接依赖 RxJava，但是如果你去回购，你会看到只有一些小的变化和配置，使它在 GWT 工作，几乎所有的实际 RxJava 源代码都是一样的。

*记得我的推荐；这两个库的想法是，您能够使用您的 Java 知识找到`JS+RxJS`项目并通过 GWT 迁移到`Java+RxJava`!这是一个很棒的习惯 GWT 的方式，有很多有趣的微型反应项目可以改进！*

要添加此依赖关系，请用
更新`pom.xml`

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>me</groupId>
  <artifactId>hello-app</artifactId>
  <version>HEAD-SNAPSHOT</version>
  <packaging>gwt-app</packaging>
  <name>hello-app</name>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>com.google.gwt</groupId>
        <artifactId>gwt</artifactId>
        <version>2.9.0</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies>
    <dependency>
      <groupId>com.google.gwt</groupId>
      <artifactId>gwt-user</artifactId>
    </dependency>
    <dependency>
      <groupId>com.google.gwt</groupId>
      <artifactId>gwt-dev</artifactId>
    </dependency>
    <dependency>
      <groupId>org.jboss.elemento</groupId>
      <artifactId>elemento-core</artifactId>
      <version>1.0.0-rc3</version>
    </dependency>
    <dependency>
      <groupId>com.intendia.gwt.rxgwt2</groupId>
      <artifactId>rxgwt</artifactId>
      <version>2.3</version>
    </dependency>
    <dependency>
      <groupId>com.intendia.gwt</groupId>
      <artifactId>rxjava2-gwt</artifactId>
      <version>2.2.20-gwt1</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.13.2</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>net.ltgt.gwt.maven</groupId>
        <artifactId>gwt-maven-plugin</artifactId>
        <version>1.0.1</version>
        <extensions>true</extensions>
        <configuration>
          <moduleName>me.App</moduleName>
          <skipModule>true</skipModule>
        </configuration>
      </plugin>
    </plugins>
    <pluginManagement>
      <plugins>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.1</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个类型为`pom`和范围为`import`的特殊依赖，称为 *BOM* 。这是一个实用程序依赖项，它断言在 *BOM* 中定义的所有依赖项都将是在 *BOM* 本身中指定的版本。这是很有用的，因为我们最终会得到各种版本的各种`gwt-xxx`库，这可能是个问题，所以这个 *BOM* 断言所有 GWT 核心依赖项都是同一个版本。

另外，我们增加了 3 个新的显式依赖项，`elemento-core`、`rxgwt`和`rxjava-gwt`。

然后，转到您的`App.gwt.xml`模块文件并添加这两个依赖项:

```
<inherits name="com.intendia.rxgwt2.RxElemento"/>
<inherits name="org.jboss.gwt.elemento.Core"/> 
```

Enter fullscreen mode Exit fullscreen mode

如果您仍在运行`gwt:devmode`，您将需要重启。有新的 maven 依赖项，由于这些依赖项应该添加到类路径中，您将被迫重新启动它。只是几秒钟，所以没问题。停止并再次运行`mvn gwt:devmode`。打开`localhost:8888`,进入`app.App`,检查你是否还能在浏览器中看到`Hello World!`。

现在，用
更新`App.java`的内容

```
package me.client;

import com.google.gwt.core.client.EntryPoint;
import org.jboss.elemento.Elements;

public class App implements EntryPoint {

    @Override
    public void onModuleLoad() {
        Elements.body().add("Hello World!");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

酷，这就不那么冗长了。`Elements`是一个 *elemento* 工具，它包含了所有那些用户友好的类型安全元素 API。大多数类型的`Elements`方法返回构建器，它们是实际*元素 2* 类型的包装器。可以提取*元素 2* 类型调用`get()`。此时，您几乎可以直接访问浏览器 API。GWT 将把 Java 源代码转换成 JS，但是像这样的一行代码`DomGlobal.document.body.background = "red";`将被转换成`document.bod.background = 'red';`，几乎一样。*提示*:浏览 *elemental2* 源代码是学习如何在 Java GWT 中映射本机 API 的一个好策略。

现在我们要应用 *RxJava* ，将`App.java`的内容更新为:

```
package me.client;

import com.google.gwt.core.client.EntryPoint;
import com.intendia.rxgwt2.elemento.RxElemento;
import elemental2.dom.HTMLElement;
import org.jboss.elemento.Elements;

import static elemental2.dom.DomGlobal.document;
import static org.jboss.elemento.EventType.mousemove;

public class App implements EntryPoint {

    @Override
    public void onModuleLoad() {
        HTMLElement el = Elements.span().style("font-weight: bold;").element();
        Elements.body().add("mouse position: ").add(el);
        RxElemento.fromEvent(document, mousemove)
                .subscribe(ev -> el.textContent = ev.clientX + ", " + ev.clientY);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们已经用页面`document`中的`mouse position`文本、`span`和`mousemove`事件的事件监听器替换了`hello world`。这是一个非常简单的代码，但是有很多好东西！`Elements.span()`是一个*element to*builder，看看使用它的 fluent API 设置样式和获取元素有多容易。`HTMLElement`是一个 *elemental2* 类型，几乎就像直接使用浏览器 API 一样。`RxElemento.fromEvent`是一个 *RxGWT* 实用程序，它将 *elemento* 类型安全事件与 *RxJava* 结合在一起。这是`fromEvent`返回的实际类型`Observable<MouseEvent>`，即它是具体事件类型的`Observable`。这个可观察对象将为每个订阅添加一个新的事件侦听器，并在取消订阅时删除它。最后一行是将使用鼠标客户端坐标更新`el` span 元素的实际订阅。

在这个小例子中，我们将一个纯 Java lib 与一个浏览器本地 API 混合起来订阅和编写事件。它太简单了，可能不会给人留下深刻印象，但是如果你仔细想想，它是非常棒和强大的🤯！实际上，如果你是一个 android 开发者，并且你有很多关于 UI、MVP、Dagger、RxJava 和所有那些管理你的 UI 状态的花哨东西的经验，那么这应该是你开始应用所有这些知识来创建基于 web 的 UI 应用程序的起点！

[第四步不同。](https://github.com/ibaca/gsg-hello-app/commit/b384fc0e)

## 结论👋

免责声明；这里没有大的结论，但我希望你喜欢它，并让你有足够的好奇心继续挖掘 GWT。GWT 是一个超级强大的传输者。我们甚至还没有谈到它如何管理资源(JS、CSS、HTML、翻译、图像等等)并将其全部嵌入到一个 JS 文件中。它是巨大的，但是即使忽略所有那些额外的实用程序，仅仅作为一个“简单的”transpiler，它对于那些想要编写浏览器代码的 Java 专家来说已经足够有用了。

如果你喜欢这个介绍，我会试着做一些客户端-服务器教程。我真的认为只有客户端的应用程序是最好的起点，但是没错，人们喜欢用同一种语言编写客户端和服务器的想法，可能几乎每个使用 GWT 的人都是带着这个目标使用它的。但是，当然，本教程也将是一个相当有偏见的😬。

有一个项目是从 GWT 开始的，使用的是*弹簧靴方法*。我个人更喜欢本指南中解释的手动方法，因为*弹簧引导方法*增加了许多依赖性，让你感觉有点失去控制。但是，如果你喜欢它，那么你会喜欢这个项目 [gwt-boot-samples](https://github.com/gwtboot/gwt-boot-samples) ，它甚至更容易开始。无论如何，这个项目有各种现代流行的解决方案，所以即使你不使用依赖项本身，你也可以得到启发并手动配置它。

最后，我关于*求助*的建议:

*   非常明显，但没错，这是解决标准问题的最佳地方
*   gitter gwt - gitter chat，这是一个非常活跃的地方，如果你不知道或者有一个快速的问题，这是最好的地方
*   包含了许多老问题，也有各种特定的贡献者小组，指导委员会，各种经典的 gwt 库，等等

那三个地方应该够了。但是，如果你想找到更多的东西，也许是一些惊喜，那么去 [awesome gwt](https://gwt.zeef.com/awesomegwt) 那里有一个有用的 gwt 资源、教程、库、视频、博客等等的无限列表！