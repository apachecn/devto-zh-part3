# 在 JCenter 上上传 Android 库

> 原文：<https://dev.to/tsalikispk/uploading-an-android-library-on-jcenter-2ddf>

面对相当长一段时间缺乏灵感，我决定从我的一个老项目中汲取一些灵感。几年前，我创建了一个名为 [`target-layout`](https://github.com/tsalik/target-layout) 的开源库，它基本上是一个`FrameLayout`，在其中心绘制一个视图，在其周围绘制一些`drawables`，让用户放大视图并通过一系列级别进行选择。

[![TargetLayout animation](img/e851ec0b98b19a3f686da2ddf955026e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dnbbU7yX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://www.droidship.com/gifs/targetlayout.gif)

当然，GIF 可能是展示图书馆工作的最佳方式。

除了创建定制和独特的 UI 以及深入研究如何测量、布局和绘制视图的乐趣之外，我决定学习如何在 JCenter 上发布库。不幸的是，我并不是世界上最好的维护者，所以我离开了针对旧 Android SDK 版本的库，没有对 AndroidX 的任何支持。此外，我已经离开了没有持续集成的项目(CI 从现在开始),随着时间的推移，我忘记了在 JCenter 上发布是如何工作的，所以我决定在对库本身做任何进一步的工作之前重新审视这些主题。

# 在您的开源库上添加 CI

我要做的第一件事就是更新它所有过时的依赖项。这意味着更新 gradle 版本以及 target 和编译 Android SDK 版本，这样库的任何新用户都可以编译它，而不会出现问题和警告。尽管我是维护者，可以直接推送到主分支，但我决定自己去做一个拉请求过程。这样做意味着我将模拟其他人试图使用和修改这个库的过程，所以我需要从外部 CI 服务器添加一些检查，以便验证这个库是否真正构建了。

最常见的支持开源项目并且对 Android 友好的 CI 服务有 [CircleCI](https://circleci.com/) 和 [TravisCI](https://travis-ci.org/) 。由于我以前在一个玩具项目上与 TravisCI 有过一些经验，我决定使用 TravisCI 来启动使图书馆现代化的进程。才能开始使用 TravisCI。首先，需要创建一个帐户或者用你的 GitHub 帐户登录。这样做之后，我们可以从仪表板添加一个 git 存储库，然后在 repo 的根上添加一个`.travis.yml`文件。`.travis.yml`是必不可少的，因为它指导 TravisCI 如何构建项目——如果 TravisCI 在回购的根上找不到`.travis.yml`,它将无法构建。下面你可以找到一个构建 Android 项目的示例`.travis.yml`:

```
language: android
jdk: oraclejdk8

before_cache:
  - rm -f $HOME/.gradle/caches/modules-2/modules-2.lock
  - rm -fr $HOME/.gradle/caches/*/plugin-resolution/

cache:
  directories:
    - $HOME/.gradle/caches/
    - $HOME/.gradle/wrapper/

env:
  global:
    - ANDROID_API=28
    - ANDROID_BUILD_TOOLS=28.0.3

android:
  components:
    - tools
    - platform-tools
    - build-tools-$ANDROID_BUILD_TOOLS
    - android-$ANDROID_API
    - extra-google-m2repository
    - extra-android-m2repository # for design library

licenses:
  - android-sdk-preview-license-.+
  - android-sdk-license-.+
  - google-gdk-license-.+

before_install:
  - mkdir "$ANDROID_HOME/licenses" || true
  - echo -e "\n8933bad161af4178b1185d1a37fbf41ea5269c55" > "$ANDROID_HOME/licenses/android-sdk-license"
  - echo -e "\n84831b9409646a918e30573bab4c9c91346d8abd" > "$ANDROID_HOME/licenses/android-sdk-preview-license"
  - yes | $ANDROID_HOME/tools/bin/sdkmanager "build-tools;24.0.3"
  - chmod +x gradlew

script:
  - "./gradlew :library_module_name:clean :library_module_name:build" 
```

Enter fullscreen mode Exit fullscreen mode

## 讲解. travis.yml

上面的 yml 指示 CI 服务在构建和导出期望的 Android API 和构建工具版本作为环境变量之前清除缓存。然后，我们声明将使用哪些组件，以及它们的许可证。在我们的日常工作中，这是通过 Android Studio 手动完成的，但是因为我们是在 CI 服务器上构建的，所以我们需要通过编程来完成。通过在`.travis.yml`上这样声明它们，我们指示 TravisCI 下载想要的组件并接受任何需要的许可证。由于接受许可存在一些问题，我们额外指示通过`sdkmanager`工具接受许可，并最终使`gradlew`文件可执行，以便让 TravisCI 执行构建项目的 gradle 脚本。最后，`script`部分是构建实际发生的地方。因为我们只对构建库模块感兴趣，而不是附带的示例，所以我们只清理和构建库模块(在脚本上，名称示例为 library_module_name)

## 有些痛点

我们必须确保我们已经在 git 目录的根目录下添加了`.travis.yml`,否则 TravisCI 将无法构建。另一个棘手的问题是许可证。通过`sdkmanager`接受它们解决了任何持续存在的问题。不幸的是，我没有找到在 TravisCI 上访问 lint html 报告的方法，所以我最终通过库模块的`build.gradle`在控制台上以文本形式显示任何报告。

```
android {
    compileSdkVersion 28
    buildToolsVersion "28.0.3"

    lintOptions {
        textReport true
        abortOnError true
        warningsAsErrors false
    }
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 更新 gradle 版本，在 AndroidX 上移动

在这一点上，我们有一个通过 TravisCI 的构建，我们准备更新 gradle 和 Android SDK 版本。幸运的是，这很容易通过 Android Studio 完成，因为它已经对库的`build.gradle`文件给出了警告。只需在各自的版本上点击`Alt+Enter`，自动更新它们，AndroidX 的迁移工具工作正常，因为该项目除了 Android `appcompat`库之外没有任何依赖关系——所以我不必处理 AndroidX 迁移的任何[问题。现在我们准备创建拉请求，看到它通过 TravisCI 构建并安全地接受它。更重要的是，我们可以在我们的`README.md`中添加 TravisCI 构建徽章，以显示构建正在通过。](https://www.droidship.com/posts/androidx)

# 上传到 JCenter

现在构建通过了，我们终于准备好将库的工件上传到 JCenter 上，这样每个人都可以将它作为一个依赖项添加到他们的构建中。但是在上传任何东西之前，首先我们需要知道实际上传什么文件！

## 美芬库

为了使我们的库可追踪和可下载，我们需要将它上传到 maven 存储库。关于什么是 maven 资源库以及它是如何工作的，可以在[这里找到](https://blog.packagecloud.io/eng/2017/03/09/how-does-a-maven-repository-work/#what-is-a-maven-dependency)。简而言之，maven 资源库是一个目录，其中存储了库的所有可执行文件，并可供下载。目录的路径是由库的`.pom`文件决定的，它实际上也给了库它的名字，并添加了它所拥有的任何可传递的依赖项。作为本文示例的`target-layout`库的`.pom`文件如下:

```
<?xml version="1.0" encoding="UTF-8"?>
<project  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.tsalik.targetlayout</groupId>
  <artifactId>targetlayout</artifactId>
  <version>1.0.2</version>
  <packaging>aar</packaging>
  <dependencies>
    <dependency>
      <groupId>androidx.appcompat</groupId>
      <artifactId>appcompat</artifactId>
      <version>1.0.2</version>
      <scope>runtime</scope>
    </dependency>
  </dependencies>
</project> 
```

Enter fullscreen mode Exit fullscreen mode

上面的意思是，如果有人想用 gradle 从 maven 资源库中获取库，他们必须在他们的`build.gradle`文件的依赖项中添加下面一行:

```
implementation "com.tsalik.targetlayout:targetlayout:1.0.2" 
```

Enter fullscreen mode Exit fullscreen mode

因为这是一个 Android 库，所以声明应该下载一个`.aar`(更具体地说，命名为 targetlayout-1.0.2.aar)文件，以及该库的依赖项，即`androidx.appcompat:appcompat:1.0.2`。

现在我们知道我们需要从库的代码中产生`.aar`,并将它与一个`.pom`文件一起添加，包括任何额外的资源或 maven 存储库上的 javadoc，所以接下来的步骤应该是构建库并在特定的 maven 存储库上添加任何上述工件(在我们的例子中，我们希望最终将它上传到 JCenter 存储库)

## 让我们在本地构建和发布

首先，我们需要在本地存储库中创建上面提到的工件。要做到这一点，我们需要知道如何通过 gradle 发布一个 maven 知识库，这是在 Android 上构建的事实上的工具(尽管像 [Bazel](https://bazel.build/) 和 [Buck](https://buck.build/) 这样的替代品确实存在)。关于 maven publishing 如何工作的大量文档可以在[这里](https://docs.gradle.org/current/userguide/publishing_maven.html)找到(在撰写 gradle 5 . 4 . 1 版本时)。虽然官方 gradle 文档描述了发布是如何工作的，但是 bintray 插件文档(撰写本文时的版本 1.8.4)更具体地说明了如何在 Android 中发布并避免常见的陷阱。

下面你可以找到发布`target-layout`库的 gradle 脚本示例:

```
apply plugin: 'maven-publish'

task androidJavadocs(type: Javadoc) {
    failOnError = false
    source = android.sourceSets.main.java.srcDirs
    ext.androidJar = "${android.sdkDirectory}/platforms/${android.compileSdkVersion}/android.jar"
    classpath += files(ext.androidJar)
    exclude ' **/R.html', '** /R.*.html', '**/index.html'
}

task androidJavadocsJar(type: Jar, dependsOn: androidJavadocs) {
    classifier = 'javadoc'
    from androidJavadocs.destinationDir
}

task androidSourcesJar(type: Jar) {
    classifier = 'sources'
    from android.sourceSets.main.java.srcDirs
}

project.afterEvaluate {
    publishing {
        publications {
            targetlayout(MavenPublication) {
                groupId project.ext.PUBLISH_GROUP_ID
                artifactId project.ext.PUBLISH_ARTIFACT_ID
                version project.ext.PUBLISH_VERSION

                artifact bundleReleaseAar
                artifact androidJavadocsJar
                artifact androidSourcesJar

                pom.withXml {
                    final dependenciesNode = asNode().appendNode('dependencies')

                    ext.addDependency = { Dependency dep, String scope ->
                        if (dep.group == null || dep.version == null || dep.name == null || dep.name == "unspecified")
                            return // ignore invalid dependencies

                        final dependencyNode = dependenciesNode.appendNode('dependency')
                        dependencyNode.appendNode('groupId', dep.group)
                        dependencyNode.appendNode('artifactId', dep.name)
                        dependencyNode.appendNode('version', dep.version)
                        dependencyNode.appendNode('scope', scope)

                        if (!dep.transitive) {
                            // If this dependency is transitive, we should force exclude all its dependencies them from the POM
                            final exclusionNode = dependencyNode.appendNode('exclusions').appendNode('exclusion')
                            exclusionNode.appendNode('groupId', '*')
                            exclusionNode.appendNode('artifactId', '*')
                        } else if (!dep.properties.excludeRules.empty) {
                            // Otherwise add specified exclude rules
                            final exclusionNode = dependencyNode.appendNode('exclusions').appendNode('exclusion')
                            dep.properties.excludeRules.each { ExcludeRule rule ->
                                exclusionNode.appendNode('groupId', rule.group ?: '*')
                                exclusionNode.appendNode('artifactId', rule.module ?: '*')
                            }
                        }
                    }

                    // List all "compile" dependencies (for old Gradle)
                    configurations.compile.getDependencies().each { dep -> addDependency(dep, "compile") }
                    // List all "api" dependencies (for new Gradle) as "compile" dependencies
                    configurations.api.getDependencies().each { dep -> addDependency(dep, "compile") }
                    // List all "implementation" dependencies (for new Gradle) as "runtime" dependencies
                    configurations.implementation.getDependencies().each { dep -> addDependency(dep, "runtime") }
                }
            }
        }

        repositories {
            maven {
                // change to point to your repo, e.g. http://my.org/repo
                url = "$buildDir/repo"
            }
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

虽然看起来让人望而生畏，但还是试着解剖一下吧。首先，我们声明自己的任务，用于在各自的 jar 上生成 Javadoc 和 Android 资源。`publishing`和`publication`闭包来自`maven-publish`插件，我们必须将它应用到脚本之上。在`publication`闭包内，我们声明了一个名为`targetlayout`的 maven 发布——以库命名(对于您自己的库，您必须申请一个合适的名称)。

继续在`publication`闭包内，我们应用`groupdId`、`artifactId`和`version`来适当地命名库。上例中的值被设置为库模块的`build.gradle`文件中的额外项目属性，这样我们就可以在一个单独的点上引用它们。然后我们声明我们需要出版物拥有哪些工件(即文件)。我们需要发布版本的`.aar`，以及 Javadoc 和 jar 形式的任何额外资源(我们通过调用上面声明的任务来实现)。

最后，我们为`compile`、`api`和`implementation`配置手动添加库的所有依赖项，否则不会自动完成(bintray-gradle-plugin 很好地解释了为什么)。

为了检查发布的所有配置是否有效，我们可以添加`repositories`闭包并在本地目录上发布(在本例中，在库模块的构建文件夹下的目录名为 repo)。现在为了测试一切正常，我们可以执行:

```
./gradlew clean :targetlayout:publish 
```

Enter fullscreen mode Exit fullscreen mode

我们应该能够检查到所有的工件(aar、javadoc、pom 文件)都已经在构建文件夹中发布，并且 pom 拥有正确的`groupId`、`artifactId`和`version`。

## 那么我们应该上传到哪里呢？

现在我们已经看到了出版物是如何工作的，以及它包含哪些文件，我们已经准备好最终将库上传到 maven 存储库中，而不是我们创建的本地存储库中。在现有的各种公共 maven 资源库中，用于 Android 开发的更受欢迎的是 Maven Central、JCenter 和 JitPack。对它们进行比较超出了本文的范围，因为我已经在 JCenter 上上传了这个库，所以我们将只关注上传到那里。幸运的是，JCenter 上已经有一个 gradle 插件可以上传，所以没有必要重新发明轮子。

为了安装 bintray gradle 插件，我们将`classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.4'`应用于项目顶层`build.gradle`的依赖项，然后将其应用于上面声明的相同脚本(负责发布)。

```
apply plugin: 'maven-publish'
apply plugin: 'com.jfrog.bintray'

...
project.afterEvaluate {
    publishing {
        publications {
            targetlayout(MavenPublication) {
                ...
            }
        }
    }

    bintray {
            Properties localProperties = new Properties()
            if (project.rootProject.file('local.properties').exists()) {
                localProperties.load(project.rootProject.file('local.properties').newDataInputStream())
            }
            user = localProperties.getProperty("bintray.user")
            key = localProperties.getProperty("bintray.apikey")
            pkg {
                repo = 'target-layout'
                name = 'target-layout'
                licenses = ['MIT']
                vcsUrl = 'https://github.com/tsalik/target-layout.git'
                version {
                    name = "1.0.2"
                    released = new Date()
                }
            }
            publications = ['targetlayout']
        }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有几件事需要注意。首先，我们必须已经登录 JCenter 并创建了一个 maven 存储库。我将省去这一部分，并提供如何做的参考。您可以检查存储库和库的名称是否具有相同的名称- `target-layout`。这不是强制性的，它只是在我创建存储库的时候发生的。

为了能够上传发布的工件，我们必须以某种方式用 bintray 授权我们自己。`user`和`key`字段就是这么做的——但是要注意。我们**不能**在版本控制上添加这些值，因为每个人都可以检查这些值并上传他们想要的任何东西到你的 maven 库。如果我们通过 CI 服务器发布和上传，我们可以将它们添加为环境变量，或者在`local.properties`上添加它们(其中**必须在`.gitignore`上)。**

如果您决定通过`local.properties`注入它们，确保您检查文件是否存在——在 CI 服务器中它不存在，构建将失败。

请注意，虽然我已经将版本声明为外部属性，但我没有引用它，而是对它进行了硬编码。我确信在未来，我会对过去的自己感到沮丧，除非我尽快解决它😅。

最后，在添加任何必需的元数据之后，我们声明我们想要上传哪些出版物。这个值必须与我们在 publishing 部分声明的名称相同。最后，我们准备从命令行发布这个库:

```
./gradlew clean :targetlayout:bintrayUpload 
```

Enter fullscreen mode Exit fullscreen mode

# 最后的想法

最终，重新审视如何在开源项目中添加 CI 并自动化其发布过程被证明是一个很好的练习。除了在`travis.yml`设置 CI 时由于错误配置而导致的一些(很多)坏版本的挫折之外，这是一次非常愉快的经历。必须处理一个出版物如何工作，澄清了许多关于 maven 如何工作的事情，并强调了创建一个带有工件的出版物并将其上传到一个公共 maven repo 实际上是两件不同且部分不相关的事情。

仅仅在本地发布就足够了，因为我可以像过去一样手动上传工件，但是这一次，我没有依赖其他人的现成解决方案，而是决定在没有第三方插件帮助的情况下实际检查一下如何做到这一点。毕竟，插件越少，构建一个库或应用程序的痛苦就越小。bintray 插件确实是无价的，因为它简化了发布库的上传部分。

# 下一步

现在库已经更新了最新的 gradle 和 Android SDK 版本，我可以更彻底地研究一些 lint 检查，并可能考虑在布局第一次附加到窗口时添加一些动画。令人惊讶的是，曾经处于被否决边缘的东西现在有了新的火花，并给了人们调查各种事情的灵感。未来工作的另一步是添加 gpg 签名，并在 Maven Central 上推送库。

# 参考文献

虽然有很多关于该出版物的帖子，详细介绍了如何设置 TravisCI 帐户以及如何在 JCenter 上创建帐户和存储库，但我决定更深入地介绍出版物如何在 maven 存储库上工作。

更重要的是，大多数帖子在 gradle 的`maven-plugin`之上使用了其他 gradle 插件用于发布部分，由于练习的范围是检查它如何以最小的依赖性工作，我决定只依赖 gradle 上的普通插件。

来自 [Anitaa Murthy](https://medium.com/@anitaa_1990/6-easy-steps-to-upload-your-android-library-to-bintray-jcenter-59e6030c8890) 、 [Yegor Zatsepin](https://medium.com/@yegor_zatsepin/simple-way-to-publish-your-android-library-to-jcenter-d1e145bacf13) 和 [Wajahat Karim](https://android.jlelse.eu/publishing-your-android-kotlin-or-java-library-to-jcenter-from-android-studio-1b24977fe450) 的帖子作为这一篇的指南，提供了如何在 JCenter 上设置存储库的优秀复制步骤。

*特别感谢 Michalis Kolozoff 的校对*