# 如何记录 Kotlin 项目

> 原文：<https://dev.to/cjbrooks12/how-to-document-a-kotlin-project-edc>

文档远不是软件开发中最性感的东西，但它肯定是必要的。回想一下你每天使用的所有库和工具；它们都必须有很好的文档，否则，你永远也不会知道如何使用它们。

因此，如果你正在建立一个库，你想让人们使用它，它也应该有好的文档。如果这个库碰巧是用 Kotlin 编写的，那么你很幸运，因为在一个叫做[兰花](https://orchid.netlify.com/)的新工具的帮助下，创建一个伟大的文档网站实际上比你想象的要容易得多。

本教程将带您了解如何使用 Orchid 为您的 Kotlin 项目创建一个基本的文档站点，包括 Dokka 代码文档、wiki 和 changelogs，并将其部署到 Github 页面。如果你想直接进入一个工作项目，你可以在[或教程示例项目](https://github.com/JavaEden/OrchidTutorials/tree/master/kotlin-site)中找到这里描述的一切。

# 目录

*   [入门](#getting-started)
    *   [应用程序设置](#app-setup)
    *   [兰花设置](#orchid-setup)
*   [添加内容](#adding-content)
    *   [主页](#homepage)
    *   [站点信息/主题配置](#site-infotheme-configuration)
    *   [维基](#wiki)
    *   [变更日志](#changelog)
*   [科特林代码文档](#kotlin-code-docs)
    *   [设置插件](#setting-up-the-plugin)
    *   [创建菜单](#creating-the-menus)
*   [部署在 Github 页面上](#deploy-on-github-pages)
*   [结论](#conclusion)

## 入门

我们将在这个项目中使用 Gradle，但是如果您喜欢 Maven，这个过程几乎是相同的。

首先，我们需要一个 Kotlin 项目。理想情况下，它会在自己的 Gradle 子项目中，比如`:app`，而文档会在另一个子项目中，比如`:docs`。为了便于说明，我将快速设置一个示例应用程序，但是您可以使用自己的库，直接跳到 [*兰花设置*部分](#orchid-setup)。

### App 设置

从一个基本的 Gradle 项目开始，我们需要创建两个子项目。这包括在`settings.gradle`中告诉 Gradle 我们的项目，并在`:app`项目的`build.gradle`
中设置 Kotlin 插件

```
// settings.gradle
rootProject.name = 'Kotlin Project'

include ':app' 
```

Enter fullscreen mode Exit fullscreen mode

```
// app/build.gradle
plugins {
    id 'org.jetbrains.kotlin.jvm' version '1.3.21'
    id 'application'
}
repositories {
    jcenter()
}
dependencies {
    compile 'org.jetbrains.kotlin:kotlin-stdlib-jdk8:1.3.21'
}
application {
    mainClassName = 'com.example.MainKt'
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备开始添加和运行 Kotlin 文件。为了简单起见，我们使用了`application`插件，这样我们就可以在 Kotlin 项目中运行一个`main`方法。我们将应用程序设置为一个`Application`接口，一个实现该接口的`ExampleApplication`类，并让 main 方法运行该应用程序。虽然它非常简单，但作为一个例子，它将足够好地工作，这是一种兰花将为它生成的文档。

所以让我们把这些都准备好！

```
// app/src/main/kotlin/main.kt
package com.example

/**
 * Run the ExampleApplication
 */
fun main() {
    ExampleApplication().run {
        println("Running $name version $version...")
        start("3")
        println("Finished!")
    }
}

/**
 * An Application that can be run with arbitrary arguments.
 */
interface Application {

    /**
     * The name of the application
     */
    val name: String

    /**
     * The current application version
     */
    val version: String

    /**
     * Run this application with the arguments provided
     *
     * @param args the args to configure the application
     */
    fun start(vararg args: String)
}

class ExampleApplication : Application {
    override val version: String = "1.0.0"
    override val name: String = "Example Application"

    override fun start(vararg args: String) {
        for(i in 1..args.first().toInt()) {
            println("$i...")
            Thread.sleep(1000)
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行`./gradlew :app:run`了，Gradle 将编译 Kotlin 源代码并运行它的 main 方法。您应该会看到下面的输出:

```
./gradlew :app:run

> Task :app:run
Running Example Application version 1.0.0...
1...
2...
3...
Finished!

BUILD SUCCESSFUL in 4s
2 actionable tasks: 2 executed 
```

Enter fullscreen mode Exit fullscreen mode

### 兰花设置

现在让我们准备好使用兰花的项目。Orchid 是一个新的静态站点生成器，用 Kotlin 编写，专门用于生成代码文档。它支持 Kotlin 源代码，但也有 Java、Groovy 和 Swift 插件，这些插件的工作方式与本教程非常相似。

兰花作为一个 Gradle 插件运行，最好是给它自己的 Gradle 子项目。因此，让我们用一个新的`:docs`项目来设置我们的`settings.gradle`，我们还将设置`docs/build.gradle` :

```
// settings.gradle
rootProject.name = 'Kotlin Project'

include ':app'
include ':docs' 
```

Enter fullscreen mode Exit fullscreen mode

```
// docs/build.gradle
// 1\. Apply Orchid plugin
plugins {
    id "com.eden.orchidPlugin" version "0.16.0"
}

// 2\. Include Orchid dependencies
dependencies {
    orchidRuntime "io.github.javaeden.orchid:OrchidDocs:0.16.0"
    orchidRuntime "io.github.javaeden.orchid:OrchidKotlindoc:0.16.0"
    orchidRuntime "io.github.javaeden.orchid:OrchidPluginDocs:0.16.0"
}

// 3\. Get dependencies from JCenter and Kotlinx Bintray repo
repositories {
    jcenter()
    maven { url = "https://kotlin.bintray.com/kotlinx/" }
}

// 4\. Use the 'Editorial' theme, and set the URL it will have on Github Pages
orchid {
    theme = "Editorial"
    baseUrl = "http://username.github.io/project"
    version = "1.0.0"
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是运行您的兰花网站所需的一切！我们还需要做一些事情来建立 wiki，并告诉它在哪里可以找到我们的 Kotlin 代码，但你现在可以用`./gradlew :docs:orchidServe`运行兰花，并在 [http://localhost:8080](http://localhost:8080) 上查看网站。它应该会给出如下输出:

```
./gradlew :docs:orchidServe

> Task :docs:orchidServe
Using the following modules: 
-------------------------
 * com.eden.orchid.StandardModule

Auto-loaded modules: 
-------------------------
 * com.eden.orchid.editorial.EditorialModule
 * com.eden.orchid.impl.compilers.markdown.FlexmarkModule
 * com.eden.orchid.impl.compilers.pebble.PebbleModule
 * com.eden.orchid.kotlindoc.KotlindocModule
 * com.eden.orchid.pages.PagesModule
 * com.eden.orchid.search.SearchModule
 * com.eden.orchid.wiki.WikiModule

Flag values: 
-------------------------
-adminTheme: Default
-baseUrl: http://username.github.io/project
-defaultTemplateExtension: peb
-dest: ...
-dryDeploy: false
-environment: debug
-logLevel: VERBOSE
-port: 8080
-src: ...
-task: serve
-theme: Editorial
-version: 1.0.0

[INFO] Orchid: Running Orchid version 0.16.0, site version unspecified in debug environment
[INFO] OrchidWebserver: Webserver Running at http://localhost:8080
[INFO] OrchidWebsocket: Websocket running at http://localhost:8081/
[INFO] TaskServiceImpl: Build Starting...
[INFO] GeneratorServiceImpl: Indexing [10000: assets]
[INFO] GeneratorServiceImpl: Indexing [1000: home]
[INFO] GeneratorServiceImpl: Indexing [1000: kotlindoc]
[INFO] GeneratorServiceImpl: Indexing [1000: pages]
[INFO] GeneratorServiceImpl: Indexing [1000: wiki]
[INFO] GeneratorServiceImpl: Indexing [11: sitemap]
[INFO] GeneratorServiceImpl: Indexing [10: indices]
[INFO] GeneratorServiceImpl: Generating [10000: assets]
[INFO] GeneratorServiceImpl: Generating [1000: home]
[INFO] GeneratorServiceImpl: Generating [1000: kotlindoc]
[INFO] GeneratorServiceImpl: Generating [1000: pages]
[INFO] GeneratorServiceImpl: Generating [1000: wiki]
[INFO] GeneratorServiceImpl: Generating [11: sitemap]
[INFO] GeneratorServiceImpl: Generating [10: indices]

Build Metrics: 
┌───────┬────────────┬───────────────┬─────────────────┬───────────────────────────┬─────────────────────────────┐
│       │ Page Count │ Indexing Time │ Generation Time │ Mean Page Generation Time │ Median Page Generation Time │
├───────┼────────────┼───────────────┼─────────────────┼───────────────────────────┼─────────────────────────────┤
│  home │     1      │     54ms      │      481ms      │           472ms           │            472ms            │
├───────┼────────────┼───────────────┼─────────────────┼───────────────────────────┼─────────────────────────────┤
│ TOTAL │          1 │      3s 496ms │           520ms │                     472ms │                       472ms │
└───────┴────────────┴───────────────┴─────────────────┴───────────────────────────┴─────────────────────────────┘

Build Complete
Generated 1 page in 4s 18ms

[WARN] Warnings:
[WARN] WikiGenerator: 
[WARN]     - Could not find wiki summary page in 'wiki/'

Webserver Running at http://localhost:8080
Hit [CTRL-C] to stop the server and quit Orchid 
```

Enter fullscreen mode Exit fullscreen mode

您还会看到在 localhost:8080 上提供的基本站点，看起来像:

[![empty Orchid site](img/53af036d44a18098154a9719fc6ab3c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FDEExEMJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0w4awux9ya5vm1a84z5i.png)

但是让我们进入下一步:向站点添加内容！

## 添加内容

### 首页

任何人登陆你的网站首先看到的是你的主页。兰花基于站点的*资源*的根目录下的`homepage.md`文件创建这个页面，默认情况下，这个根目录位于`src/orchid/resources`中。让我们从创建这个文件开始，并向其中添加我们项目的简短描述。

```
// docs/src/orchid/resources/homepage.md
# Kotlin Project

This is a short description of this project. 
```

Enter fullscreen mode Exit fullscreen mode

[![Orchid Site with Homepage content](img/53f35c5958b4a9f44ce2fbd4c1a712d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5vatUsgK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q9olagjvmkeud0jwxgnh.png)

添加到主页的一个常见的东西是你的项目的`README`，它有像徽章、基本设置说明等东西。我们不想将自述文件中的所有内容都复制到我们的主页文件中，所以让我们告诉 Orchid 将自述文件放在主页上吧！

为此，我们将在主页文件的顶部添加一个**首页事件**部分，并在首页事件中添加“Readme”**组件**。如果你以前使用过 Jekyll 或其他静态站点生成器，Orchid 以完全相同的方式处理前面的问题:在一对三虚线之间的 YAML。

```
// docs/src/orchid/resources/homepage.md
-------- components:
 - type: 'pageContent'
 - type: 'readme'
--------
# Kotlin Project

This is a short description of this project. 
```

Enter fullscreen mode Exit fullscreen mode

[![Homepage with Readme contents](img/dd57078621f1f376beab282fb2b487ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cNFJZ-qM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p6yuklpfg3fxf2x5inzj.png)

Orchid 的组件只是一个“块”列表，这些块按顺序呈现在页面上。组件有很多不同的类型，不同的插件可以自己添加。这里，我们添加了`readme`组件，它在项目中搜索自述文件并将其添加到页面中。我们还添加了`pageContent`组件，它添加了`homepage.md`的降价内容。如果你没有定义任何组件，这个是自动添加的，但是如果你使用组件，你必须自己添加。

主页大概就这些了。但是我们的网站看起来有点无聊，主题希望我们提供一些信息。因此，让我们继续配置主题。

### 站点信息/主题配置

兰花在资源的根中用一个`config.yml`来处理配置。在其中，我们将添加一些关于我们网站的基本信息，我们还可以配置我们的主题，例如改变它的颜色。

这个配置文件包括一些在`site`下的配置，这是你选择的任何主题通常使用的公共信息，以及一些在`Editorial`下的特定于主题的配置。如果你记得，这个键和我们在`docs/build.gradle`中设置的主题值是一样的。对于主题，我们正在改变网站的强调颜色，并为社交链接加入 Github 项目。

```
# docs/src/orchid/resources/config.yml
site:
  about:
    siteName: Kotlin Project
    siteDescription: This is a short description of this project.
Editorial:
  primaryColor: '#DE9149'
  social:
    github: 'username/project' 
```

Enter fullscreen mode Exit fullscreen mode

[![Orchid site with some configuration](img/e93938aa44dc23bbc6187e101e790751.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SqpZbb7W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d2nyfiehj7tdrdnt9ugl.png)

但是这些配置值并不是凭空出现的。如果你的网站在本地服务时访问 http://localhost:8080/admin ，你可以看到兰花的管理面板。在这里，您可以找到所有可用于定制您的主题、组件和其他任何东西的选项。

[![Orchid Admin Panel Theme Options](img/dd845e845c8098ff5d01cb3c741ee240.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vrOrccx---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dnfh7gg6cvpyryin9200.png)

### 维基

现在我们的网站看起来更独特了，我们可以开始添加一些额外的内容来帮助我们的用户学习如何使用这个项目。兰花的维基插件将在这里帮助我们，允许我们创建一个容易导航的知识库，引导用户通过他们需要的一切来进行项目。

这个插件以一个`wiki/summary.md`文件开始。在这里，我们将添加到维基子页面的相对链接。然后，兰花将在您的网站上为每个条目创建一个新的页面，并将它们连接在一起，以便它们按照摘要中声明的顺序从一个链接到下一个。

```
// docs/src/orchid/resources/wiki/summary.md
- [Installation](installation.md)
- [Basic Configuration](configuration.md)
- [Features](features.md)
 - [Feature One](features/one.md)
 - [Feature Two](features/two.md)
- [Extending](extending.md) 
```

Enter fullscreen mode Exit fullscreen mode

我将把创建这些 wiki 条目的工作交给您。它们的工作方式与主页文件完全相同，可以包含降价内容和带有任何您想要的组件的首页内容部分。

现在，如果你访问[http://localhost:8080/wiki](http://localhost:8080/wiki)，你将能够开始浏览你的 wiki。兰花的一个非常好的特性是内置的静态搜索。您所有的 wiki 页面都支持全文搜索，以便于在您的站点中查找任何内容。wiki 的所有单个页面也通过前向和后向链接连接在一起。

[![Wiki summary with search results](img/92a7bc9c5cce74ec685602ded4903c57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O-7x9A9i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5r4apzo10ip7wtnpqxos.png)

[![Wiki entry with next and previous page links](img/f1a8a9b6c53fcc4727d580308973c6c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ru-RGq42--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nlpgex5lkcx1gjv8seur.png)

我们需要做的最后一件事是开始设置我们的网站菜单，并包含一个到这个 wiki 的链接。你可以在`config.yml`中做到这一点。将其更改为如下所示:

```
# docs/src/orchid/resources/config.yml
site:
  about:
    siteName: Kotlin Project
    siteDescription: This is a short description of this project.

Editorial:
  primaryColor: '#DE9149'
  social:
    github: 'username/project'
  menu:
    - type: 'separator'
      title: 'Wiki'
    - type: 'wiki' 
```

Enter fullscreen mode Exit fullscreen mode

我已经在`Editorial`主题配置中添加了一个`menu`属性。与组件类似，菜单包含一个按顺序呈现的菜单项类型列表。单个菜单项配置可能会在实际菜单中产生一个以上的条目，如`wiki`项，它会为您的维基中的每个页面添加一个到侧边栏的链接。

[![Wiki Page with Wiki Menu Items](img/c9610f2b62f24f2de63c506984c28a95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wstg6o0n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k7f8mvcoyjojnw00htyd.png)

### 变更日志

我们想要添加到我们站点的最后一部分内容是一个变更日志。通过这种方式，用户可以跟踪您对项目所做的更改，而 Orchid 也可以使用相同的信息在 Github 上创建发布。

每个 Changelog 条目都被设置为 resources `changelog`目录中的一个文件。每个条目都应该有一个带有版本名称的`version`属性的前端事件部分(作为语义版本)。所以让我们继续创建一些变更日志条目，比如`changelog/1.0.0.md`，如果你愿意，也许还可以创建一些其他的条目:

```
// docs/src/orchid/resources/changelog/1.0.0.md
--------
version: '1.0.0'
-------- - Project is now ready for production! 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，changelog 插件不会为 changelog 条目列表创建页面，但是我们可以创建一个*静态页面*来显示 Changelog 条目列表。静态页面是降价文件，就像维基和主页文件一样，在`pages`目录中。我们将创建一个`changelog.md`文件，并给它一个`changelog`组件，以便在页面[上按顺序呈现我们的版本](http://localhost:8080/changelog:)

```
// docs/src/orchid/resources/pages/changelog.md
-------- components:
 - type: changelog
-------- 
```

Enter fullscreen mode Exit fullscreen mode

[![Changelog entries](img/c0676f446c62ab0ac1f645b7e9160ba4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yyvQu7Qb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hmwr5trhhzyszyubzesz.png)

让我们也将它添加到我们的站点菜单中，这样用户可以很容易地找到最近的变化:

```
# docs/src/orchid/resources/config.yml
site:
  about:
    siteName: Kotlin Project
    siteDescription: This is a short description of this project.

Editorial:
  primaryColor: '#DE9149'
  social:
    github: 'username/project'
  menu:
    - type: 'separator'
      title: 'Wiki'
    - type: 'wiki'
    - type: 'page'
      itemId: 'Changelog' 
```

Enter fullscreen mode Exit fullscreen mode

[![Menu with Changelog item](img/0e8f2c692c0c6237202d536eeafae590.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ib52Z3-R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7j1ra7v0908l3rww4ib1.png)

## 科特林代码文档

### 设置插件

现在我们可以开始记录我们的科特林资源了。首先，我们需要设置插件，并将其指向我们的另一个带有 Kotlin 源文件的项目。兰花插件已经作为`OrchidKotlindoc`的依赖项安装在`docs/build.gradle`中，所以我们只需要更新我们的`config.yml`。

如果我们回到管理面板，我们可以找到我们需要的配置值。具体来说，我们需要在侧边栏中找到“Kotlindoc”**生成器**菜单项。点击该按钮，显示我们需要添加到`config.yml`中的可用配置值:

[![Kotlindoc docs page in Orchid admin panel](img/70054b4158124b48037e086ed7f69ed6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hc6MIQ2W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ae0099ddden8rvvbvbv1.png)

这告诉我们，我们可以将下面的代码片段添加到`config.yml`中，这就是我们所需要的！我们将把`sourceDirs`设置为从 docs 站点资源到 app 项目的 Kotlin 源的相对文件路径。

```
# docs/src/orchid/resources/config.yml
kotlindoc:
  sourceDirs:
    - './../../../../app/src/main/kotlin' 
```

Enter fullscreen mode Exit fullscreen mode

现在，兰花将挂钩到 [Dokka](https://github.com/Kotlin/dokka) 来获得它需要的信息，然后为它在你的源目录(Java 和 Kotlin)中找到的每个类和包生成页面。完全限定名使用相同的路径创建嵌套目录，因此`com.example.ExampleApplication`将在您的站点中创建[http://localhost:8080/com/example/example application](http://localhost:8080/com/example/ExampleApplication)。

[![Kotlin Class page](img/833a853103f303cceaf2cf4fec167e96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gv7pDPat--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/36vmgvls13dg68rgbo4y.png)

目前，这个页面看起来不是很好。默认的标记是基于 Bootstrap 的，但是主题不是。不过，不用担心，我们可以告诉 Orchid 在我们的 Kotlin 文档页面中添加一个精简的引导样式表！

让我们把[这个要点](https://gist.github.com/cjbrooks12/7b3942d6fe18a1151aae4cfe9ed9cedb#file-orchidkotlindoc-css)下载到一个名为`assets/css/orchidKotlindoc.scss`的文件中。这是一个 SCSS 文件，兰花会自动为我们将它编译成 CSS，并添加到我们的 Kotlin 文档页面，在`config.yml`中有以下代码片段:

```
# docs/src/orchid/resources/config.yml
kotlindoc:
  sourceDirs:
    - './../../../../app/src/main/kotlin'
  pages:
    extraCss:
      - 'assets/css/orchidKotlindoc.scss' 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在页面看起来好多了！

[![Kotlin Class page with improved styling](img/8df6bd933a1f2f125b0e8292189b7c2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4Ggbnh8h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/26oxcx6tz6fft746f9p1.png)

我们可以从这个类页面导航到它的包页面，看起来非常相似。正如全文搜索在我们的 wiki 页面上工作一样，它也会在这些页面上的代码注释中找到匹配项！

[![Kotlin Package page with search results](img/73372ad5f405ed0a81ff71800d56b0ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3THyVdLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/079z6mit8d762sgpx38i.png)

### 创建菜单

我们几乎完成了我们网站的设置，只剩下一个我们需要的拼图:到我们 Kotlin 文档页面的菜单链接！我们首先将项目中所有类和包的链接添加到侧边栏。这被添加到我们之前在`config.yml`中为 wiki 页面使用的相同菜单列表中。

```
# docs/src/orchid/resources/config.yml
site:
  about:
    siteName: Kotlin Project
    siteDescription: This is a short description of this project.

Editorial:
  primaryColor: '#DE9149'
  social:
    github: 'username/project'
  menu:
    - type: 'separator'
      title: 'Wiki'
    - type: 'wiki'
    - type: 'page'
      itemId: 'Changelog'
    - type: 'separator'
      title: 'API  Docs'
    - type: 'kotlindocPackages'
    - type: 'kotlindocClasses' 
```

Enter fullscreen mode Exit fullscreen mode

[![Menu item links for all Kotlin class and package pages](img/0e06c57308e91b8f475227ad8fa5905f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--14IPiomR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7otue9dcg8y1h21jmpru.png)

这里还有一个很好的菜单。就像主题有菜单一样，*兰花中的每个页面*也可以有自己的菜单，这样菜单可能会在站点的不同部分略有变化，突出相关内容。因此，让我们为所有 Kotlin 文档页面添加一个菜单，其中包含每个方法、构造函数等的链接。在每一页上。点击其中一个菜单项会将你链接到页面上适当的*锚*，而不是将你带到一个新的页面。

```
kotlindoc:
  sourceDirs:
    - './../../../../app/src/main/kotlin'
  pages:
    extraCss:
      - 'assets/css/orchidKotlindoc.scss'
    menu:
      - type: "kotlindocClassLinks"
        includeItems: true 
```

Enter fullscreen mode Exit fullscreen mode

[![Kotlindoc Class Links](img/d73af59431430d694acde52d887bee87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D_GUzUii--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hjw96uidterf187zj7qf.png)

## 部署在 Github 页面上

我们的站点现在可以部署了！对于大多数项目来说，Github Pages 是存放文档的好地方。你所要做的就是将静态文件推送到一个库的`gh-pages`分支，Github 会为你托管它。

虽然许多静态站点生成器希望您手动提交和推送您的站点到这个分支，但是通过为您提供一个简单的部署管道，兰花实际上使它变得非常简单。因此，让我们建立 Github 页面发布。将以下代码片段添加到`config.yml`。

```
# docs/src/orchid/resources/config.yml
services:
  publications:
    stages:
      ghPages:
        branch: gh-pages
        repo: 'project'
        username: 'username' 
```

Enter fullscreen mode Exit fullscreen mode

此外，我们需要通过 Github 的认证，才能真正成功地推进到那个分支。为此，您需要创建一个 [Github 个人访问令牌](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)，并将其设置为`githubToken`环境变量。

```
export githubToken=... 
```

Enter fullscreen mode Exit fullscreen mode

现在，不用运行`./gradlew :docs:orchidServe`，只需运行`./gradlew :docs:orchidDeploy -Penv=prod`，兰花将使用您的生产基地网址构建您的网站，并在成功构建后将构建好的网站推送到您的回购的`gh-pages`分支。

但是让我们更进一步。我们总是希望确保我们的文档站点与 Github 上的发布保持同步，这样就不会有人得到误导或冲突的信息。幸运的是，兰花有另一个出版商将创建一个 Github 发布基于最新的 changelog 版本！设置和使用与`ghPages`发布者相同的`githubToken`环境变量非常简单。

```
# docs/src/orchid/resources/config.yml
services:
  publications:
    stages:
      ghPages:
        branch: gh-pages
        repo: 'project'
        username: 'username'
      githubReleases:
        repo: 'username/project' 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

至此，我们的 Kotlin 项目的 doc 站点完成了！现在，完成所有这些设置似乎是一项繁重的工作，但让我们回忆一下本网站包含的所有功能:

*   自动拉入自述文件的主页
*   在条目之间向前和向后链接的维基
*   通过语义版本化排序的 changelog
*   所有 Kotlin 和 Java 源代码的源代码文档
*   对您的 wiki 和 KDoc 评论进行全文搜索
*   自动生成的菜单项链接到 wiki 中的每个页面以及源代码中的每个类和包。
*   到特定方法、属性等的简单链接。在您的课程和包页面上
*   将呈现的站点部署到 Github 页面
*   用最新的 changelog 版本在 Github 上创建一个发行版

好多东西啊！具体来说，这通常需要一个单独的工具/构建流程/第三方服务。事实上，对于基本项目，Orchid 将以下所有工具整合到一个独特的集成平台中:

*   吉基尔博士
*   Gitbook
*   阿尔及利亚人
*   Dokka
*   发布到 Github 页面的自定义部署脚本
*   每次发布时在 Github 上手动创建发布

本教程中概述的所有内容实际上只是兰花中可用的全部功能的一个示例！兰花有不同的源代码文档，演示，博客和更多的其他插件。点击查看插件的完整列表[，或者你甚至可以制作自己的插件！](https://orchid.netlify.com/group/plugins)

感谢跟随，快乐记录！

* * *

由[迈克·丁尼恩](https://unsplash.com/photos/3ym6i13Y9LU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/) 上拍摄的封面照片，由[凯西·布鲁克斯](https://www.caseyjbrooks.com/)修改。