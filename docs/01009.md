# 如何安装和管理多个版本的 Java(提示:jabba 和 jEnv)

> 原文：<https://dev.to/mjclemente/how-to-install-and-manage-multiple-versions-of-java-hint-jabba-and-jenv-2ho0>

我未来几个月的目标之一是提高我的 Java 熟练程度；也就是说，把一些真正的知识放在我目前东拼西凑的理解后面，这些知识是多年来通过反复试验拼凑起来的。

在深入 Udemy、Youtube 等网站上的 Java 培训课程之前，我想确保我有办法在我的机器上使用不同版本的 JDK。这至少部分是由于甲骨文对他们的 Java 许可和发布周期所做的改变。现在有各种各样的 Java 版本和供应商发行版可用，我想找到一种方法来轻松地在不同版本的 JDK 之间切换，而不需要实际更新我的系统的 Java 版本或每次手动设置`JAVA_HOME`。

基本上，同样的方式，我可以使用不同版本的 Ruby、Node 和 CFML，分别感谢 [rbenv](https://github.com/rbenv/rbenv) 、 [nvm](http://nvm.sh) 和 [CommandBox](https://commandbox.ortusbooks.com/) ，看起来我应该能够轻松地使用不同版本的 Java，而不会被系统的默认版本所束缚。因此，像一个优秀的开发人员一样，我做了一些谷歌搜索，偶然发现了[这篇有用的 StackOverflow 帖子](https://stackoverflow.com/questions/52524112/how-do-i-install-java-on-mac-osx-allowing-version-switching)，并最终决定同时使用 **[jabba](https://github.com/shyiko/jabba)** 和**[jEnv](http://www.jenv.be/)**——前者安装版本，后者基于每个目录配置它们。它是这样工作的:

## 贾巴安装不同的 JDK 版本

**jabba** 是一个跨平台的 Java 版本管理器。我发现它在简化安装不同版本 Java 的过程方面非常出色。你可以在 [jabba Github repo](https://github.com/shyiko/jabba#installation) 上找到安装说明以及一些非常有用的文档。

安装后，您可以通过`jabba ls-remote` :
列出 JDK 的可用版本

```
$ jabba ls-remote
1.12.0
1.12.0-1
1.6.65
adopt@1.12.33-0
...
zulu@1.7.95 
```

现在，有超过 100 个结果，从 A 到 Z(即采用 OpenJDK 到 Zulu OpenJDK)。

要安装一个特定的版本，比如 Corretto，可以运行:

```
$ jabba install amazon-corretto@1.11.0-3.7.1
#installed to ~/.jabba/jdk/amazon-corretto@1.11.0-3.7.1/ 
```

在 Linux/Mac 上，JDK 版本被下载/安装到`~/.jabba`。记住这一点，因为在使用 jEnv 时，你需要知道它们在哪里。

不容易获得的 JDK 版本，如 Oracle 的 11.0.3，现在需要一个帐户才能下载，可以通过 URL 或文件位置手动安装。下面是我如何安装甲骨文的，下载后:

```
$ jabba install oracle@1.11.0-3=tgz+file:///Users/MYUSER/Downloads/jdk-11.0.3_osx-x64_bin.tar.gz 
```

### 在 Mac 上使用 jabba 和 Intellij

当我试图将 jabba 安装的 JDK 作为 SDK 添加到 Intellij 时，我遇到了一个小问题——因为它们位于一个隐藏的文件夹中，不容易通过文件浏览器选择。不出所料，我不是第一个遇到这个问题的人，[一篇 r/java 的 Reddit 帖子](https://www.reddit.com/r/java/comments/a1rfvx/jabba_intellij_on_mac/)给了我两个答案:

*   在 Intellij 中，当浏览`~/.jabba`中的 JDK 时，您可以键入**command-shift-period**(⌘+⇧+.)，这将显示隐藏的文件/文件夹，使他们可以选择。显然这并不适用于所有 Intellij 版本，所以有另一个选择。
*   在常规 Finder 中，导航到文件夹`~/.jabba/jdk/JDK@VERSION/Contents/Home` -然后你应该能够将它拖到 Intellij Finder 窗口中，以便使用/选择它。

## jEnv 为每个目录分配 JDK

安装好 JDK 版本后，我的下一个目标是让管理和使用它们变得简单明了。一个主要的问题是将 JDK 版本分配给一个项目/目录的能力，然后不必再考虑它。对我来说， **jEnv** 搞定了。

那么，什么是 [jEnv](http://www.jenv.be/) ？从它的网站上摘抄一段话:

> jEnv 是一个命令行工具，帮助您忘记如何设置 JAVA_HOME 环境变量

因此，虽然 jEnv 没有安装不同版本的 Java，但它使管理您机器上已有的版本变得容易得多(就像 jabba 安装的那些版本)。只需注意，不幸的是，对于 Windows 用户来说，它目前还不能跨平台。

安装 jEnv 后，我需要添加来自 jabba(和我的机器)的 JDK。这是通过使用`jenv add`命令并提供 JDK 版本的路径:
来完成的

```
# Add a jabba JDK
$ jenv add /Users/MYUSER/.jabba/jdk/oracle@1.11.0-3/Contents/Home/
oracle64-11.0.3 added
11.0.3 added
11.0 added

# Add the system JDK
$ jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_181.jdk/Contents/Home/
oracle64-1.8.0.181 added
1.8.0.181 added
1.8 added

# List JDK versions
$ jenv versions 
```

现在——这是我最兴奋的地方——我可以为一个目录设置一个 Java 版本，然后我就不需要再考虑它了。它是这样工作的:

```
$ java -version
java version "1.8.0_181"

$ jenv local oracle64-11.0.3

$ java -version
java version "11.0.3" 2019-04-16 LTS 
```

这通过向目录添加一个`.java-version`文件来实现，该文件包含指定的版本。jEnv 自动确保该版本在文件夹(及其子文件夹)中使用。一劳永逸。

## 包装完毕

我最终对这个解决方案非常满意——它使我能够为一个项目做一些初始的 Java 配置，然后忘记它，继续写代码。也就是说，我不是 Java 专家。如果你有更好的方法或者发现这个方法有问题，请告诉我。