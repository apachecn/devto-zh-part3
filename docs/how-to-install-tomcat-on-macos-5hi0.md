# 如何在 MacOS 上安装 Tomcat

> 原文：<https://dev.to/starlightslo/how-to-install-tomcat-on-macos-5hi0>

这是我的第一个帖子，这个帖子不是很特别，你也许可以在其他网站上找到同样的教程，它只是给可能和我有同样问题的人的一个提示。

Tomcat 网站提供了不同的 Windows 版本，但是没有 MacOS 版本，也没有 MacOS 安装版本。所以，这篇文章将教你如何安装 Tomcat 并在 MacOS 上运行它。

## 下载 MacOS 版 Tomcat

对于 MacOS，你必须下载**内核【tar.gz】T2**并解压。(你可以选择你想要的版本)

## 创建 Tomcat 文件夹

我建议在您当前的用户下创建，不要放入系统文件夹中，因为您可能会在开发时频繁地将代码部署到 Tomcat 文件夹中。因此，它将防止您总是需要根权限来更改文件。

```
mkdir -p /User/{current user}/Library/Tomcat 
```

## 将 Tomcat 移动到那个文件夹中

在你解压 Tomcat 之后，你会得到一个文件夹，以我为例，我用的是 7.0.92 版本，所以，文件夹名是 **apache-tomcat-7.0.92** 。

```
mv ./apache-tomcat-7.0.92 /User/{current user}/Library/Tomcat/ 
```

## 使所有脚本可执行

我们稍后将执行一些脚本文件，例如:启动或停止脚本，我们需要使它们可执行。

```
chmod +x /User/tony/Library/Tomcat/apache-tomcat-7.0.92/bin/*.sh 
```

## 启动 Tomcat

在您使所有脚本可执行之后，现在您可以使用:
来启动您的 Tomcat

```
/User/tony/Library/Tomcat/apache-tomcat-7.0.92/bin/startup.sh 
```

现在你应该可以访问 [http://localhost:8080/](http://localhost:8080/) 。

## 停止 Tomcat

如果你想停止你的 Tomcat，可以使用:

```
/User/tony/Library/Tomcat/apache-tomcat-7.0.92/bin/shutdown.sh 
```

就这些了，希望这个帖子能对以后的人有所帮助。