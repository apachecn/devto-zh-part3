# 真正集成的开发环境

> 原文：<https://dev.to/stereobooster/truly-integrated-development-environment-1ogg>

当人们说 IDE 时，大多数时候他们指的只是美化了的文本编辑器，有时是调试器，有时是自动完成。在我看来，文本编辑器还不足以成为一个开发环境。我们还需要:

*   编译程序(或解释程序，或翻译程序等)
*   自动化工具(`make`或类似工具)
*   一个包管理器(`bundler`、`yarn`、`cargo`或类似的)
*   大概是一个版本切换器(`rbenv`、`nvm`、`rustup`或者类似的)
*   一个[语言服务器](https://langserver.org/)，用于更智能的自动完成功能(`solargraph`、`typescript`、`flow`、`gocode`或类似功能)
*   棉绒或类型检查器(`rubocop`、`eslint`或类似)
*   格式化程序(`prettier`、`gofmt`或类似)
*   可能是一个 shell ( `bash`、`zsh`或类似的)和 git

## 解

最近我发现了一个真正集成开发环境的潜在解决方案:Docker +“云 IDE”(浏览器内 IDE)。我们可以将所有工具打包在 Docker 容器和一些“云”IDE 中，并配置所有工具一起工作，然后您将使用一个命令来运行 Docker 容器，浏览器中将提供完全可用的编辑器。

我不是第一个想出这个主意的人，所以有一些选择:

[忒伊亚](https://www.theia-ide.org/) :

```
docker run -it -p 3000:3000 -v "$(pwd):/home/project:cached" theiaide/theia 
```

Enter fullscreen mode Exit fullscreen mode

[编码员](https://coder.com/) :

```
docker run -it -p 127.0.0.1:8443:8443 -v "${PWD}:/home/coder/project" codercom/code-server --allow-http --no-auth 
```

Enter fullscreen mode Exit fullscreen mode

[月蚀车](https://www.eclipse.org/che/docs/che-6/quick-start.html) :

```
docker run -ti -v /var/run/docker.sock:/var/run/docker.sock -v /local/path:/data eclipse/che start 
```

Enter fullscreen mode Exit fullscreen mode

你对 IDE 有什么期望？它应该提供哪些现成的功能？

> 由 Unsplash 上的制造商 NESA 拍摄的照片