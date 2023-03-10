# 从 Gradle 运行节点脚本或吞咽/咕哝任务

> 原文：<https://dev.to/cjbrooks12/run-node-scripts-or-gulpgrunt-tasks-from-gradle-3mod>

我最近在开始使用 Kotlin/JS 时偶然发现了这个 Gradle 插件。它将下载 Node 作为您构建的一部分(因此它甚至不需要安装在您的 CI 服务器上)，让您运行任意的 Node 脚本，并设置 Gradle 任务来镜像您的 Gulp 或 Grunt 任务。

我还没怎么用过它，但它为全栈 Gradle 项目无缝构建的潜力是巨大的。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)SRS/[度节点插件](https://github.com/srs/gradle-node-plugin)

### Gradle 插件，用于在您的构建中集成 NodeJS。🚀

<article class="markdown-body entry-content container-lg" itemprop="text">

# 节点的 Gradle 插件

[![Build Status](img/53288176585d3a10614a543bbe74552d.png)](https://travis-ci.org/srs/gradle-node-plugin)[![Windows Build status](img/427034287b51b9043b88b86a67ab60a2.png)](https://ci.appveyor.com/project/srs/gradle-node-plugin)[![License](img/eae524541cdbc6fc3b7c526cf777d506.png)](http://www.apache.org/licenses/LICENSE-2.0.html)[![Version](img/fca0dae5024fdb8a6b8d2d80efef67c4.png)T11】](https://camo.githubusercontent.com/eed3b94633bf75835bba7b1c835ddcef0aee64f3a670ad9c3ec3cf0425e3dd7f/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f56657273696f6e2d312e332e312d6f72616e67652e737667)

这个插件使你能够使用许多基于 [NodeJS](https://nodejs.org) 的技术作为你构建的一部分，而不需要在你的系统上本地安装 NodeJS。它将以下基于 NodeJS 的系统与 Gradle 集成在一起:

*   [节点 j](https://nodejs.org)
*   [纱线](https://yarnpkg.com/)
*   [咕噜声](https://gruntjs.com/)
*   [一饮而尽](https://gulpjs.com/)

它实际上是 3 个插件合二为一:

*   [节点插件](https://plugins.gradle.org/plugin/com.moowork.node) ( `com.moowork.node` ) - [见文档](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/node.md)。
*   [Grunt 插件](https://plugins.gradle.org/plugin/com.moowork.grunt) ( `com.moowork.grunt` ) - [见文档](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/grunt.md)
*   [大口插件](https://plugins.gradle.org/plugin/com.moowork.gulp) ( `com.moowork.gulp` ) - [见文档](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/gulp.md)

## 证明文件

这是你开始使用这个插件的方法。如果您没有找到您想要的内容，请在 [GitHub 问题](https://github.com/srs/gradle-node-plugin/issues)中添加一个问题。

*   [安装](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/installing.md)
*   [Node Plugin](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/node.md)
*   [咕哝插件](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/grunt.md)
*   [Gulp 插件](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/gulp.md)
*   [常见问题解答](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/docs/faq.md)
*   [变更日志](https://raw.githubusercontent.com/srs/gradle-node-plugin/master/CHANGELOG.md)

## 旧版本的文档

以下是该插件旧版本的文档:

*   [1.2.0](https://github.com/srs/gradle-node-plugin/blob/v1.2.0/README.md)
*   [1.1.1](https://github.com/srs/gradle-node-plugin/blob/v1.1.1/README.md)
*   [1.1.0](https://github.com/srs/gradle-node-plugin/blob/v1.1.0/README.md)
*   [1.0.1](https://github.com/srs/gradle-node-plugin/blob/v1.0.1/README.md)
*   [1.0.0](https://github.com/srs/gradle-node-plugin/blob/v1.0.0/README.md)
*   [0.14](https://github.com/srs/gradle-node-plugin/blob/v0.14/README.md)
*   [0.13](https://github.com/srs/gradle-node-plugin/blob/v0.13/README.md)
*   [0.12](https://github.com/srs/gradle-node-plugin/blob/v0.12/README.md)
*   [0.11](https://github.com/srs/gradle-node-plugin/blob/v0.11/README.md)
*   [0.10](https://github.com/srs/gradle-node-plugin/blob/v0.10/README.md)

## 构建插件

要构建插件，只需键入以下命令:

```
./gradlew clean build
```

Enter fullscreen mode Exit fullscreen mode

## 贡献的

贡献总是…

</article>

[View on GitHub](https://github.com/srs/gradle-node-plugin)