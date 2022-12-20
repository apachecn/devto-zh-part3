# 用 IntelliJ 创建基于 Kotlin 的 Azure 函数-第 1 部分

> 原文：<https://dev.to/azure/creating-kotlin-based-azure-function-with-intellij-486b>

Kotlin 是一种新兴的开发语言，在最近的堆栈溢出开发者调查中被评为最受欢迎的语言之一。它正在成为 Android 开发的默认语言，并受到那些投资 Java 并寻找更现代的 JVM 语言的人的关注。

## 1。参考文献

*   [用 Java 和 IntelliJ 创建你的第一个 Azure 函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-maven-intellij?WT.mc_id=devto-blog-dglover)
*   [Azure Functions Java 开发者指南](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-java?WT.mc_id=devto-blog-dglover)
*   【Azure Java 函数库
*   [注册 Azure 函数绑定扩展](https://docs.microsoft.com/bs-latn-ba/azure/azure-functions/functions-bindings-register?WT.mc_id=devto-blog-dglover)
*   用于 Azure 功能的 Maven 插件
*   [Azure IntelliJ 工具包](https://docs.microsoft.com/en-us/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable&WT.mc_id=devto-blog-dglover)

## 2。建立你的开发环境

要使用 Java 和 IntelliJ 开发功能，请安装以下软件:

*   Java 开发者工具包第八版 (JDK)
*   [Apache Maven](https://maven.apache.org) ，版本 3.0 或更高
*   [IntelliJ IDEA](https://www.jetbrains.com/idea/download) ，Maven 的社区或终极版本
*   [Azure CLI](https://docs.microsoft.com/cli/azure?WT.mc_id=devto-blog-dglover)
*   [Azure Functions 核心工具，第二版](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local?WT.mc_id=devto-blog-dglover)

> 要完成本文中的步骤，必须将 JAVA_HOME 环境变量设置为 JDK 的安装位置。

## 3。创建一个 Azure 函数项目

1.  在 IntelliJ IDEA 中，选择**创建新项目**。
2.  在**新项目**窗口中，从左侧窗格中选择 **Maven** 。
3.  选中**从原型**创建复选框，然后为[azure-functions-原型](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)选择**添加原型**。
4.  在**添加原型**窗口中，填写如下字段:
    *   *GroupId*:com . Microsoft . azure
    *   artifact id:azure-functions-原型
    *   *版本*:使用来自[中央存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)的最新版本
5.  选择**确定**，然后选择**下一个**。
6.  输入当前项目的详细信息，然后选择**完成**。

Maven 在一个新文件夹中创建项目文件，其名称与 *ArtifactId* 值相同。项目生成的代码是一个简单的 [HTTP 触发的](https://dev.to/azure/azure-functions/functions-bindings-http-webhook)函数，它回显触发 HTTP 请求的主体。

[![create new Kotlin project](img/92e69056824f16aacc6352d68b01ce25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x9i6J8cT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/create-new-project.jpg)

## 4。指定美芬的基本要素

定义项目的 GroupId 和 ArtifactId。此信息将添加到项目 pom.xml 中。

ArtifactId 是 pom.xml 文件中 Azure 函数名称的一部分。

更多信息参见[创建新的 Maven 项目](https://www.jetbrains.com/help/idea/2018.3/maven-support.html?utm_content=2018.3&utm_medium=link&utm_source=product&utm_campaign=IC#create_new_maven_project)

[![create new kotlin project](img/708757141e6f805d7017af220f6cd89f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TIHjLwLq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/create-new-project-properties.jpg)

## 5。确认 Maven 项目设置

[![Maven summary](img/fd45fd4c630ff3d50ea176464b67c00c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eGZPZ4mW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/create-new-project-summary.jpg)

## 6。确认项目名称和地点

[![create new kotlin project](img/24538120906175fc010416b02e640f5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dxmKIZPc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/create-new-project-confirmation.jpg)

将创建项目结构。

## 7。启用自动导入

[![create new kotlin project](img/9964e0d8b2b91869357185ee8c3f2dc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bUtKLDUl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/create-new-project-enable-auto-import.jpg)

## 8。天蓝色配置

默认情况下，创建项目时会打开 pom.xml 文件。

您可以在 pom.xml 文件中设置以下项目属性

1.  Azure 功能应用名称
2.  Azure 功能应用程序区域

运行以下命令获取完整的区域列表。通过返回的 JSON 数组中的“name”字段选择位置。

```
az account list-locations 
```

[![create new kotlin project](img/b95c6af522b62a82d07fd8cdf12d1ba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---XsR97jF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/create-new-project-skelton.jpg)

## 9。打开默认的 Http 触发器

Azure Functions Maven 原型将创建一个示例 Http 触发器。您将通过导航 **src** 项目目录找到它。

[![create new kotlin project](img/dacf7d3f8e081c882314308b91b3bee7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--10nVyx-p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-default-http-trigger.jpg)

## 10。将 Java Azure 函数转换为 Kotlin 文件

右键单击默认的 Java 函数**函数**，选择**将 Java 文件转换为 Kotlin 文件**。瞧，Java 文件神奇地将一个 Kotlin 项目。

[![create new kotlin project](img/b8d33d61647ffac3a11317aa41e7cb7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E-lRtuaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-convert-to-kotlin.jpg)

从 Java 转换的默认 Azure 函数现在看起来会像漂亮的 Kotlin。

[![create new kotlin project](img/306fb2ec70678006cd68b6abce02f826.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p2eWm5rh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-converted-to-kotlin.jpg)

## 11。为科特林配置项目

从**工具**菜单中，选择**科特林**，然后**在项目**中配置科特林

[![create new kotlin project](img/a6ec3504796e25d4d73ed6f32ef127bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1MZZFn5S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-configure-kotlin-in-project.jpg)

## 12。启用所有包含 Kotlin 文件的模块

[![create new kotlin project](img/e56cc66ef70b05d498477e84cf248945.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pCAQTjM6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-configure-kotlin-in-project-confirm.jpg)

## 13。清理并打包项目

从 Maven 弹出选项卡中，展开**生命周期**，然后运行**清理**，接着运行**打包**命令。这将构建 Kotlin 项目。

[![create new kotlin project](img/c722e7e775c41f8724da13e394695b32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xuNXoBFM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-clean-package-results.jpg)

## 14。执行 Azure 功能运行

从 Maven 弹出标签中，展开**插件**，然后运行 **azure-functions:run** 。这将启动 Azure Functions 核心工具并引导你的 Kotlin Azure 函数。

要测试该功能，请单击[http://localhost:7071/API/http trigger-Java](http://localhost:7071/api/HttpTrigger-Java)链接

[![create new kotlin project](img/9acdc84527d8b56739b20386e38d8e69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z4RZK9yH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-azure-functions-run.jpg)

## 15。在查询字符串上传递一个参数

在浏览器中，将名称参数添加到查询字符串中。比如[http://localhost:7071/API/http trigger-Java？name=dave](http://localhost:7071/api/HttpTrigger-Java?name=dave) 您将看到网页回显传入的 name 值。

[![create new kotlin project](img/9b7c0dac9ddb841e7a632f9cd3fae4f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TXydKJuz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-test-http-trigger.jpg)

## 16。停止功能

单击停止图标停止运行该功能。

[![create new kotlin project](img/194df50796e293557af322ae08e0db67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LXvTzmkh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-azure-functions-stop.jpg)

## 17。启用调试

鼠标右键点击 **azure-functions:运行** Maven 原型并选择**创建‘手套箱-功能’...**

[![create new kotlin project](img/0b36d8bde36953fdba1339ec5f5fe594.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pDHPwVnu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-enable-debug-azure-function-maven-debug.jpg)

## 18。创建运行/调试配置

将 **-DenableDebug** 添加到命令行。

[![create new kotlin project](img/d490dd1571dd17423e5b24c1145228ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lGV59pbJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-enable-debug-azure-function-maven-debug-configure.jpg)

## 19。启用 Java 调试器附加

从**运行**菜单，选择**编辑配置**

[![create new kotlin project](img/ff5ece121a66694cc27c7863bf720692.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z7JzC9Df--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-run-edit-configurations.jpg)

## 20。添加新配置

点击 **+** 符号，然后选择**遥控**。

[![create new kotlin project](img/2ce679de850314aa23a9ed1ca0a70579.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eNzXiKH---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-run-edit-configurations-add-remote-config.jpg)

## 21。命名新配置

在这种情况下，配置被命名为**附加调试器**

[![create new kotlin project](img/c0d47aff50e454e455ff2dac0a8906b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MwQSFQeu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-run-edit-configurations-add-remote-attach-debugger.jpg)

## 22。在 Kotlin Azure 函数源码中设置断点

[![create new kotlin project](img/142446bd784bebd81681fc94600406fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WIFfC59L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-debug-set-breakpoint.jpg)

## 23。运行调试器启用配置

从**运行/调试配置选择器**中选择 Maven azure-functions:运行配置。然后单击绿色的开始图标或按 Shift+F10。

[![create new kotlin project](img/940c6c5801f64fad073d68c02d2e8da9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ACVCbamo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-debug-run-debug-enabled-maven.jpg)

## 24。附上调试器

从**运行/调试配置选择器**中选择**附加调试器**配置，点击绿色启动调试器图标或按 Shift+F9。

[![create new kotlin project](img/c95bf2ce3c80061754988e5d52edef96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WlOOyN5z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-debug-debugger-attached.jpg)

## 25。在调试模式下启动 Http 触发器

点击[http://localhost:7071/API/Http Trigger-Java](http://localhost:7071/api/HttpTrigger-Java)链接，启动 Http 触发器。

[![create new kotlin project](img/40318f7a6f38ed91885df949d39eb255.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fHN7izrc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-debug-switch-to-run.jpg)

## 26。用调试器单步调试 Http 触发器 Azure 函数

使用调试器控件，逐句通过 Azure 函数代码。

[![kotlin debugging](img/209fdbe9970bc94a5b755884b1d6b577.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rG3P69HW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-debug-step-through-code.jpg)

## 27。停止调试器和 Azure 功能

单击停止图标分离调试器并停止 Azure 功能

[![create new kotlin project](img/39255def4ed0496b2185317378bd29da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E2wPc4OI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/project-debugger-stop.jpg)

## 28。添加新的 Azure 函数触发器

[azure-functions:添加](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-functions-maven-plugin/readme?view=azure-java-stable#azure-functionsadd&WT.mc_id=devto-blog-dglover)

azure-functions:add Maven 原型支持以下触发器类型。

*   HTTP 触发器
*   Azure 存储 Blob 触发器
*   Azure 存储队列触发器
*   定时器触发器
*   事件网格触发器
*   事件中心触发器
*   Cosmos DB 触发器
*   服务总线队列触发器
*   服务总线主题触发器

从 Maven 弹出菜单中的插件下，选择 **azure-functions:add**

[![Azure functions add](img/71b5c8c106a0398a6701d4232ac11566.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OaaMvU-v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/azure-function-add.jpg)

## 29。将 Kotlin Azure 功能部署到 Azure

[azure-功能:部署](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-functions-maven-plugin/readme?view=azure-java-stable#azure-functionsdeploy&WT.mc_id=devto-blog-dglover)

将暂存目录部署到目标 Azure 功能。如果目标 Azure Functions 尚不存在，将会创建它。

[![Azure functions deploy](img/a5490c2e9c4bea1465a09e12d7deab77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84KroAyP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/azure-function-deploy.jpg)

## 30。构建端到端安全、可扩展的物联网无服务器解决方案

下一篇文章将是关于用 Kotlin Azure 函数和 Azure SignalR 创建一个端到端的物联网无服务器解决方案。

[![end to end solution](img/cefb3a9a1781302b5993969693c0ca49.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iFJ8CkSw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/gloveboxes/Create-your-first-Azure-function-with-Kotlin-and-IntelliJ/master/resources/kotlin-end-to-end-iot-serverless-functions-signalr.png)