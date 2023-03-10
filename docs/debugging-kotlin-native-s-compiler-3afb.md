# 调试 Kotlin 本地编译器

> 原文：<https://dev.to/touchlab/debugging-kotlin-native-s-compiler-3afb>

为了在 Kotlin native 中将[泛型添加到头文件输出](https://github.com/JetBrains/kotlin-native/pull/2850)中，我不得不使用调试器对 Kotlin Native 编译器进行逆向工程👎。将调试器附加到 Konan 编译器需要几个步骤，但是相对简单。

# 第一步:构建科特林原生

您需要[克隆 KN](https://github.com/JetBrains/kotlin-native/) 并确保创建一个指向您想要使用的版本的分支。正如文档所述，这至少需要一个小时，根据我的经验，这是乐观的，但它通常是一个无故障的构建。散散步/小睡一会儿。

# 第二步:创建样本项目

当您遇到编译问题时，可能是在一个更大的应用程序的上下文中。虽然您可以直接对此进行调试，但我建议您制作一个最小的样本来处理相同的问题。这会节省你的时间。

# 第三步:配置样本项目

您需要将示例项目指向您的本地 Konan 构建，并告诉编译器您想要附加一个调试器。将以下内容添加到 gradle.properties 中:

org . jetbrains . Kotlin . Native . home =[Koltin Native Clone Dir]/dist
org . jetbrains . Kotlin . Native . JVM args =-agent lib:jdwp = transport = dt _ socket，server=y，suspend=y，address=5006
用您在其中构建 KN 的任何目录替换[Kotlin Native Clone Dir]。这将告诉 kotlin 插件使用它而不是常规的编译器和运行时。第二行让 konan 编译器等待远程调试器连接。

# 第四步:配置远程调试器(简单的部分)

在 Intellij 中打开 Kotlin 原生 repo(这是一个 gradle 项目)。打开后，创建一个远程调试器配置。

### 编辑配置

[![](img/d5dce22c0e7e85632213fd2243612b76.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w9pxdhAP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0nw6fsrp32wx3x4mf60w.png)

### 添加‘远程’

[![](img/38b4e2aeac4ef8cabfa730975d7b9b77.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q4Hmr4Cp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4in5xtodlsh7dzxl71hk.png)

### 设置端口为 5006

[![](img/e80596ae0e5a4ee2c7fe661a361132b2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CGYorVv5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7vkervncynj5pupwnu3u.png)

如果您回头看第 3 步，我们将端口设置为 5006

org . jetbrains . kotlin . native . JVM args =-agent lib:jdwp = transport = dt _ socket，server=y，suspend=y，address=5006
确保在您的远程配置中进行了设置。

# 第五步:盈利

现在回到您的测试项目并构建它。我通常在命令行上用。/gradlew build。您将收到一条消息，告诉您附加一个调试器。

[![](img/89838b5fd880e6d50ab35b0b2b65c9f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4PhS5_eT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4s2q18g9fuxosxp90xfm.png)

回到 Kotlin Native Intellij 项目并运行远程配置。它会连接并让编译器运行。

在我的测试中，我实际上需要调试一个不同的任务，所以我需要连接调试器两次。我会在示例项目中看到这一点…

[![](img/6da81f3cda81e9d9386779e7bf4aeafd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JoNPr02Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zr8ezwvgeuxa5anlrra.png)

所以，虽然有点笨重，只是再次附上。

另外，不要忘记在编译器代码中添加断点，这才是重点。

在视频中一起运行测试应用和调试器(抱歉是音频。如果有兴趣，我会用真正的麦克风重做)

[调试视频](https://www.youtube.com/watch?v=6W00Ngx0ESw)

* * *

👎在 TDD 领域有一些反调试器的情绪，但是调试器是不好的实践的想法完全忽略了发现用例，这是巨大的。这是一个极好的例子。我真的很反感“真正的开发者不用调试器”这种态度。只是在这里咆哮一下。无论如何…