# 减少 Android 开发 DevOps 的 Gradle 和 Kotlin 的内存使用

> 原文：<https://dev.to/mrpowerscripts/reduce-memory-usage-of-gradle-and-kotlin-for-android-development-devops-1afm>

当你在本地构建你的 android 项目时，你的机器可能有很大的马力。当您在 DevOps 工作流(如持续集成和交付)中运行时，您可能没有那么多资源。这可能会导致您的构件耗尽内存。这里有一些快速提示，可以帮助你在 Android 开发中减少 Gradle 和 Kotlin 版本的内存使用。

## 告诉 Java 放松内存使用

由于 android 构建工具是基于 Java 的，而 Java 是内存猪，你可以先告诉 Java 限制它的内存使用。你可能会看到人们将这些相同的选项放在一个`GRADLE_OPTS`变量中，但是如果流程被分叉，这些选项不会总是被传递到分叉的流程中。最好用 Java 设置一个环境变量 consumed direction。`_JAVA_OPTIONS`基于 Oracle 的 Java 环境广泛支持环境变量。结合`-Xmx`配置，您可以设置 Java 使用的最大堆大小，这是它将类对象之类的东西存储到内存中的地方。将这个值设置为可用内存的 75%会给 Java 足够的空间来存储它需要的东西，而不会阻塞其余的系统资源。如果您有 4GB 的可用空间，那么尝试将其设置为 3GB。`_JAVA_OPTIONS=-Xmx3g`。

## 关闭守护进程

这是给 Gradle 和 Kotlin 的提示，因为他们都有自己的守护进程。守护程序在后台运行，通过存储以前编译时生成的项目信息，帮助加快重新编译的速度。通常，当在 CI 工作流中运行时，您不会重新编译任何东西，因为每次都会重置环境。在这种情况下，守护进程将占用额外的内存，而不会带来任何好处。

使用这些方法来控制 Android DevOps 生命周期中的内存使用，可以减少资源有限的环境中的内存不足故障。