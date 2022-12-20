# 使用 Gradle 在 pCloudy 中运行 Espresso

> 原文：<https://dev.to/pcloudy/run-espresso-in-pcloudy-using-gradle-3nl>

你知道谷歌为什么选择 Gradle 作为 Android Studio 的构建系统吗？许多 Android 开发人员工作在具有不同技术栈的异构环境中。Gradle 解决了开发人员面临的一些最困难的问题，如如何自动化应用程序的测试，以及如何管理依赖关系和变化，使专业开发人员只需点击一下鼠标就可以开发他们的应用程序的变化。这就是为什么 pCloudy 想出了一个新的更新，现在你可以在 pCloudy 设备上用 Gradle 运行 Espresso。在学习如何在 pCloudy 设备上使用 Gradle 运行 Espresso 之前，让我们先简单介绍一下 Espresso 和 Gradle。

Espresso 是一个 Android 测试框架，它可以自动将你的测试动作与你的应用程序的用户界面同步。它还让测试一直等到所有后台活动完成。

Gradle 是一个开源的高级构建工具，允许无缝执行任务。它使用领域特定语言，并且基于 Groovy 和 Kotlin。这是一个基于插件的系统，所以如果你想从源代码中自动构建一些包，那么你可以用 Java 编写完整的插件并与世界分享。

Gradle 允许高效且可重复地使用 espresso 和 [Test Orchestrator](https://www.pcloudy.com/appium-vs-espresso-the-most-popular-automation-testing-framework-in-2019/) ，后者允许对您运行测试的方式进行自动且微调的控制。您可以决定运行哪些特定的测试套件来将测试用例分布到不同的设备上。它是开发者的首选，因为它允许深度单元和功能测试装备。

在 [pCloudy](https://www.pcloudy.com)
上用 Espresso 和 Gradle 启动你的 DevOps 你会在你的机器上用 Android Studio 和 Gradle 运行 Espresso。但是，测试将在 pCloudy 设备上运行。我们提供了一个 pCloudy Espresso 脚本，您需要将该脚本放在项目的工作区中。除了 Gradle 脚本，您还将获得一个配置文件。gradle 脚本将从这个配置文件中读取输入参数。

然后你运行 Gradle 来调用脚本，该脚本将把你的应用程序 APK、测试 APK 和其他 APK 文件上传到 pCloudy。它会获得一个设备来运行你的测试，它会执行这些测试，它会将正在发生的状态报告给 Android Studio。测试用例运行后，您可以看到详细的报告，之后 gradle 脚本将释放设备供其他用户使用。

有一个一次性的设置，您需要做的是将 gradle 脚本放入工作区，并且您需要填写配置文件。因此，当您运行 gradle 脚本时，它将为您完成所有任务并生成报告。

在 pcloudy 上用 Gradle 运行 Espresso 的步骤

1.  从这里下载 espresso starter pack 并解压
2.  您将找到三个文件，a . pCloudy _ Espresso . jar b . config . properties c . build.gradle . sample(这是一个示例 build.gradle，展示了如何更改 build . grad le 以添加 pCloudy Espresso jar。)
3.  将 build.gradle.Sample 文件的内容复制到 Android 应用程序的 build.gradle 文件中，以注册新的 gradle 任务并根据需要进行更新(见下图)

1.  将 config.properties 文件放在 android 工作区中与 build.gradle 文件相同的目录下。

1.  按照文件注释中的说明更新 config.properties 文件。

2.  在您的 Android 工作区中，通过键入 gradle 任务的名称来运行命令，如下所示。

1.  一旦测试执行完成，您将获得测试执行报告的 URL。报告中的一些字段目前为空。我们将在后续阶段修复它们。

gradle 的使用使得在 pCloudy 设备上运行 Espresso 测试变得更加容易。我们可以只使用配置文件，在 pCloudy 设备上轻松运行测试脚本。这将节省时间和精力，因为它进一步简化了 pCloudy 中的应用程序测试过程。

您可以观看我们的网络研讨会了解更多信息。

[https://www.youtube.com/watch?v=TA9PVScCLU8](https://www.youtube.com/watch?v=TA9PVScCLU8)