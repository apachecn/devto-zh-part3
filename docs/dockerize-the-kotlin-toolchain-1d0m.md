# 记录 Kotlin 工具链

> 原文：<https://dev.to/baens/dockerize-the-kotlin-toolchain-1d0m>

*这篇文章最初是在[blog.baens.net](https://blog.baens.net/posts/dockerize-kotlin-toolchain/)T3 发表在我的博客上的*

我疯了吗？是的，是的，我是。不管出于什么原因，我喜欢这个项目的想法，我可以在安装很少东西的情况下从一台计算机移动到另一台计算机，并且能够在那个代码库中工作。这里，我的意思是我可以安装一个或者两个工具，然后我可以在代码库中高效地工作。现在， [Docker](https://www.docker.com/) 感觉也许我可以实现这个疯狂的梦想。

带着这个疯狂的想法，我决定是否可以完全对接 [Kotlin](https://kotlinlang.org/) 工具链。理论上，我可以通过安装编辑器、git、docker 和 bam 来设置工作站。我现在 100%准备好在这台机器上编码。好吧，拿着这个，我们走。

# 第一步:让基本构建工作起来

现在，为了让事情变得简单，我将重用我的[科特林基地](https://blog.baens.net/posts/step-by-step-kotlin/)，并用这个工具链重新创建这个项目。第一步，让我们用 docker 执行的 gradle 文件替换`gradlew`文件。它看起来会像这样:

```
#!/usr/bin/env sh

docker run --rm -it -v $(pwd):/workspace -w /workspace gradle:5.0.0-jdk11 gradle $@ 
```

这是一个相当直接的`docker`命令，但以防你以前从未使用过`docker`,让我简单介绍一下一些基础知识:

*   我们将运行一个 docker 映像
*   `--rm -it` -完成后移除(`rm`)并添加标准输入管道( [`it`](https://docs.docker.com/engine/reference/commandline/run/#assign-name-and-allocate-pseudo-tty---name--it) )
*   `-v`——在`/workspace`将我们当前的工作空间挂载到容器内的目录中
*   `-w` -使当前工作方向`/workspace`(即开始时的`cd /workspace`)
*   `gradle:5.0.0-jdk11` -将 gradle 5.0.0 与 JDK 11 号图像一起使用(参见 [gradle docker hub](https://hub.docker.com/_/gradle/) 上的标签完整列表)
*   `gradle` -执行 gradle 命令
*   `$@` -将父 shell 中的所有参数传递给该命令

好吧，让我们测试一下，看看会发生什么！

<video width="640" loop=""><source src="https://blog.baens.net/videos/gradle-first-build.mov" type="video/mp4"></video> 

现在，如果你的输出与*不完全相同，不要太惊慌。我已经运行了它，所以 docker 下载过程不会继续，但之后应该会相当接近。*

 *看起来我们有一些罐子，让我们把它们放进一个容器，然后运行它们！

# 第二步:将罐子放入对接器

现在，我有一个`Dockerfile`构建了 jar，然后创建了 docker 映像，我将继续这个想法。下面是`Dockerfile`的样子:

```
FROM gradle:5.0.0-jdk11 as BUILD

RUN mkdir -p /home/gradle/src
WORKDIR /home/gradle/src
COPY . /home/gradle/src
RUN gradle --no-daemon shadowJar

FROM openjdk:11.0.1-jre

COPY --from=BUILD /home/gradle/src/build/libs/step-by-step-kotlin-all.jar /bin/runner/run.jar
WORKDIR /bin/runner

CMD ["java","-jar","run.jar"] 
```

我不打算深入这个文件，因为我已经在另一篇博文中解释过了，但是让我指出第 3 行是特殊的和需要的。容器内部的权限有点不稳定，我必须确保文件夹已经正确创建。这方面有很多工作，但我不想演示。

让我们现在就构建并运行它吧！

<video width="640" loop=""><source src="https://blog.baens.net/videos/docker-build-and-run.mov" type="video/mp4"></video> 

太棒了。它像预期的那样工作。但是它太慢了。让我们尽快解决这个问题。

# 第三步:构建时间改进

好吧，显然这个构建循环有点荒谬。让我向您展示一个针对构建时间的快速改进。如果你仔细观看视频，下载插件和其他工件是花费时间最多的。如果我们只是缓存那些工件，并让 gradle 每次都发现它们，那么这个问题应该很容易解决。gradle 工具直接在你的家中存储这个文件夹，所以我们为什么不创建一个可以在每次 gradle 运行之间存在的文件夹呢？好吧，如果我们想这么做的话，`gradlew`会变成这样:

```
#!/usr/bin/env sh

[ -d .build_cache ] || mkdir .build_cache
docker run --rm -it -v $(pwd):/workspace -v $(pwd)/build_cache:/home/gradle/.gradle -w /workspace gradle:5.0.0-jdk11 gradle --no-daemon $@ 
```

没什么疯狂的。只需创建`build_cache`文件夹，然后将该文件夹挂载到 docker 映像中预期的位置。我的快速测试显示，我们从 30 秒到 8 秒构建。还不错。

# 第四步:.....利润？？....评估这是否可行

好了，现在我们已经有了这样的工作并正在生产构建，这真的可行吗？嗯，老实说，很接近了。它工作，你不需要安装很多东西。通过一些调整，你可以进一步提高速度。我会把这个设置作为我自己的设置来使用(我现在在一些项目中使用了类似的设置),但是我可能不会把它推给其他开发人员使用，除非他们愿意。但我开始喜欢这种设置了。同样，这种方法有许多小问题，但它非常接近。

因此，这至少是一次有趣的冒险，看看这是否可能，我认为它展示了事情可能走向的前景。

# 特别说明

如果您签出代码，您可能会注意到一个`.dockerignore`文件。这个文件告诉 docker 在将文件转移到 docker 上下文时不要包含什么。这是非常重要的，因为`.gradle`文件夹不能在机器间很好地共享，并且东西开始损坏。*